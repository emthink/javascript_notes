# Git由浅入深之操作与指令

本篇正式开始介绍Git的基础操作与原理，看完本篇，你应该知道如何使用Git进行一次基础的版本控制，包括：Git仓库的生成和获取，添加和忽略版本控制对象；暂存，查看，缓存，变更；查看和查找提交历史；格式化历史记录输出；删除和移动Git仓库内对象。

## 获取Git仓库（Git repository）

使用Git的第一步是要获取一个Git仓库，我们使用Git的操作对象都是存放在Git仓库里，获取Git仓库的方式有两种：

- 导入一个项目或目录到Git，初始化(init)生成Git仓库；
- 从远程服务器克隆（clone）一个Git仓库。

#### 初始化仓库（init）

若我们需要使用一个项目或目录生成一个Git仓库，只需要通过命令行进入该目录，执行如下Git指令：

```
git init
```
此操作将在此目录生成一个.git子目录，该目录包含整个仓库结构，即仓库的所有文件；同时会检出（checkout）一个默认工作分支，通常名为master。

此时，我们只是初始化生成了一个Git仓库，还未添加需要进行版本控制的对象--文件或目录。

#### 克隆仓库（clone）

很多时候，我们需要从远程服务器获取一个已存在的Git仓库，我们只需要使用如下指令：

```
git clone https://github.com/codingplayboy/javascript_notes
```

```git clone```后面跟着的url就是已存在的Git仓库地址，我们需要知道的是Git克隆是对服务器上仓库的一次近乎完整的数据拷贝，当前仓库项目的所有文件及其各版本历史都会被获取。

执行如上指令后，会在当前目录创建一个javascript_notes目录并在javascript_notes中初始化一个.git子目录，拉取仓库的所有数据，然后根据仓库（或项目）的最新版本检出（check out）一个工作分支，通常该分支默认名称为master。

##### 仓库别名

我们克隆一个Git仓库时，其默认名还是仓库名，但是也支持我们自定义本地别名：

```
git clone https://github.com/codingplayboy/javascript_notes js_notes
```

执行如上指令，会克隆一个仓库，并导入到当前目录下的js_notes目录。

#### 仓库Url

Git仓库Url支持的协议有很多，最常见的是https:// 和git@；还有使用[SSH传输协议](http://baike.baidu.com/link?url=rSSjv5pXjc4JJ6akAQWnBAyk45XQRe4ooxnVxebkWLHaSRqihYeFEPktzt0q3E2VkbVrX0j0zG5ZQe2Ix66xw_)的，
形如git://或者user@server:path/xxx/repository.git。

## 版本控制对象

在上节，我们已经知道如何获取一个Git仓库，但是到目前为止也仅仅是存在一个仓库，我们进行版本控制的对象（文件或目录）并没有添加进仓库。

#### 工作目录及其内对象的状态

相关指令：```git status```

当我们获取一个仓库，如克隆一个远端仓库后，在仓库目录执行上面指令，如图所示：

![git-status](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-status.png)

- 图中第一行```on branch master```告诉我们当前我们处在名为master的分支；
- 第二行```up-to-date with 'origin/master'```，说明目前分支与远程仓库的master分支保持同步最新版本；
- 第三行```working directory clean```，说明目前仓库中没有新加或修改过的对象（文件或目录）。

##### 关于控制对象的状态(git status)

在当前工作目录的文件或目录，可能出于两种状态：

- 已标记（tracked）

	所谓已标记文件或目录，即那些在Git最新快照里存在的对象，可能是未修改(unmodified)，已修改(modified)或暂存（staged）的文件或目录。
- 未标记（untracked）

	未标记对象，即除去已标记对象外，所有对象，比如在工作目录下但是未包含在最新快照里并且不在暂存区（staging area）。

可以想象一下，当第一次克隆一个远程仓库后，仓库下的所有文件都应该是处于已标记（tracked）但是未修改的状态。

