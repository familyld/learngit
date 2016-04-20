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

```
lincoln@ubuntu:~/learngit$ git add README.md 
lincoln@ubuntu:~/learngit$ git status
位于分支 master
您的分支与上游分支 'origin/master' 一致。

要提交的变更：
  （使用 "git reset HEAD <file>..." 撤出暂存区）

    修改:         README.md

```

第一次修改后把文件add到暂存区，然后进行第二次修改，再调用commit提交，然后查看status：

```
lincoln@ubuntu:~/learngit$ git commit -m "update:管理修改1"
[master 2d4e074] update:管理修改1
 1 file changed, 6 insertions(+)
 lincoln@ubuntu:~/learngit$ git status
位于分支 master
您的分支领先 'origin/master' 共 1 个提交。
  （使用 "git push" 来发布您的本地提交）

尚未暂存以备提交的变更：
  （使用 "git add <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

    修改:         README.md

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```

显然，根据`git status`返回的信息，我们可以知道commit提交的并不是文件本身，而是已经add到暂存区的第一次修改。

这时如果使用 `git diff HEAD` 命令就可以看到工作区和版本库里最新版本的区别。

那么要怎样提交第二次修改呢？ 一种方法是再次add和commit。除此之外，还可以第一次修改add之后不要急着commit。 而是等到第二次修改也add了，两次修改后并后再commit。 总结一句就是，**每次修改如果不add到暂存区，就不会被commit**。

###撤销修改

如果我们在工作区做了修改，然后查看 `git status`，就会发现，Git除了会提示我们用add来暂存变更之外，还会提示我们可以用 `git checkout 文件名.文件格式` 来撤销变更，丢弃工作区的修改，回到最近一次git add的状态。

```
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 master
您的分支与上游分支 'origin/master' 一致。

尚未暂存以备提交的变更：
  （使用 "git add <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

    修改:         README.md

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```

撤销修改的方法如下，可以看到撤销后再次查看 `git status` ，之前修改的内容已经撤回了，工作区是干净的。

```
lincoln@ubuntu:~/Learning/learngit$ git checkout -- README.md
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 master
您的分支与上游分支 'origin/master' 一致。

无文件要提交，干净的工作区
```

注意！`git checkout -- file` 中的 `--`  很重要，没有这个就变成了切换分支的命令了，后面的分支管理会再提及到。

然而，我们有时犯了傻，已经add到暂存区了，怎么办呢？

```
lincoln@ubuntu:~/Learning/learngit$ git add *
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 master

要提交的变更：
  （使用 "git reset HEAD <file>..." 撤出暂存区）

    修改:         README.md
```

可以看到Git提示了我们可以用 `git reset HEAD 文件名.文件类型` 的方式把最近一次add到暂存区的内容撤销掉。

```
lincoln@ubuntu:~/Learning/learngit$ git reset HEAD README.md
重置后撤出暂存区的变更：
M   README.md
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 master

尚未暂存以备提交的变更：
  （使用 "git add <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

    修改:         README.md

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```

撤销后就回到前面的状态了，这样我们又可以用 `git checkout 文件名.文件格式` 来把工作区的修改撤销了～～

```
lincoln@ubuntu:~/Learning/learngit$ git checkout -- README.md
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 master
您的分支领先 'origin/master' 共 1 个提交。
  （使用 "git push" 来发布您的本地提交）

无文件要提交，干净的工作区
```

重新得到干净的工作区，世界变清净了～～

更进一步来说，如果你不但改错，还从暂存区提交到了版本库，怎么办呢？ 这时我们可以直接利用之前的知识，回退一个版本。不过，这是有条件的，就是你**还没有把自己的本地版本库推送到远程**。后面会继续讲到远程版本库的内容。如果把提交推送到了远程版本库，那就真的惨了……

####小结

场景1：改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令 `git checkout -- file`。

场景2：不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令 `git reset HEAD file`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考前面的版本回退一节，不过前提是没有推送到远程库。

###删除文件

首先添加一个新文件test.txt到Git并且提交：

