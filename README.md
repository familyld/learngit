#Git学习笔记
##Git简介
Git是目前世界上最先进的分布式版本控制系统(没有之一~~)。 So，什么是版本控制系统？ 

版本控制就是每次改动文件时都进行记录，如果没有git这样的自动版本控制软件，我们就是自己复制很多个备份，要回滚时就会非常麻烦和混乱。 使用git还有一个好处就是可以和同事协作编辑，这样就不需要我们每次把文件传来传去了。向下面这样：

| 版本 | 用户 | 说明  | 日期 |
|:------:|:------:|:------:|:------:|
| 1    | 张三 | 删除了软件服务条款5 | 7/12 10:38 |
| 2    | 王五 | 增加了License人数限制 | 7/12 18:09 |
| 3    | 李四 | 财务部门调整了合同金额 | 7/13 09:51 |
| 4    | 张三 | 延长了免费升级周期5 | 7/14 15:17 |

###集中式vs分布式

Git是Linux的开发者Linus开发的~ 在此之前其实已经存在CVS和SVN这两个集中式版本控制系统，Linus坚决不用，而是选择自己开发了分布式版本控制系统Git，那么集中式和分布式到底有什么区别呢？

集中式版本控制系统中，版本库是放在中央服务器的。什么意思呢？就是说干活前得先从中央服务器拿到最新版本，然后在自己电脑干活，干完活再推送到中央服务器。

![](http://www.liaoxuefeng.com/files/attachments/001384860735706fd4c70aa2ce24b45a8ade85109b0222b000/0)

集中式最大的毛病就是要联网才能工作，网速一慢整个流程速度就非常慢..而且多人协作时，如果中央服务器出了问题，所有人都没法干活了。

分布式版本控制系统就不同了，每个人电脑上都有一个完整的版本库，工作时就不需联网了。多人协作时只需要完成后把自己修改的版本推送给对方就可以了。

![](http://www.liaoxuefeng.com/files/attachments/0013848607465969378d7e6d5e6452d8161cf472f835523000/0)

实际中一般也很少两个人之间推送，因为双方可能不在同一局域网或者不同时在线。所以一般还是会有一台充当“中央服务器”的电脑，这个中央服务器仅用来方便大家修改，即使坏了，大家电脑中也有自己的版本库，不影响工作。除了这个优势，Git还拥有极其强大的分支管理，后面再谈及这一点。

##创建版本库

版本库也叫**仓库(repository)**，可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

创建版本库很简单，先找好地方创建空目录：

    $ mkdir learngit
    $ cd learngit
    $ pwd
    /home/lincoln/learngit

pwd命令用于linux中获取当前目录。

第二步是通过 `git init` 把这个目录变为Git可以管理的仓库：

    $ git init
    初始化空的 Git 版本库于 /home/lincoln/learngit/.git/

这时原本的空目录下就会多出一个 `.git` 目录了，这会是Git用来跟踪管理版本库的，**千万不要手动修改里面的文件**。如果没看到这个目录是因为系统默认隐藏了，可以通过 `ls -ah` 命令查看。

注意**任何版本控制系统，只能跟踪文本文件的改动**。对于文本文件，版本控制系统可以告诉你每次的改动，比如在第5行加了一个单词“Linux”，在第8行删了一个单词“Windows”。 而图片视频等二进制文件是无法进行版本控制的，只能知道文件大小的变化。(Word也是二进制格式，无法进行版本控制，所以**文本文件应当用纯文本方式编写**)。

现在**在该目录下**(仓库内)编写一个readme文件，然后使用Git把它添加到仓库。这需要两步：

    lincoln@ubuntu:~/learngit$ git add readme.md
    lincoln@ubuntu:~/learngit$ git commit -m "wrote a readme file"
    [master （根提交） 85e8aa4] wrote a readme file
     1 file changed, 5 insertions(+)
     create mode 100644 readme.md

这里 `git commit` 命令中 `-m` 参数后接的是本次提交的说明，可以写一下这次提交做了什么改动，这样对自己还是对合作的同事都有好处。

`git commit` 命令执行成功后会告诉你，多少个文件被改动，插入了多少行内容。commit是可以一次提交多个文件的，只要这些文件已经add到目录里：

    $ git add file1.txt
    $ git add file2.txt file3.txt
    $ git commit -m "add 3 files."

##时光机穿梭

在提交readme成功后，再次进行修改，然后可以通过 `git status` 查看结果：

    lincoln@ubuntu:~/learngit$ git status
    位于分支 master
    尚未暂存以备提交的变更：
      （使用 "git add <file>..." 更新要提交的内容）
      （使用 "git checkout -- <file>..." 丢弃工作区的改动）
    
    	修改: readme.md
    
    修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）

`git status` 命令可以让我们时刻掌握仓库当前的状态，上面的命令告诉我们，readme.md被修改过了，但还没有提交的修改。

如果隔了一段时间，不记得上一次修改了什么内容，可以通过 `git diff` 命令来查看：

    lincoln@ubuntu:~/learngit$ git diff
    diff --git a/readme.md b/readme.md
    index d9cd201..85d8a04 100644
    --- a/readme.md
    +++ b/readme.md
    @@ -1,5 +1,5 @@
     Git is a version control system.
     Git is free software.
    -This is my first time using Git.
    +This is my second time using Git.
     I'm a little nervous. 
     ..........lol...........
    \ No newline at end of file

这里显示的格式正是Unix通用的diff格式，可以从上面的命令输出看到，这次修改是修改了第3行，把单词first换成了second。

了解了修改的内容后就可以放心地提交到仓库了，这和提交新闻奖是一样的，分add和commit两步：

    lincoln@ubuntu:~/learngit$ git add readme.md 
    lincoln@ubuntu:~/learngit$ git status
    位于分支 master
    要提交的变更：
      （使用 "git reset HEAD <file>..." 撤出暂存区）
    
    	修改: readme.md

可以看到add之后，仓库的状态与add之前有了变化，修改以及加入到目录(工作区)中。

    lincoln@ubuntu:~/learngit$ git commit -m "second try"
    [master 7a56abc] second try
     1 file changed, 1 insertion(+), 1 deletion(-)
    lincoln@ubuntu:~/learngit$ git status
    位于分支 master
    无文件要提交，干净的工作区

提交后再查看仓库的状态，此时工作目录是干净的~直到发生下次更改。

