### 1. 如何从远程仓库删除文件而不影响本地目录

应用场景：在上传本地仓库的时候由于没有事先建立.gitignore文件，而将编译的目标文件或者工作区元数据文件上传至远程仓库，因此需要从远程仓库和本地仓库中删除这些文件。

参考文章：https://blog.csdn.net/wudinaniya/article/details/77508229

方法步骤：

$ git pull origin master                    # 将远程仓库里面的项目拉下来
$ git rm -r --cached target              # 删除target文件夹
$ git commit -m '删除了target'        # 提交,添加操作说明
$ git push -u origin master               # 将本次更改更新到github项目上去


### 2. .gitignore文件简单使用
建议：在做push之前就建立好.gitignore文件

参考文章：https://www.cnblogs.com/kevingrace/p/5690241.html

\#               表示此为注释,将被Git忽略

*.a             表示忽略所有 .a 结尾的文件

!lib.a          表示但lib.a除外

/TODO           表示仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO

build/          表示忽略 build/目录下的所有文件，过滤整个build文件夹；

doc/*.txt       表示会忽略doc/notes.txt但不包括 doc/server/arch.txt
 
bin/:           表示忽略当前路径下的bin文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件

/bin:           表示忽略根目录下的bin文件

/*.c:           表示忽略cat.c，不忽略 build/cat.c

debug/*.obj:    表示忽略debug/io.obj，不忽略 debug/common/io.obj和tools/debug/io.obj

**/foo:         表示忽略/foo,a/foo,a/b/foo等

a/**/b:         表示忽略a/b, a/x/b,a/x/y/b等

!/bin/run.sh    表示不忽略bin目录下的run.sh文件

*.log:          表示忽略所有 .log 文件

config.php:     表示忽略当前路径的 config.php 文件
 
/mtk/           表示过滤整个文件夹

*.zip           表示过滤所有.zip文件

/mtk/do.c       表示过滤某个具体文件


### 3. 本地分支与远程分支  
参考文章： https://blog.csdn.net/ustccw/article/details/79068547  

#### 3.1 查看分支
* 查看本地分支  
D:\workspace\idea_SecKill>**git branch**  
  feature-multi-thread  
\* feature-multi-thread-update  
  feature-setup-connection-beforehand  
  master  

* 查看远程分支  
D:\workspace\idea_SecKill>**git branch -r**  
  origin/feature-multi-thread  
  origin/feature-setup-connection-beforehand  
  origin/master  
  
* 查看所有分支
D:\workspace\idea_SecKill>**git branch -a**  
\* feature-multi-thread  
  feature-setup-connection-beforehand  
  master  
  remotes/origin/feature-multi-thread  
  remotes/origin/feature-setup-connection-beforehand  
  remotes/origin/master  


#### 3.2 在与远程分支同步前保持本地分支干净  
对于Untracked files，直接操作系统上文件删除  
对于已经git add到暂存区的, 使用**git reset HEAD filename** （特定文件） 或者 **git reset HEAD** (所有文件文件夹) .  
对于已经git add并且git commit的，  
* 使用**git reset commit_id**，该id是想要回到的那个节点，通过git log查看，输入id的前6位即可。撤销之后，该commit还在工作区。  
* 使用**git reset --hard commit_id**, 该id是想要回到的那个节点，通过git log查看，输入id的前6位即可。撤销之后，该commit修改的代码从暂存区以及工作区清除。  

#### 3.3 将远程分支状态更新到本地
只有将远程分支状态更新到本地，后续的git checkout才能使得本地状态更新到与远程分支保持一致。  
D:\workspace\idea_SecKill>**git fetch origin feature-multi-thread**  
From https://github.com/waterdropping/idea_SecKill  
 \* branch            feature-multi-thread -> FETCH_HEAD  


#### 3.4 拉取远程分支并创建本地分支
参考文章： https://blog.csdn.net/tterminator/article/details/52225720  
  
在本地新建分支，并自动切换到该本地分支。采用此种方法建立的本地分支会和远程分支建立映射关系。  
**git checkout -b 本地分支名 origin/远程分支名**  
  
如果当前就在与远程分支映射的本地分支上，且本地分支已经通过Step 3保持干净，则直接使用git pull就可将远程分支最新文件更新到本地。  
D:\workspace\idea_SecKill>**git pull**  


#### 3.5 拓展 - 本地分支与远程分支的映射关系  
参考文章： https://blog.csdn.net/tterminator/article/details/78108550  

* 查看本地分支与远程分支的映射关系  
D:\workspace\idea_SecKill>**git branch -vv**  
  feature-multi-thread                6964ddc [origin/feature-multi-thread: behind 8] Add simple class comments  
\* feature-multi-thread-update         de6e4b1 [origin/feature-multi-thread] Merge pull request #3 from waterdrop  
ping/feature-setup-connection-beforehand  
  feature-setup-connection-beforehand 6b25143 [origin/feature-setup-connection-beforehand] refactor some files l  
ocation and add .iml into .gitignore  
  master                              3636727 [origin/master: behind 4] add gitignore file  

如果当前本地分支与任何远程分支都没有建立映射关系，则**git pull**以及**git push**命令都无法正常操作。  


* 建立本地分支与远程分支的映射关系  
**git branch -u 远程分支(例如origin/addFile)**  
或者**git branch --set-upstream-to origin/addFile**  

* 撤销本地分支与远程分支的映射关系  
**git branch --unset-upstream**  

 