```
lincoln@ubuntu:~/Learning/learngit$ git add test.txt
lincoln@ubuntu:~/Learning/learngit$ git commit -m "add:新增test.txt 文件"
[master 2e64ab8] add:新增test.txt文件
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt
```

如果不小心把test.txt文件删除掉了，比如：

```
lincoln@ubuntu:~/Learning/learngit$ rm test.txt 
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 master

尚未暂存以备提交的变更：
  （使用 "git add/rm <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

    删除:         test.txt

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```

可以看到删除之后，Git会告诉我们工作区和版本库不一致，并且给了我们两个选择。 

一是确实要从版本库中删除该文件，那就用命令 `git rm` 删掉，并且 `git commit`：

```
lincoln@ubuntu:~/Learning/learngit$ git rm test.txt
rm 'test.txt'
lincoln@ubuntu:~/Learning/learngit$ git commit -m "remove:删除了test.txt文件"
[master b996ce2] remove:删除了test.txt文件
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```

这样一来，文件就从版本库中删除掉了。第二个选择就是恢复文件，万一我们删错了也不担心，因为文件在版本库中还有(如果用了一的方法就真没有了。。。)，我们可以很轻松地恢复文件：

    lincoln@ubuntu:~/Learning/learngit$ git checkout -- test.txt

####小结

命令 `git rm` 用于删除一个文件。如果一个文件**已经被提交到版本库**，那么你永远不用担心误删，但是要小心，你**只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容**。

##远程仓库

前面介绍的功能其实跟集中式版本控制系统SVN没有太大的区别。 事实上，如果只是在一个仓库中管理文件历史，Git和SVN真的没什么区别。 而接下来这章要介绍的就是Git的杀手级功能之一：**远程仓库**。

Git作为一个分布式版本控制系统，同一个Git仓库，可以分布在不同的机器上。当然，最早肯定只有一台机器有一个原始版本库，此后，别的机器可以**克隆**这个原始版本库，而且每台机器的版本库其实都是一样的，并**没有主次之分**。

事实上，一台电脑也可以克隆多个版本库，只要不在统一目录下即可，但一般不这样做，因为万一硬盘坏了，那么所有仓库都挂掉了，这样做没有意义。

实际情况往往是这样的，找一台电脑充当**服务器**的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且**各自把各自的提交推送到服务器仓库里**，也**从服务器仓库中拉取别人的提交**。

我们可以自己搭建一台运行Git的服务器，不过现阶段，为了学Git先搭个服务器绝对是小题大作。好在这个世界上有个叫GitHub的神奇的网站，从名字就可以看出，这个网站就是提供Git仓库托管服务的，所以，只要注册一个GitHub账号，就可以免费获得Git远程仓库。

由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

```
lincoln@ubuntu:~/Learning/learngit$ ssh-keygen -t rsa -C "邮箱名@xxx.com"Generating public/private rsa key pair.
Enter file in which to save the key (/home/lincoln/.ssh/id_rsa): 
/home/lincoln/.ssh/id_rsa already exists.
Overwrite (y/n)? 
```

第一步是创建SSH Key，首先看看主目录(即`lincoln@ubuntu:~`)下有没有`.ssh`这个文件夹，如果有，并且里面有`id_rsa `和`id_rsa.pub`两个文件，就说明已经有SSH密匙了，可以跳到下一步。 如果没有就按照上面的指令输入，然后一路回车即可。

第二步是进入`.ssh`文件夹，里面的`id_rsa `是私钥，绝对不可以泄漏出去；而`id_rsa.pub`是公钥，可以放心告诉任何人。

第三步，登录Github网站，选择`Edit profile`，然后左侧导航栏选择`SSH keys`，点击`ADD Key`添加我们刚刚获得的公钥即可。

为什么GitHub需要SSH Key呢？因为GitHub需要**识别出你推送的提交确实是你推送的**，而不是别人冒充的。因为Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

GitHub允许添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，**只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了**。

However～在GitHub上免费托管的Git仓库是任何人都可以看到的喔（但只有创建人可以修改）。所以，不要把敏感信息放进去。