![对象状态](http://blog.codingplayboy.com/wp-content/uploads/2017/01/lifecycle.png)

#### 添加对象（git add）

添加版本控制对象的指令是```git add```，比如，我们可以使用如下指令添加一个README.txt文件，当然我们首先需要在仓库目录下，创建该文件（任意方式创建），查看状态：

![untracked file](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-status-untrack.png)

如上图，显示README.txt文件为Untracked file，并且提示:use ```git add``` to track;

然后使用```git add```指令后：

```
git add README.txt
```

再次执行```git status```指令：

![after git add](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-add.png)

如上，出现：Changes to be committed,说明该文件已被标记（tracked）且被暂存,我们可以进行提交了。

当然该指令还可以对目录使用：

```
git add test/
```

如上指令，将添加该test目录及其内所有文件或子目录。

最后，也许我们希望一次添加所有变更，而不是一个一个添加，如下指令，可以实现：

```
git add .
```
我们还需要关注```git add```指令，不只是能添加版本控制对象，还有以下功能：

- 暂存变更（staged）
- 标记冲突已解决（resolved）

> git add指令，更贴切的作用应该是添加新内容（文件/目录/变更）到下一次提交，即将新内容加入最新快照，等待提交；
关于后两点功能，后文有详细介绍。

#### 忽略对象(.gitignore)

有时候，我们并不希望对仓库内所有对象（文件或目录）进行管理，
某些开发依赖或本地调试使用文件和目录，我们不需要在团队间共享，这些文件应该被Git忽略，我们只需要创建一个.gitignore文件，在此文件中列出希望被忽略的文件或目录，如：

![.gitignore](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-igonre.png)

.gitignore文件语法主要如下：

- 以行为单位，一行匹配一个条件
- 空行或以#开头的行会被忽略
- 表达式支持glob格式
- 排除某表达式匹配项在该表达式前加!;

#### 暂存变更(git add)

在添加README.txt文件后，我们可以对其进行修改，如在该文件加入一行文字```http://blog.codingplayboy.com```，然后执行```git status指令```，结果如下：

![git add to staging area](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-vi.png)

发现，除了之前新添加文件的记录，又多了一条记录：```Changes not staged for commit```，表明已标记对象发生变更但是未被暂存，其下面第一第二行是辅助信息，第三行告诉我们，README.txt已经修改（modified）；

随后我们可以使用```git add```指令暂存此次变更，此时，再执行```git status```指令，可以看到：

![git add .](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-add-stage.png)

所有变更都已暂存，等待下一次提交。

##### 查看简短状态信息(git status -s)

上文已经指出，使用```git status```可以查看当前工作目录完整的状态，同时，git还支持使用参数指明查看简短状态信息：

```
git status -s
or 
git status --short
```

如下，其信息比```git status```简洁明了：

![git status -s](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-status-s.png)


#### 查看变更信息(git diff)

前文的```git status```可以查看当前工作目录的状态信息，包括当前分支，变更文件等，但都属于文件层次的信息；
有时我们希望知道：

- 哪些变更未被暂存；
- 哪些变更已暂存，等待提交

```git status```显然是不能告诉我们，因为这是属于```git diff```的使命，如,在README.txt文件中内容添加‘/’：

![git diff](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-diff.png)


，如上图，```git diff```以行为单位，告诉我们所有变更文件哪些行发生变更（增加或删除）。


> ```git diff```比较当前工作目录和暂存区的内容，然后展示哪些文件内容发生变更并且尚未暂存；
同时，其支持额外参数```--staged```（或--cached），该参数指定时，将输出上次提交内容与暂存区内容比较后的的变更，
简言之，```git diff```输出未暂存的变更，```git diff --staged```输出已暂存待提交的变更。

在使用```git add .```暂存变更后，使用```git diff```指令是没用的，而使用```git diff --staged```指令，输出如下：

![git diff --staged](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-diff-staged.png)
 
#### 提交变更（git commit）

所有的变更，最终都需要提交，才能在本地持久化报存，在将所有变更暂存(git add)后，我们就可以进行提交了，相关指令就是：

```
git commit
```
在输入如上指令后，将进入Git commit信息编辑状态：

![git commit](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-commit.png)

我们可以编辑本次提交的备注信息,其中的默认备注信息都以#开头，表明提交时会被忽略。

> git commit指令告诉Git持久化记录（提交）我们暂存区（staging area）中的快照，任何未被暂存的变更，不会被添加进暂存区的快照，仍然保留在当前工作目录，我们可以随后提交。

##### 更详细的提交信息（git commit -v）

除了使用默认的git commit指令，我们还可以添加```-v```参数，在提交信息中显示变更内容，如下：

![git-commit-v](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-commit-v.png)

如上图，和之前的比较，除了基本的提示，还有文件变更内容提示，可以避免某些误提交。

##### 行内输入提交信息（git commit -m）

当然，Git还支持我们使用```-m```参数，指明我们在使用```git commit ```指令时直接填写提交备注信息：

![git-commit-m](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-commit-m.png)

#### 跳过暂存区（git commit -a）

我们知道对于发生变更的对象，我们需要先使用```git add```，暂存变更，再使用```git commit```提交变更；
但是还有一种可以不使用```git add```指令的方式，可以提交变更，就是给```git commit```指令添加```-a```参数：

```
git commit -a
```

**但是需要注意的是，该参数，只能直接提交工作目录中已标记的对象（文件或目录）的变更，对于未标记，如新添加的对象，是无效的。**

#### 查看提交历史（Git log）

本节要介绍的是如何查看之前的提交历史及信息，你应该知道的```git log```指令，
默认地，不带参数时，执行```git log```指令，输出的是当前仓库按逆序排序（最近提交在最前）的提交记录:

![git log](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-log.png)

如上图，每个提交记录包含其SHA-1校验和，提交者用户名，提交日期，提交备注信息。

```git log```指令支持指定许多参数，以过滤输出不同提交记录，下文展开介绍。

##### 指定查看提交记录数（git log -2）

Git支持我们指定数量参数，限定该次查看提交记录数量，如```git log -2```，之后输出最近的两条提交记录。

##### 格式化输出提交记录（git log --pretty）

Git支持我们格式化输出的提交记录信息，使用--pretty参数，其值主要有以下几个：

- oneline: 指定一行输出一条提交记录；
- short: 指定按原格式输出简要信息；
- full: 指定按原格式输出信息；
- fuller: 指定按原格式输出更多信息；
- format:"": 允许指定自定义输出格式，如：

```
git log --pretty=format:"%h - %an, %ar : %s"
```

![git log --pretty=format](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-log-pretty-format.png)

##### 查看简要提交变更（git log --stat）

```git log --stat```输出如下：

![git log --stat](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-log-stat.png)

除了输出基础提交信息，后面还输出了本次提交的简要变更信息：变更了多少文件，及每个文件变更的行数，并在最后输出总结数据。

##### 查看详细提交变更（git log -p）

相对于```git log --stat```输出简要提交变更信息，我们可以指定-p参数：```git log -p```，输出提交变更的详细内容，如：

![git-log-p](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-log-p.png)

##### 更多参数说明

|参数|说明|
|-------|----|
|-p|显示详细提交变更|
|--stat|显示简要提交变更|
|--shortstat|在--stat参数输出基础上只输出修改，新增或删除的行|
|--name-only|显示提交时发生变更的文件名|
|--name-status|显示提交时发生变更的文件名，并显示变更类型（删除，新增，或修改）|
|--abbrev-commit|只显示提交记录SHA-1校验和的前几个字符|
|--relative-date|显示简要提交日期(如, “2 weeks ago”) |
|--graph|显示分支提交历史的ASCII图|
|--pretty|支持预定义输出格式，详细说明见上文|

##### 过滤提交历史输出

Git支持我们在输出历史记录时，添加多种过滤条件，最简单的比如```-<num>```参数条件，指定输出最近的若干条提交记录，还有诸如提交时间，提交作者等条件。

- --since/--after: ```git log --since=2.weeks```

	输出两周内的提交记录，参数值还可以形如"2016-01-15"，"2 years 1 day 10 minutes ago"

- --until/--before: ```git log --until=2016-01-15```

- --grep: ```git log --grep=README```

	可以指定关键字，只有提交信息中存在关键字才会输出

- --author: 过滤输出指定提交作者的记录
- --commiter: 输出满足指定commiter的提交记录
- -S: ```git -log -Swebpack```

	输出提交内容（代码或文件或目录）包含指定字符串的记录

#### 删除对象（git rm）

很多时候，我们也会需要从Git仓库中删除某些对象，```rm```就是删除文件或目录的指令，但是需要特别强调的是，该指令只是将某对象从当前工作目录删除，如：

![rm file](http://blog.codingplayboy.com/wp-content/uploads/2017/01/rm.png)

使用```rm```后,当前状态是"Changes not staged for commit:"此次变更未被暂存和提交。

若你需要将某对象从已标记文件或暂存区删除，则需要使用```git rm```指令，如下：

![git rm](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-rm.png)

使用```git rm```后，变更会被暂存。

##### 强制删除（git rm -f）

当我们发生某次变更，且将其添加到暂存区（index索引），我们只使用```git rm```指令是不行的：

![git rm -f](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-rm-f.png)

我们必须加上```-f```参数，指明强制删除。

#### 缓存变更（git rm --cached）

有时候，我们在某次变更添加了某文件（甚至可能已经添加到暂存区），但是暂时不需要提交，又不想直接删除它，即只在工作目录存在，而不将其放入暂存区，只需要添加```--cached```参数，如修改README.txt同时，新增了test.txt文件，并且暂存了变更，之后提交时我们不希望此次提交test.txt，但又不希望删除它，则在提交前使用```git rm --cached test.txt```：

![git rm --cached](http://blog.codingplayboy.com/wp-content/uploads/2017/01/git-rm-cached.png)


#### 移动对象（git mv）

通常，我们也许需要移动或重命名某文件或目录，Git有```mv```指令，还要一个更方便的指令```git mv```，如：

```
git mv a.txt b.txt
```


另一方面，重命名或移动某文件或目录，这两个操作对于Git来说是没有太大区别的，比如上面的重命名文件操作等效于：

```
mv a.txt b.txt
git rm a.txt
git add b.txt
```

我们可以看到```git mv```指令是在```mv```指令操作的基础上暂存此次操作的变更，而```mv```只是一个重命名或移动指令，不涉及版本控制流程。


到此，Git的基本使用，已经介绍完成，不过，本篇讲解的基础操作，都是在计算机本地进行的版本控制，并没有同步到服务器，那么下一篇的主题就出来了：Git如何与远程服务器进行协同工作。

