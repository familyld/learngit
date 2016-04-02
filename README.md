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

关于diff格式，可以阅读阮一峰老师的文章“[读懂diff](http://www.ruanyifeng.com/blog/2012/08/how_to_read_diff.html)”的第六小节。 diff提供了改动位置的上下文信息。其中:

第二行和四五行都表示进行比较的两个文件。四五行中"---"表示变动前的版本，"+++"表示变动后的版本。

第三行表示两个版本的Git哈希值（**暂存区**(即上次add的版本)的d9cd201对象，与**工作区**(即当前修改过的内容)的85d8a04对象进行比较），最后的六位数字是对象的模式（普通文件，644权限）。

第五行变动的位置用两个@作为起首和结束。前面的"-1,5"分成三个部分：减号表示第一个文件，"1"表示第1行，"5"表示连续5行。合在一起，就表示**下面展示了**第一个文件从第1行开始的连续5行。同样的，"+1,5"表示变动后，**成为第二个文件**从第1行开始的连续5行。

接下来是变动的内容，除了有变动的那些行以外，一般上下文各显示3行(这里例子因为改变的地方上下文各只有2行，所以只能显示总共5行了.. 当然如果是新增内容而不是改动原内容的某些部分的话，diff就会显示新增的内容)。因为这种格式将两个文件的上下文，合并显示在一起，所以叫做**"合并格式"**。每一行最前面的标志位，空表示无变动，**减号表示第一个文件删除的行，加号表示第二个文件新增的行**。

此外，使用 `git diff --cached` 可以对**暂存区**的版本和**已提交仓库**的版本进行比较，也即上一次add的版本和上一次commit的版本进行比较。而使用 `git diff HEAD` 可以对**工作区**的版本和**已提交仓库**的版本进行比较。

了解了修改的内容后就可以放心地提交到仓库了，这和提交新文件是一样的，分add和commit两步：

    lincoln@ubuntu:~/learngit$ git add readme.md 
    lincoln@ubuntu:~/learngit$ git status
    位于分支 master
    要提交的变更：
      （使用 "git reset HEAD <file>..." 撤出暂存区）
    
        修改: readme.md

可以看到add之后，仓库的状态与add之前有了变化，修改的版本被加入了暂存区。

    lincoln@ubuntu:~/learngit$ git commit -m "second try"
    [master 7a56abc] second try
     1 file changed, 1 insertion(+), 1 deletion(-)
    lincoln@ubuntu:~/learngit$ git status
    位于分支 master
    无文件要提交，干净的工作区

提交后再查看仓库的状态，此时工作目录是干净的~直到发生下次更改。

###版本回退

学会查看修改之后，这次再做一次修改，(把second改成third)，然后提交。

    lincoln@ubuntu:~/learngit$ git add readme.md 
    lincoln@ubuntu:~/learngit$ git commit -m "third try"
    [master c161f68] third try
     1 file changed, 1 insertion(+), 1 deletion(-)

像这样我们可以不断更改不断提交，每次提交就相当于保存了一个快照。如果把文件改乱了或者误删了，可以从最近一个commit恢复。

回顾一下我们的修改，可以用 `git log`  命令查看：

    lincoln@ubuntu:~/learngit$ git log
    commit c161f68f94bfc677cc1d4732377ba316a2295c50
    Author: familyld <family_ld@foxmail.com>
    Date:   Tue Mar 29 14:11:07 2016 +0800
    
        third try
    
    commit 7a56abcd1e56260bbe0ef2d15c2e19f419abd1bb
    Author: familyld <family_ld@foxmail.com>
    Date:   Tue Mar 29 13:27:18 2016 +0800
    
        second try
    
    commit 85e8aa449ba1868259a259f8083d015c3f37941b
    Author: familyld <family_ld@foxmail.com>
    Date:   Mon Mar 28 00:18:09 2016 +0800
    
        wrote a readme file
    
`git log` 命令会显示从最近到最远的提交日志，我们可以通过commit时的注释来清楚看到自己做了什么修改(所以一定要写好，像这里的就是写得不好的例子hahaha)。

如果觉得输出太多，还可以用简易的版本：
    
    lincoln@ubuntu:~/learngit$ git log --pretty=oneline
    c161f68f94bfc677cc1d4732377ba316a2295c50 third try
    7a56abcd1e56260bbe0ef2d15c2e19f419abd1bb second try
    85e8aa449ba1868259a259f8083d015c3f37941b wrote a readme file

加上 `--pretty=oneline` 参数后，每个提交只显示一行，前面的字符串是**commit id(版本号)**。

如果写错了想回到上一个版本怎么办呢？我们可以用 `git reset` 来实现：

    lincoln@ubuntu:~/learngit$ git reset --hard HEAD^
    HEAD 现在位于 7a56abc second try

通过参数 `hard` 来定位要回滚到什么版本，当前版本是 `HEAD`，所以如果跟的是 `HEAD`，将不会有任何变化。 而 `HEAD^` 表示的则是回滚到上一个版本，可以看到这里滚回到了第二次修改的状态。

    lincoln@ubuntu:~/learngit$ git log --pretty=oneline
    7a56abcd1e56260bbe0ef2d15c2e19f419abd1bb second try
    85e8aa449ba1868259a259f8083d015c3f37941b wrote a readme file

这时再查看 `git log`，显然最新那次修改已经没有了。 咦，那我们既然能回到过去，能不能回到未来呢？这是可以的！不过稍微麻烦一些~

    lincoln@ubuntu:~/learngit$ git reset --hard c161f68
    HEAD 现在位于 c161f68 third try
    lincoln@ubuntu:~/learngit$ git log --pretty=oneline
    c161f68f94bfc677cc1d4732377ba316a2295c50 third try
    7a56abcd1e56260bbe0ef2d15c2e19f419abd1bb second try
    85e8aa449ba1868259a259f8083d015c3f37941b wrote a readme file

如果我们要“回到未来”，很重要的一点是要记住版本号！我们可以通过给 `hard` 参数指定未来的位置，然后顺利地回去。而且不需要输入完整的版本号，输入前面一部分，能和其他版本区分开就可以了。

Git版本回退的速度非常快，实际内部有一个指向当前版本的 `HEAD` 指针，回退时Git仅仅是改变了指针指向的位置。

然而..万一吃错药了，电脑关机了第二天打开找不到版本号了怎么回到未来呢？ 英雄莫慌！ Git大法师还有一招：

    lincoln@ubuntu:~/learngit$ git reflog
    7a56abc HEAD@{0}: reset: moving to HEAD^
    c161f68 HEAD@{1}: commit: third try
    7a56abc HEAD@{2}: commit: second try
    85e8aa4 HEAD@{3}: commit (initial): wrote a readme file

`git reflog` 记录着我们每一次的命令， 可以看到我们之前commit第三次修改时的版本号 `c161f68`，于是我们又可以轻松地坐着时光机回到未来了~~

####小结

- `HEAD` 指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令 `git reset --hard commit_id`。

- 穿梭前，用 `git log` 可以查看提交历史，以便确定要回退到哪个版本。

- 要重返未来，用 `git reflog` 查看命令历史，以便确定要回到未来的哪个版本。

###工作区和暂存区

Git和其他版本控制系统如SVN的一个不同之处就是有暂存区的概念。

首先看几个名词的解释：

***工作区(Working Directory)***

工作区其实顾名思义就是我们工作的地方，就是我们存储文件的本地目录，比如这个git项目中learngit文件夹就是一个工作区。

![工作区](http://www.liaoxuefeng.com/files/attachments/0013849082162373cc083b22a2049c4a47408722a61a770000/0)

***版本库(Repository)***

前面“创建版本库”一节中已经提到了这个概念，在工作区下有一个隐藏目录 `.git`，这个隐藏目录其实就是Git的版本库。

![版本库](http://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)

Git的版本库中保存了很多信息，上图显示的是我们的工作流程，可以看到add命令发出后，文件被放入到版本库的**stage区域**，这个区域也称**暂存区**， 或者**index区域**。 

除了暂存区，版本库里还有Git自动为我们创建的第一个分支 `master`, 以及指向 `master` 的指针 `HEAD`。 分支和指针 `HEAD` 的指针放在后面的章节中再作了解。

所以我们往版本库添加改动信息时分两步执行的：

第一步是用 `git add` 把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用 `git commit` 提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个 `master` 分支，所以调用 `git commit` 时默认往 `master` 分支上提交更改。

可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。就像在逛超市一样，我们一路把商品(改动信息)放入购物车(暂存区)，最后再在收银台结帐(提交到分支)。如果没有购物车，我们没买一次商品都要付一次账就太麻烦了，这就是暂存区存在的意义。

简单实践一下，在learngit文件夹下创建一个LICENSE文件，然后用 `git status` 查看：

```
lincoln@ubuntu:~/learngit$ git status
尚未暂存以备提交的变更：
  （使用 "git add <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

    修改:         README.md

未跟踪的文件:
  （使用 "git add <file>..." 以包含要提交的内容）

    LICENSE

```

可以看到因为README文件修改了，Git会提醒我们**变更尚未暂存**(没有add到暂存区)，而LICENSE文件因为是一个新的文件，从未被add过，所以状态是**未跟踪的**。

接下来就把它们都add到暂存区，然后再次查看状态：

```
lincoln@ubuntu:~/learngit$ git status
位于分支 master

要提交的变更：
  （使用 "git reset HEAD <file>..." 撤出暂存区）

    新文件:       LICENSE
    修改:         README.md

```

这时变动都已经放入版本库的暂存区了，如下图：

![暂存区](http://www.liaoxuefeng.com/files/attachments/001384907720458e56751df1c474485b697575073c40ae9000/0)

最后执行 `git commit` 就可以一次性把暂存区的所有修改提交到分支了。

```
lincoln@ubuntu:~/learngit$ git commit -m "update:工作区和暂存区1"
[master c177c5e] update:工作区和暂存区1
 2 files changed, 60 insertions(+), 3 deletions(-)
 create mode 100644 LICENSE
```

提交过后，直到下一次作出修改，工作区都是“干净的”:

```
lincoln@ubuntu:~/learngit$ git status
位于分支 master
无文件要提交，干净的工作区
```

此时，**暂存区和最新一次commit(笔记里有时也称“已提交仓库/分支”)内容一致**，所以使用 `git diff --cached` 查询 不会显示任何差异信息。注意这里是内容一致，而**不是暂存区没有任何内容**。 这点和廖雪峰老师教程里的讲法是有区别的。

###管理修改

为什么Git比其他版本控制系统设计得优秀，因为**Git跟踪并管理的是修改，而非文件**。

不妨做个实验：