如果不想让别人看到Git库，有两个办法，**一个是充钱～把GitHub的公开仓库变成私有的**，这样别人就看不见了（不可读更不可写）。**另一个办法是自己动手，搭一个Git服务器**，因为是你自己的Git服务器，所以别人也是看不见的。这个方法我们后面会讲到的，相当简单，公司内部开发必备。

配置好SSH和Github，我们就终于可以开始远程仓库的学习啦～自从有了远程仓库，我们再也不用担心硬盘不够用啦～

***号外:***

如果在公司一个github帐号，自己也有一个github帐号，怎样可以在电脑上同时配两个呢？

注意ssh-keygen是可以选择key的名字的，只要第二个key不要用默认的名字`id_rsa`，自己换个别的，比如`mycompany_rsa`就可以了。在用户主目录下创建`.ssh/config`文件，不同的host用不同的key，参考：[网址1](http://stackoverflow.com/questions/7927750/specify-an-ssh-key-for-git-push-for-a-given-domain) 或者 [网址2](http://jingyan.todgo.com/shuma/2161775tdu.html)。

###添加远程库

在本地创建好一个Git仓库后，我们可以在GitHub也创建同名的Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作了。

在Github上创建仓库非常简单，在`Repositories`页面点击New按钮，填写信息(一般填写仓库名就可以了)后确认即可。这时，Github的仓库还是空的,GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。如下图：

![](http://www.liaoxuefeng.com/files/attachments/0013849084720379a3eae576b9f417da2add578c8612a2e000/0)

我们可以首先吧本地仓库和远程仓库进行关联：

    lincoln@ubuntu:~/Learning/learngit$ git remote add origin git@github.com:用户名/learngit.git
    fatal: 远程 origin 已经存在。

注意这个命令要在本地的learngit仓库下执行，用户名换成自己的即可。添加关联后，远程库的名字就是origin，这是Git默认的叫法，也**可以改成别的**，但是origin这个名字一看就知道是远程库。这里因为我之前已经添加过了，所以显示的是这个远程分支已存在。接下来就可以把本地库的内容推送到远程库上了。

```
lincoln@ubuntu:~/Learning/learngit$ git commit -m "update:添加远程仓库1"[master 56a890c] update:添加远程仓库1
 1 file changed, 17 insertions(+)
lincoln@ubuntu:~/Learning/learngit$ git push -u origin master
Counting objects: 14, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (12/12), 4.05 KiB | 0 bytes/s, done.
Total 12 (delta 4), reused 0 (delta 0)
To git@github.com:familyld/learngit.git
   1e05900..56a890c  master -> master
分支 master 设置为跟踪来自 origin 的远程分支 master。
```

用`git push`命令，可以把本地库的内容推送到远程，实际上是**把本地的master分支推送到远程**。

当我们第一次推送master分支时，加上`-u`参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会**把本地的master分支和远程的master分支关联起来**，在以后的推送或者拉取时就可以直接使用`git push origin master`或者` git pull origin master`了。

####SSH警告

第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：

```
The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
RSA key fingerprint is xx.xx.xx.xx.xx.
Are you sure you want to continue connecting (yes/no)?
```

这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。然后Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

    Warning: Permanently added 'github.com' (RSA) to the list of known hosts.

这个警告只会出现一次，后面的操作就不会有任何警告了。如果实在担心有人冒充GitHub服务器，输入yes前可以对照GitHub的RSA Key的指纹信息是否与SSH连接给出的一致。

####小结

要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；

关联后，使用命令`git push -u origin master`进行master分支的第一次推送；

此后，每次本地提交后就可以直接使用命令`git push origin master`推送最新的修改了；

分布式版本系统的最大好处之一是**在本地工作完全不需要考虑远程库的存在**，也就是没有联网也能正常工作，而SVN在没有联网的时候是拒绝干活的！这样我们只要在有网络的时候把本地提交推送到远程库就完成同步了。

###从远程库克隆

前面一节是先有本地库，再有远程库时如何进行关联。这一节掉过来，我们先在Github创建远程库，然后直接克隆到本地。

建远程库的方法和上一节一样，一个小细节是我们创建远程库时可以勾选初始化README文件，这样创建出来的仓库就自带`README.md`了：

![](http://www.liaoxuefeng.com/files/attachments/0013849085474010fec165e9c7449eea4417512c2b64bc9000/0)

远程库准备好后我们可以直接用`git clone https://github.com/用户名/仓库名.git`语句来把远程仓库克隆到本地来，命令执行的路径下会多出一个以仓库名命名的文件夹，cd进去就可以做我们的工作了。多人协作开发时各自从远程克隆一份即可。

远程仓库的地址我们可以在Github打开对应仓库然后在URL后加上`.git`就可以了。除了使用https地址外，Github还支持原生git的`git clone git@github.com:用户名/仓库名.git`这样的地址，速度较https快一些。

##分支管理

分支在实际中有什么用呢？假设你准备开发一个新功能，需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码没写完，不完整的代码库会导致无法运行程序，这样大家就都不能干活了。但如果我们等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。我们可以创建了一个属于自己的分支，别人看不到，还继续在原来的分支上正常工作，而我们可以在自己的分支上干活，想提交就提交，直到**开发完毕后，再一次性合并到原来的分支上**，这样，既安全，又不影响别人工作。

其他版本控制系统如SVN等都有分支管理，但是这些版本控制系统创建和切换分支比蜗牛还慢，结果分支功能成了摆设，大家都不去用。

但Git的分支是与众不同的，**无论创建、切换和删除分支，Git在1秒钟之内就能完成！无论你的版本库是1个文件还是1万个文件**。

###创建与合并分支

从前面版本回退一节中我们已经知道，每次commit新版本，Git都会把新版本和以前的版本串成一条时间线，这条时间线就称为一个**分支**。 到目前为止，我们都只是用了**主分支**，也即**master分支**。

前面我们讲到`HEAD`指针指向当前的版本(最新commit)，**实际上HEAD指向的是当前分支**，我们使用master分支时，`HEAD`指向的就是master，然后master指向的才是当前的版本，如图：

![](http://www.liaoxuefeng.com/files/attachments/0013849087937492135fbf4bbd24dfcbc18349a8a59d36d000/0)

可以看下面这个短视频来理解，每次commit之后，master分支都会向前移动一步，指向新的版本。

<video id="video" controls="" preload="yes">
      <source  src="http://michaelliao.gitcafe.io/video/master-branch-forward.mp4" type="video/mp4">
</video>

当我们创建一个新的分支时，比如叫做dev分支，Git做的事情非常简单，就是创建了一个指针dev，然后指向mater当前指向的那个版本，然后把`HEAD`指针改为指向dev分支。工作区本身是没有任何变化的，所以创建分支的速度是很快的。

![](http://www.liaoxuefeng.com/files/attachments/001384908811773187a597e2d844eefb11f5cf5d56135ca000/0)

这时，我们再修改工作区并提交修改，针对的就是dev这个分支了，下图表示的是新提交一次的情形，这是master分支不动，dev分支往前移，指向我们最新一次的提交。

![](http://www.liaoxuefeng.com/files/attachments/0013849088235627813efe7649b4f008900e5365bb72323000/0)

假设我们在dev上的工作完成了，就可以把dev分支合并到master上。实现也很简单，只需要直接**把master指向dev当前指向的版本**就可以了。

![](http://www.liaoxuefeng.com/files/attachments/00138490883510324231a837e5d4aee844d3e4692ba50f5000/0)

所以在Git中合并分支也是非常快的，只是改一改指针，工作区不变化。合并完分支后，甚至可以删除dev分支，这样不会对工作区有任何影响。删除dev分支仅仅是把dev指针给删掉，删掉后，我们就只剩下了一条master分支了。

可以看下面的小视频了解从创建分支到合并分支和删除分支的整个过程：

<video id="video" controls="" preload="yes">
      <source  src="http://github.liaoxuefeng.com/sinaweibopy/video/master-and-dev-ff.mp4" type="video/mp4">
</video>

接下来实践一下：

```
lincoln@ubuntu:~/Learning/learngit$ git checkout -b dev
M README.md
切换到一个新分支 'dev'
lincoln@ubuntu:~/Learning/learngit$ git branch
* dev
  master
```

首先创建分支dev并切换到分支dev，我们可以使用上面的写法，用`checkout`命令加上`-b`参数表示创建并切换。这一条命令相当于下面两条：

```
lincoln@ubuntu:~/Learning/learngit$ git branch dev
lincoln@ubuntu:~/Learning/learngit$ git checkout dev
切换到一个新分支 'dev'
```

然后我们可以使用`git branch`来查看当前分支的状况，可以看到分支dev前面有一个星号，这表示当前分支是dev。

  ```
lincoln@ubuntu:~/Learning/learngit$ git add README.md 
lincoln@ubuntu:~/Learning/learngit$ git commit -m "update:创建与合并分支1"
[dev 3fc6975] update:创建与合并分支1
 1 file changed, 37 insertions(+), 1 deletion(-)
 ```

接下来对工作去进行修改并提交到dev分支上去，知道我们完成了工作就切换回主分支，也即master分支：

```
lincoln@ubuntu:~/Learning/learngit$ git checkout master
切换到分支 'master'
您的分支与上游分支 'origin/master' 一致。
```

但我们会发现切换回master分支后，我们之前在工作区上做的修改全都不见了。不用着急！这是因为master分支指向的还是之前的版本，我们只要把dev分支合并到master分支上，之前做的修改就回来了：

```
lincoln@ubuntu:~/Learning/learngit$ git merge dev
更新 9842117..3fc6975
Fast-forward
 README.md | 38 +++++++++++++++++++++++++++++++++++++-
 1 file changed, 37 insertions(+), 1 deletion(-)
 ```

注意到合并是Git告诉我们合并的方式是Fast-forward，即快进模式，直接吧master指向dev的当前提交，所以速度很快。实际上不是每次合并都能Fast-forward的，后面会再讲到其他的方式。最后我们可以删除掉dev分支：

 ```
lincoln@ubuntu:~/Learning/learngit$ git branch -d dev
已删除分支 dev（曾为 3fc6975）。
lincoln@ubuntu:~/Learning/learngit$ git branch
* master
```

####小结

因为创建、合并和删除分支非常快，所以Git鼓励大家使用分支来完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但`过程更安全`。

查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

### 解决冲突

这一小节主要将的是分支和主分支合并时产生冲突要如何处理，首先创建一个新分支，然后修改文件内容，再在新分支中作出提交：

```
lincoln@ubuntu:~/Learning/learngit$ git checkout -b feature1
M README.md
切换到一个新分支 'feature1'
lincoln@ubuntu:~/Learning/learngit$ git add README.md 
lincoln@ubuntu:~/Learning/learngit$ git commit -m "update:冲突1"
[feature1 c07f126] update:冲突1
 1 file changed, 4 insertions(+)
```

然后切换为master分支，这是文件内容会恢复为原来的样子，刚刚在feature1分支做的修改就不见了。我们在master分支也修改内容，然后同样进行提交：

```
lincoln@ubuntu:~/Learning/learngit$ git checkout master
切换到分支 'master'
您的分支与上游分支 'origin/master' 一致。
lincoln@ubuntu:~/Learning/learngit$ git add README.md 
lincoln@ubuntu:~/Learning/learngit$ git commit -m "update:冲突2"
[master 19fca4f] update:冲突2
 1 file changed, 1 insertion(+)
```

这时如果我们尝试吧feature1分支合并到master分支，会提示冲突，因为master分支中有着feature1分支没有的内容。

```
lincoln@ubuntu:~/Learning/learngit$ git merge feature1
自动合并 README.md
冲突（内容）：合并冲突于 README.md
自动合并失败，修正冲突然后提交修正的结果。
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 master
您的分支领先 'origin/master' 共 1 个提交。
  （使用 "git push" 来发布您的本地提交）

您有尚未合并的路径。
  （解决冲突并运行 "git commit"）

未合并的路径：
  （使用 "git add <file>..." 标记解决方案）

  双方修改：     README.md

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```

如果画个图出来，这是的时间线是这样的：

![](http://www.liaoxuefeng.com/files/attachments/001384909115478645b93e2b5ae4dc78da049a0d1704a41000/0)

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，只能手动解决。如果我们查看此时的文件内容会发现Git对两个分支的修改做了标注，像下面这样：

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

`<<<<<<< HEAD` 表示的是master分支的修改，和下一分支的内容用 `=======` 分隔开，`>>>>>>> feature1` 就是我们在feature1分支做的修改。我们要手动把这两个修改合并后再提交就可以了。

```
lincoln@ubuntu:~/Learning/learngit$ git add README.md 
lincoln@ubuntu:~/Learning/learngit$ git commit -m "update:解决冲突"
[master 35c430e] update:解决冲突
```

这时的时间先如下图：

![](http://www.liaoxuefeng.com/files/attachments/00138490913052149c4b2cd9702422aa387ac024943921b000/0)

可以借助 `git log --graph --pretty=oneline --abbrev-commit` 来直观地查看到分支的合并情况，这里可以看到星号表示的是提交新版本，可以看到在f66865a版本后创建了新的分支，并且在新的分支和master分支上都有提交，最终合并的到35c430e分支。

```
lincoln@ubuntu:~/Learning/learngit$ git log --graph --pretty=oneline --abbrev-commit
*   35c430e update:解决冲突
|\  
| * c07f126 update:冲突1
* | 19fca4f update:冲突2
|/  
* f66865a update:创建与合并分支2
* 3fc6975 update:创建与合并分支1
...
```

最后，工作完成，可以删除feature1分支了～～

```
lincoln@ubuntu:~/Learning/learngit$ git branch -d feature1
已删除分支 feature1（曾为 c07f126）。
```

####小结

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

用`git log --graph`命令可以看到分支合并图。

###分支管理策略

通常，合并分支时，如果可能，Git会用 `Fast forward` 模式，但这种模式下，删除分支后，会丢掉分支信息，注意这里说丢掉的意思是我们查看log的时候无法知道我们在哪里开始分支和在哪里做了合并，master的log里面没有任何的合并记录! 具体的下文会进行对比：

```
lincoln@ubuntu:~/Learning/learngit$ git checkout -b dev
切换到一个新分支 'dev'
lincoln@ubuntu:~/Learning/learngit$ git add README.md 
lincoln@ubuntu:~/Learning/learngit$ git commit -m "update:分支管理策略2"
[dev 6a73e3f] update:分支管理策略2
 1 file changed, 1 insertion(+)
```

首先还是新建一个分支dev，然后修改文件并提交。提交后回到master分支，这是我们选择使用 `git merge --no-ff -m "update:分支管理策略3- 禁止快速合并模式" dev` 来合并， `--no-ff` 参数指的就是**禁用快速合并模式**，因为**合并时会产生一个新的commit**，所以我们可以用 `-m` 参数来把commit的描述写进去：
 
```
lincoln@ubuntu:~/Learning/learngit$ git checkout master
切换到分支 'master'
您的分支领先 'origin/master' 共 1 个提交。
  （使用 "git push" 来发布您的本地提交）
lincoln@ubuntu:~/Learning/learngit$ git merge --no-ff -m "update:分支管理策略3- 禁止快速合并模式" dev
Merge made by the 'recursive' strategy.
 README.md | 1 +
 1 file changed, 1 insertion(+)
```

然后我们可以通过 `git log` 查看分支历史，可以看到dev分支的信息被保留下来了，这个分支是从d962bdf版本新建的，然后在ba73e3f版本完成后和master分支进行了合并：

```
lincoln@ubuntu:~/Learning/learngit$ git log --graph --pretty=oneline --abbrev-commit
*   baeeade update:分支管理策略3-禁止快速合并模式
|\  
| * 6a73e3f update:分支管理策略2
|/  
* d962bdf update:分支管理策略1
...
```

再看看使用快速合并模式是怎样的，新建一个dev2分支，同样修改，提交，切换为master分支，然后合并：

```
lincoln@ubuntu:~/Learning/learngit$ git checkout -b dev2
M README.md
切换到一个新分支 'dev2'
lincoln@ubuntu:~/Learning/learngit$ git add *
lincoln@ubuntu:~/Learning/learngit$ git commit -m "update:分支管理策略4"
[dev2 0afa2cc] update:分支管理策略4
 1 file changed, 32 insertions(+), 1 deletion(-)
lincoln@ubuntu:~/Learning/learngit$ git checkout master
切换到分支 'master'
您的分支领先 'origin/master' 共 3 个提交。
  （使用 "git push" 来发布您的本地提交）
lincoln@ubuntu:~/Learning/learngit$ git merge dev2
更新 baeeade..0afa2cc
Fast-forward
 README.md | 33 ++++++++++++++++++++++++++++++++-
 1 file changed, 32 insertions(+), 1 deletion(-)
```

查看合并后的git log，可以发现只有一条直线，也即只有master分支的信息，我们无法看到dev2分支是从哪个版本开始，又是从哪个版本和master进行合并的，**master的log里面没有任何的合并记录**!

``` 
lincoln@ubuntu:~/Learning/learngit$ git log --graph
* commit 0afa2cc3ac186dd2491286f965f7f2d1afdcb422
| Author: familyld <family_ld@foxmail.com>
| Date:   Tue Apr 19 11:37:37 2016 +0800
| 
|     update:分支管理策略4
|    
*   commit baeeadefe03984e54feddc52631936c6bdaebbb6
|\  Merge: d962bdf 6a73e3f
| | Author: familyld <family_ld@foxmail.com>
| | Date:   Mon Apr 18 22:13:32 2016 +0800
| | 
| |     update:分支管理策略3-禁止快速合并模式
| |   
| * commit 6a73e3f6421664f0984e198d9fc23985694493f2
|/  Author: familyld <family_ld@foxmail.com>
|   Date:   Mon Apr 18 22:12:30 2016 +0800
...
```

设想一下如果你的master很重要, 而某一次合并后出现了问题, 但你用的是ff模式, master的log里面没有记录任何的分支信息，这样就**没有任何记录可以让你回退到分支合并前的版本**了! (回退只有靠commit id) **而如果我们很不幸地删除了分支，就既无法知道分支存在过，也无法区分哪些修改是在分支上提交的了**。

不使用Fast forward模式的话，merge之后就长这样：

![](http://www.liaoxuefeng.com/files/attachments/001384909222841acf964ec9e6a4629a35a7a30588281bb000/0)

####小结

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本测试好，要发布了， 就把dev分支合并到master上，在master分支发布1.0版本；

你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![](http://www.liaoxuefeng.com/files/attachments/001384909239390d355eb07d9d64305b6322aaf4edac1e3000/0)

合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过的合并。

最后，如果想要查看本地都有什么分支，可以使用`git branch -a`命令：

```
lincoln@ubuntu:~/Learning/learngit$ git branch -a
  dev
  dev2
* master
  remotes/origin/master
```

###Bug分支

在软件开发中，很容易会出现bug。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，将临时分支删除就可以了。

设想一个情景，当你突然接到一个修复代号101的bug的任务时，很自然地，你想创建一个分支issue-101来修复它，但是你现在正在dev上进行的工作还至少要一天的时间才能完成，所以无法进行提交，而bug要求在两个小时内进行修补，这要怎么办呢？

幸好，Git为我们提供了`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场就能继续工作了。

```
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 dev
尚未暂存以备提交的变更：
  （使用 "git add <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

  修改:         README.md
```

如上，我们在dev分支上做了些修改，但是没有做完，所以还不能提交。如果这是我们要到别的分支修复bug，可以先用`git stash`保存工作现场：

```
修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
lincoln@ubuntu:~/Learning/learngit$ git stash
Saved working directory and index state WIP on dev: addeb12 update:分支管理策略5
HEAD 现在位于 addeb12 update:分支管理策略5
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 dev
无文件要提交，干净的工作区
```

可以看到保存工作现场后，这时的工作区是干净的，我们可以放心地创建分支来修复bug。

```
lincoln@ubuntu:~/Learning/learngit$ git checkout master
切换到分支 'master'
您的分支与上游分支 'origin/master' 一致。
lincoln@ubuntu:~/Learning/learngit$ git checkout -b 'issue-101'
切换到一个新分支 'issue-101'
lincoln@ubuntu:~/Learning/learngit$ git add README.md 
lincoln@ubuntu:~/Learning/learngit$ git commit -m "update:Bug分支1"
[issue-101 4e307eb] update:Bug分支1
 1 file changed, 1 insertion(+), 1 deletion(-)
lincoln@ubuntu:~/Learning/learngit$ git checkout master
切换到分支 'master'
您的分支与上游分支 'origin/master' 一致。
lincoln@ubuntu:~/Learning/learngit$ git merge --no-ff -m "update:Bug分支2" issue-101
Merge made by the 'recursive' strategy.
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
lincoln@ubuntu:~/Learning/learngit$ git branch -d issue-101
已删除分支 issue-101（曾为 4e307eb）。
```

假设出bug的就是master分支，我们可以切换到master分支上，然后创建一个Bug分支，修复好了，再merge到maser分支上，Bug修复就完成了。

```
lincoln@ubuntu:~/Learning/learngit$ git checkout dev
切换到分支 'dev'
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 dev
无文件要提交，干净的工作区
lincoln@ubuntu:~/Learning/learngit$ git stash list
stash@{0}: WIP on dev: addeb12 update:分支管理策略5
lincoln@ubuntu:~/Learning/learngit$ git stash pop
位于分支 dev
尚未暂存以备提交的变更：
  （使用 "git add <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

  修改:         README.md

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
丢弃了 refs/stash@{0} (2becc9f4f9237bddb3207082ba3940b03261acc1)
lincoln@ubuntu:~/Learning/learngit$ git stash list
```

这时切换回dev分支，我们可以恢复工作现场，继续之前的工作。有两种不同的方法可以实现，一是用`git stash apply stash号`(如这里可以用：`git stash apply stash@{0}`)恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；另一种方式是用`git stash pop`， 恢复的同时把stash内容也删了。通过`git stash list`可以看到保存的工作现场。

```
lincoln@ubuntu:~/Learning/learngit$ git merge master
更新 addeb12..bcc2122
error: Your local changes to the following files would be overwritten by merge:
  README.md
Please, commit your changes or stash them before you can merge.
Aborting
lincoln@ubuntu:~/Learning/learngit$ git stash
Saved working directory and index state WIP on dev: addeb12 update:分支管理策略5
HEAD 现在位于 addeb12 update:分支管理策略5
lincoln@ubuntu:~/Learning/learngit$ git status
位于分支 dev
无文件要提交，干净的工作区
lincoln@ubuntu:~/Learning/learngit$ git merge master
更新 addeb12..bcc2122
Fast-forward
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
lincoln@ubuntu:~/Learning/learngit$ git stash list
stash@{0}: WIP on dev: addeb12 update:分支管理策略5
lincoln@ubuntu:~/Learning/learngit$ git stash pop
自动合并 README.md
位于分支 dev
尚未暂存以备提交的变更：
  （使用 "git add <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

  修改:         README.md

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
丢弃了 refs/stash@{0} (ca4b8b0f47248671af0fc3bb02ff60891178a2d0)
lincoln@ubuntu:~/Learning/learngit$ git stash list
```

为了把在master上修复好的Bug同步到dev分支上，可以使用`git merge master`，注意merge的时候工作区必须是干净的，也即我们要在恢复工作现场之前merge，merge好了再恢复，这是我们就得到一个同步了Bug修复的工作现场了。

####小结

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场。123