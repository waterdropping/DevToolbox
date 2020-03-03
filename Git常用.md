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

