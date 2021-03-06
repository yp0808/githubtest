## 1 git

提交更改
```
git commit -m "wrote a readme file" 
```

`git commit`命令，`-m`后面输入的是本次提交的说明，可以输入任意内容，最好是有意义的，这样就能从历史记录里方便地找到改动记录。

为什么Git添加文件需要add，commit一共两步呢？因为commit可以一次提交很多文件，所以你可以多次add不同的文件。
每次都要add，没有add**过**的文件不能commit。

***

## 2 时光穿梭机

### 2.1版本退回

不断对文件进行修改，然后不断提交修改到版本库里，就好比玩RPG游戏时，每通过一关就会自动把游戏状态存盘，如果某一关没过去，你还可以选择读取前一关的状态。有些时候，在打Boss之前，你会手动存盘，以便万一打Boss失败了，可以从最近的地方重新开始。Git也是一样，每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为`commit`。一旦你把文件改乱了，或者误删了文件，还可以从最近的一个`commit`恢复，然后继续工作，而不是把工作成果全部丢失。

在实际工作中，我们脑子里怎么可能记得一个几千行的文件每次都改了什么内容，不然要版本控制系统干什么。版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用`git log`命令查看

准备把*test.md*回退到上一个版本，怎么做呢？

首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交*1094adb...*，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，往上100个版本写成`HEAD~100`。现在，我们要把当前版本回退到上一个版本，就可以使用`git reset`命令
```
$ git reset --hard HEAD^
```

最新的那个版本已经看不到了，想再回去已经回不去了，怎么办？
只要上面的命令行窗口还没有被关掉，找到的`commit id`是*1094adb...*，于是就可以指定回到未来的某个版本：
```
$ git reset --hard 1094a
```
现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的*commit id*怎么办？
当你用`$ git reset --hard HEAD^`回退到上一版本时，再想恢复到后一版本，就必须找到后一的*commit id*。Git提供了一个命令`git reflog`用来记录你的每一次命令：
```
$ git reflog
```


小结

HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

### 2.2 工作区和暂存区

版本库（Repository）
工作区有一个隐藏目录*.git*，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的**暂存区**，还有Git为我们自动创建的第一个分支**master**，以及指向master的一个指针叫**HEAD**。

![avatar](0.jpeg)


### 2.3 管理修改

Git跟踪并管理的是修改，而非文件。
Git管理的是修改，当你用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，git commit只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交


提交后，用`git diff HEAD -- test.md`命令可以查看工作区和版本库里面最新版本的区别：

> 注：git diff退出：英文状态下按Q。



## 2.4 撤销修改

命令`git checkout -- test.md`，把*test.md*文件在工作区的修改全部撤销，这里有两种情况：

- 一种是test.md自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
- 一种是test.md已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。


如果修改后`git add`到暂存区了：
`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区
`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。
再git checkout 


## 2.5 删除文件
先添加一个新文件*test3.py*到Git并且提交
一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用`rm`命令删了：
```
$ rm test.txt
```
这个时候，Git知道你删除了文件，因此，工作区和版本库就不一致了，`git status`命令会立刻告诉你哪些文件被删除了：
现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`：

```
$ git rm test.txt

$ git commit -m "删除test3.py"
```

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
```
$ git checkout -- test3.py
```
`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

>注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！



## 3 远程仓库
注册GitHub账号。由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

- 第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有*id_rsa*和*id_rsa.pub*这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
```
$ ssh-keygen -t rsa -C "youremail@example.com"
```
如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有*id_rsa*和*id_rsa.pub*两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。

- 第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴*id_rsa.pub*文件的内容：

为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

当然，GitHub允许你添加多个Key。假定你有若干电脑，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

### 3.1 添加远程仓库
- 首先，登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库：
- 在本地的learngit仓库下运行命令：
```
$ git remote add origin git@github.com:xxx
```
- 下一步，就可以把本地库的所有内容推送到远程库上：
```
$ git push -u origin master
```
由于远程库是空的，我们第一次推送master分支时，加上了`-u`参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。


### 3.2 从远程克隆

用命令git clone克隆一个本地库：

```$ git clone git@github.com:xxx```
你也许还注意到，GitHub给出的地址不止一个，还可以用`https://github.com/xxx` 这样的地址。实际上，Git支持多种协议，默认的`git://`使用`ssh`，但也可以使用https等其他协议。

使用`https`除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放`http`端口的公司内部就无法使用ssh协议而只能用`https`。



## 4 分支管理

分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

### 4.1 创建与合并分支

在版本回退里，你已经知道，每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即`master`分支。`HEAD`指向`master`，所以，`HEAD`指向的就是当前分支。

![avatar](1.png)

