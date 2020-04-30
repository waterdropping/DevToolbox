## Tomcat 远程调试

### Windows设置

1. 在Tomcat bin目录中添加setenv.bat文件。

2. 在setenv.bat文件中，添加以下，
set CATALINA_OPTS=-server -Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address= 8000

3. 通过startup.bat启动Tomcat

在IDEA中设置

![image](https://github.com/waterdropping/MyImages/blob/master/IDEA_remote_debug_config.png)

