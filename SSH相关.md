## SSH相关

### known_hosts文件与authorized_keys文件的区别

内容引用自以下文章

https://security.stackexchange.com/questions/20706/what-is-the-difference-between-authorized-keys-and-known-hosts-file-for-ssh

#### 一句话摘要

known_hosts文件用于client认证server, 确认要连接的server不是假冒者。

authrized_keys文件用于server认证client.



#### Client认证Server

SSH连接过程，首先由Server发送自己的公钥给Client, 证明自己拥有与该公钥匹配的私钥。如果此过程成功，Client能够明确Server是它所宣称的那个实体。

但是这个实体是否是Client要连接的，则要通过另外一个机制来明确，即在Client的~/.ssh/known_hosts文件或者Client本地系统全局文件/etc/ssh/known_hosts文件中记录有该Server的信息（包含IP, 算法，公钥）。只有在该文件中有记录的才是能够连接的。用户需要事先通过某种外部机制（例如与Server方管理员确认）来确认该Server的信息并手动将其加入known_hosts文件中，（如同将已知熟人的电话加入通讯簿）；或者当第一次连接Server时，先通过外部机制来确认，再将该Server发送过来的信息加入known_hosts文件中。

known_hosts可以包含任意被SSH所支持的公钥类型，如RSA，DSA，ECDSA

必须保证对Server的认证发生在Client发送任何机密信息之前，尤其是用户认证涉及密码时，必须先进行对Server的认证，再发送Client自己的password。

#### Server认证Client

当Client端基于某个account来访问Server端时，Client必须提供证据表明自己有权限能够使用该account访问Server端。

依赖于Server端的配置以及用户的选择，用户可以采用以下形式的认证，

* 用户发送所基于的账户的password，Server端验证该password
* 用户发送公钥并证明拥有匹配的私钥，Server端由此确认Client端是它所宣传的那个实体，但是需要进一步校验Client提供的公钥在Server端被访问账户的~/.ssh/authorized_keys文件中有保存



### 在使用jsch时候解决UnknownHostKey问题

在下面代码中使用jsch，

```java
JSch jsch = new JSch();
jsch.setKnownHosts("c:\\users\\yong.ni\\.ssh\\known_hosts");
Session session = jsch.getSession(username, remoteHost);
session.setPassword(password);
session.connect();
session.openChannel("stftp");
```

运行抛出UnknownHostKey异常，提示"RSA key fingerprint"

解决过程：

1. 先是在gitbash中运行

   ```shell
   ssh username@remotehost.com
   ```

   提示如下信息：

```
The authenticity of host xxx cannot be established.
ECDSA key fingerprint is SHA256:ffes99wjfdsfxfsddf
Are you sure you want to continue connecting (yes/no/)?
Warnings: Permanently added xxx (ECDSA) to the list of known hosts.
```

输入yes, 然后查看c:\\users\\yong.ni\\.ssh\\known_hosts文件，

里面加入了

```
remotehost, 9.8.10.11 ECDSA f54f323fdsvcdsfds
```

重新运行程序，依然报同样的UnknownHostKey异常

2. 再次搜索解决方案，找到如下链接

   https://stackoverflow.com/questions/34475366/jsch-unknownhostkey-exception-even-when-the-hostkey-fingerprint-is-present-in-t

   这里讲解的很清楚，虽然将ECDSA host key加入到了known_hosts文件中，但是由于使用的是ssh命令来连接的，而ssh偏好使用ECDSA加密类型，也因此加入到known_hosts文件中的是该类型密钥

   而Jsch偏好使用RSA类型密钥，但是known_hosts文件中并没有找到。

   所以有两种解决方法：

   * 在Jsch中enable ECDSA，https://stackoverflow.com/q/30846076/850848

   * 在ssh中使用RSA key, 通过命令行选项 

     ```
     -o HostKeyAlgorithms=ssh-rsa
     ```

3. 采用上述第二种方法，先从known_hosts文件中删除刚刚加入的行，然后运行命令，

   ```
   ssh -o HostKeyAlgorithms=ssh-rsa username@remotehost.com
   ```

   提示如下信息：

   ```
   The authenticity of host xxx cannot be established.
   RSA key fingerprint is SHA256:ffes99wjfdsfxfsddf
   Are you sure you want to continue connecting (yes/no/)?
   Warnings: Permanently added xxx (RSA) to the list of known hosts.
   ```

   输入yes, 然后查看c:\\users\\yong.ni\\.ssh\\known_hosts文件，

   里面加入了,

   ```
   remotehost, 9.8.10.11 ssh-rsa f54f323fdsvcdsfds
   ```

4. 重新运行上述Java程序，可以正常工作。