每次提交，master分支都会向前移动一步，这样，随着你不断提交，master分支的线也越来越长
当我们创建新的分支，例如`dev`时，Git新建了一个指针叫`dev`，指向`master`相同的提交内容点，再把`HEAD`指向`dev`，就表示当前分支在`dev上`：

![avatar](2.png)

从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变

![avatar](3.png)

假如我们在`dev`上的工作完成了，就可以把dev合并到`master`上。Git怎么合并呢？最简单的方法，就是直接把`master`指向`dev`的当前提交，就完成了合并：

![avatar](4.png)

合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支：

![avatar](5.png)

- 首先，我们创建dev分支，然后切换到dev分支：

```
$ git checkout -b dev
```
`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：
```
$ git branch dev
$ git checkout dev
```
然后，用git branch命令查看当前分支
然后，我们就可以在dev分支上正常提交
现在，dev分支的工作完成，我们就可以切换回master分支：
```
$ git checkout master
```
切换回master分支后，再查看文件，刚才添加的内容不见了！因为那个提交是在dev分支上，而master分支此刻的提交点并没有变.

![avatar](6.png)

现在，我们把dev分支的工作成果合并到master分支上：
```
$ git merge dev
```

git merge命令用于合并指定分支到当前分支。
> 注意到上面的*Fast-forward*信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。当然，也不是每次合并都能*Fast-forward*。




### 4.2 解决冲突

准备新的feature1分支，继续我们的新分支开发：
```
$ git checkout -b feature1
```
切换到master分支修改并提交
现在，master分支和feature1分支各自都分别有新的提交，变成了这样：

![avatar](7.png)

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突：
```
$ git merge feature1
```


### 4.3 分支管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在`merge`时生成一个新的`commit`，这样，从分支历史上就可以看出分支信息。

`--no-ff`方式的git merge：

首先，仍然创建并切换dev分支：
```
$ git checkout -b dev
$ git add readme.txt 
$ git commit -m "add merge"
```
现在，我们切换回master：
准备合并dev分支，请注意`--no-ff`参数，表示禁用*Fast forward*：
```
$ git merge --no-ff -m "merge with no-ff" dev
```
合并后，我们用`git log`看看分支历史：
```
$ git log
```
可以看到，不使用Fast forward模式，merge后就像这样：
![avatar](8.png)



### 4.4 bug分支

当你接到一个修复一个代号*101*的bug的任务时，很自然地，你想创建一个分支*issue-101*来修复它，但是，等等，当前正在dev上进行的工作还没有提交：
```
$ git status
```
Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作
```
$ git stash
```
首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支：
```
$ git checkout master
$ git checkout -b issue-101
```
修复完成后，切换到master分支，并完成合并，最后删除issue-101分支：
```
$ git checkout master
$ git merge --no-ff -m "merged bug fix 101" issue-101
$ git branch -d issue-101
```
现在，是时候接着回到dev分支干活了！
```
$ git checkout dev
$ git status
```


### 4.5 feature分支
每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。
你终于接到了一个新任务：开发代号为Vulcan的新功能，该功能计划用于下一代星际飞船。
于是准备开发：
```
$ git checkout -b feature-vulcan
```
开发完毕：
```
$ git add vulcan.py
```
切回dev，准备合并：
```
$ git checkout dev
```
feature-vulcan分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的-D参数

### 4.6 多人协作

当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。

要查看远程库的信息，用git remote
或者，用`git remote -v`显示更详细的信息：
推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：
```
$ git push origin master
```

### 4.7 rebase

多人在同一个分支上协作时，很容易出现冲突。即使没有冲突，后push的童鞋不得不先pull，在本地合并，然后才能push成功。
每次合并再push后，分支比较乱：

```
$ git log --graph --pretty=oneline --abbrev-commit
```

在和远程分支同步后，我们对hello.py这个文件做了两次提交。用git log命令看看：
```
$ git log --graph --pretty=oneline --abbrev-commit
```
失败了，这说明有人先于我们推送了远程分支。按照经验，先pull一下：
```
$ git pull
```
再用git status看看状态：
```
$ git status
```
提交历史分叉了，`rebase`就派上了用场。我们输入命令`git rebase`
```
$ git rebase
```
原本分叉的提交现在变成一条直线了,Git把本地的提交“挪动”了位置，放到了`(origin/master) set exit=1`时间点之后，这样，整个提交历史就成了一条直线。rebase操作前后，最终的提交内容是一致的，但是，我们本地的commit修改内容已经变化了，它们的修改不再基于为从远程合并之前，而是基于`(origin/master) set exit=1`，但最后的提交内容是一致的。

这就是rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。
最后，通过push操作把本地分支推送到远程：

```
$ git push origin master
```

--https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192