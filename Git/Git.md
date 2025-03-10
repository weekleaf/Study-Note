# Git

**https://git-scm.com/book/zh/v2**

## 2. Git基础

### 2.1 获取git仓库

#### 初始化仓库

```
$ git init
```

#### 克隆现有仓库

```
$ git clone
```

### 2.2 记录更新

#### 检查当前文件状态

```
$ git status
```

状态简览：

```console
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

新添加的未跟踪文件前面有 `??` 标记，新添加到暂存区中的文件前面有 `A` 标记，修改过的文件前面有 `M` 标记。 输出中有两栏，**左栏指明了暂存区的状态，右栏指明了工作区的状态**。例如，上面的状态报告显示： `README` 文件在工作区已修改但尚未暂存，而 `lib/simplegit.rb` 文件已修改且已暂存。 `Rakefile` 文件已修改，暂存后又作了修改，因此该文件的修改中既有已暂存的部分，又有未暂存的部分。

跟踪新文件或暂存已修改文件：

```
$ git add
```

`git add` 命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

#### 查看修改

```
$ git diff
```

请注意，git diff 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂存了所有更新过的文件，运行 `git diff` 后却什么也没有，就是这个原因。

若要查看已暂存的将要添加到下次提交里的内容，可以用 `git diff --staged` 命令。 这条命令将比对已暂存文件与最后一次提交的文件差异。

#### 提交更新

```
$ git commit
```

 在此之前，请务必确认还有什么已修改或新建的文件还没有 `git add` 过， 否则提交的时候不会记录这些尚未暂存的变化。

这样会启动你选择的文本编辑器来输入提交说明。

另外，你也可以在 `commit` 命令后添加 `-m` 选项，将提交信息与命令放在同一行，如下所示：

```
$ git commit -m ""
```

Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤：

```
$ git commit -a
```

#### 移除文件

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），**然后提交**。 可以用 `git rm` 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

```
$ git rm
```

如果仅手工删除文件，运行 `git status` 时就会在 “Changes not staged for commit” 部分。

如果要删除之前修改过或已经放到暂存区的文件，则必须使用强制删除选项 `-f`：

```
$ git rm -f
```

另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 `.gitignore` 文件，不小心把一个很大的日志文件或一堆 `.a` 这样的编译生成文件添加到暂存区时，这一做法尤其有用。 为达到这一目的，使用 `--cached` 选项：

```
$ git rm --cached
```

`git rm` 命令后面可以列出文件或者目录的名字。

#### 移动或重命名文件

```
$ git mv README.md README
```

其实，运行 `git mv` 就相当于运行了下面三条命令：

```console
$ mv README.md README
$ git rm README.md
$ git add README
```

### 2.3 查看提交历史

```
git log
```

参数见文档。

### 2.4 撤销操作

#### 撤销操作

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 `--amend` 选项的提交命令来重新提交：

```console
$ git commit --amend
```

这个命令会将暂存区中的文件提交。 如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令）， 那么快照会保持不变，而你所修改的只是提交信息。

你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：

```console
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```

#### 取消暂存文件

你已经修改了两个文件并且想要将它们作为两次独立的修改提交， 但是却意外地输入 `git add *` 暂存了它们两个，使用 `git reset HEAD <file>…` 来取消暂存，我们可以这样来取消暂存 `CONTRIBUTING.md` 文件：

```console
$ git reset HEAD CONTRIBUTING.md
```

#### 撤销对文件的修改

修改了文件但未在暂存区域时（CONTRIBUTING.md）：

```console
$ git checkout -- CONTRIBUTING.md
```

## 3. Git 分支

### 3.1 分支简介

#### 分支创建

创建一个 testing 分支：

```console
$ git branch testing
```

git有一个特殊指针`HEAD`指向当前所在的本地分支。

#### 分支切换

切换到新创建的 `testing` 分支：

```console
$ git checkout testing
```

这样 `HEAD` 就指向 `testing` 分支了。

分支切换会改变你工作目录中的文件，在切换分支时，一定要注意你工作目录里的文件会被改变。 如果是切换到一个较旧的分支，你的工作目录会恢复到该分支最后一次提交时的样子。

### 3.2 分支的新建和合并

同时做两件事：

```console
$ git checkout -b iss53
```

相当于

```console
$ git branch iss53
$ git checkout iss53
```

#### 分支合并

```console
$ git checkout master
$ git merge hotfix
```

将`hotfix`分支合并到`master`

当你试图合并两个分支时， 如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候， 只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧——这就叫做 **“快进（fast-forward）”**。

![](./basic-branching-4.png)

#### 删除分支

当修改合并完毕，便可以删除不再需要的分支：

```console
$ git branch -d hotfix
```

#### 一次合并提交

当合并不能快进时，比如：

![](./basic-merging-1.png)

`master` 分支所在提交并不是 `iss53` 分支所在提交的直接祖先，Git 不得不做一些额外的工作。 出现这种情况的时候，Git 会使用两个分支的末端所指的快照（`C4` 和 `C5`）以及这两个分支的公共祖先（`C2`），做一个简单的三方合并。

Git 将此次三方合并的结果做了一个新的快照并且自动创建一个新的提交指向它。 这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。

![](./basic-merging-2.png)

#### 遇到冲突时的分支合并

如果你在两个不同的分支中，对**同一个文件的同一个部分**进行了不同的修改，Git 就没法干净的合并它们，合并它们的时候就会产生合并冲突，因为不知道你需要保留哪个版本，执行`git merge`，此时 Git 做了合并，但是没有自动地创建一个新的合并提交。 Git 会暂停下来，等待你去解决合并产生的冲突。

你需要打开包含冲突的文件手动解决冲突。

在你解决了所有文件里的冲突之后，对每个文件使用 `git add` 命令来将其标记为冲突已解决。 一旦暂存这些原本有冲突的文件，Git 就会将它们标记为冲突已解决。

如果你对结果感到满意，并且确定之前有冲突的文件都已经暂存了，这时你可以输入 `git commit` 来完成合并提交。

### 3.3 分支管理

直接运行`git branch`，会得到当前所有分支的一个列表：

```console
$ git branch
  iss53
* master
  testing
```

`*` 字符：它代表现在检出的那一个分支（也就是说，当前 `HEAD` 指针所指向的分支）。

`-v`参数可查看每个分支最后一次提交：

```console
$ git branch -v
```

`--merged` 与 `--no-merged`可以过滤这个列表中已经合并或尚未合并到当前分支的分支：

```console
$ git branch --merged
$ git branch --no-merged
```

使用`git branch -d`删除未合并的分支会失败，如果真的想要删除分支并丢掉那些工作，可以使用 `-D` 选项强制删除它。

### 3.5 远程分支

通过 `git ls-remote <remote>` 来显式地获得远程引用的完整列表， 或者通过 `git remote show <remote>` 获得远程分支的更多信息。

远程与本地的分支操作详见[Git - 远程分支](https://git-scm.com/book/zh/v2/Git-分支-远程分支)

#### 抓取

与给定的远程仓库同步数据，运行 `git fetch <remote>` 命令，这个命令查找服务器，从中抓取本地没有的数据，并且更新本地数据库，移动 `<remote>/master` 指针到更新之后的位置。

抓取新的远程分支只会有一个新的不可修改的`<remote>/<branch>`指针，而不像`clone`会有`<remote>/<branch>`和`<branch>`，一般是`origin/master`，新抓取的分支可以运行 `git merge <remote>/<branch>` 将这些工作合并到当前所在的分支，也可以将其建立在远程跟踪分支之上：

```console
$ git checkout -b serverfix origin/serverfix
```

这会给你一个用于工作的本地分支，并且起点位于 `origin/serverfix`。

#### 推送

公开分享一个分支，将其推送到有写入权限的远程仓库上：`git push <remote> <branch>`

例如：

```console
$ git push origin serverfix
```

`serverfix`本质上是`refs/heads/serverfix:refs/heads/serverfix`，简化一点是`serverfix:serverfix`，即是将`refs/heads/serverfix`替换成`refs/heads/serverfix`，推送本地的 `serverfix` 分支，将其作为远程仓库的 `serverfix` 分支。

通过这种格式来推送本地分支到一个命名不相同的远程分支。 如果并不想让远程仓库上的分支叫做 `serverfix`，可以运行 `git push origin serverfix:awesomebranch` 来将本地的 `serverfix` 分支推送到远程仓库上的 `awesomebranch` 分支。

#### 跟踪分支

当克隆一个仓库时，它通常会自动地创建一个跟踪 `origin/master` 的 `master` 分支，或者：

```console
$ git checkout -b serverfix origin/serverfix
```

新创建的`master`和`serverfix`分支就是 `origin/master` 和`origin/serverfix`的跟踪分支。

跟踪分支与远程分支指向同一个提交。

一般创建跟踪分支，可以使用`--track`：

```console
$ git checkout --track origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支， 你可以在任意时间使用 `-u` 或 `--set-upstream-to` ：

```console
$ git branch -u origin/serverfix
```

查看设置的所有跟踪分支，可以使用  `-vv` 选项:

```console
$ git branch -vv
```

#### 拉取

`git fetch` 命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。 它只会获取数据然后让你自己合并。

`git pull` 在大多数情况下它的含义是一个 `git fetch` 紧接着一个 `git merge` 命令。

如果有一个像之前章节中演示的设置好的跟踪分支，不管它是显式地设置还是通过 `clone` 或 `checkout` 命令为你创建的，`git pull` 都会查找当前分支所跟踪的服务器与分支， 从服务器上抓取数据然后尝试合并入那个远程分支。

#### 删除远程分支

```console
$ git push origin --delete serverfix
```

### 3.6 变基

对于整合分叉的提交，先前我们使用`merge`将最新快照以及它们共同的祖先进行合并，生成新的快照，详见[前文](#一次合并提交)，还有一种方法：你可以提取在`C4`中引入的补丁和修改，然后在`C3`的基础上应用一次。 在 Git 中，这种操作就叫做 **变基（rebase）**。

```console
$ git checkout experiment
$ git rebase master
```

![](./basic-rebase-1.png)

它的原理是首先找到这两个分支（即当前分支 `experiment`、变基操作的目标基底分支 `master`） 的最近共同祖先 `C2`，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件， 然后将当前分支指向目标基底 `C3`, 最后以此将之前另存为临时文件的修改依序应用。

![](./basic-rebase-3.png)

此时可以直接快进合并了。

```console
$ git checkout master
$ git merge experiment
```

使用变基的结果与前文用`merge`合并结果一致，只不过提交历史不同。

使用 `git rebase <basebranch> <topicbranch>` 命令可以直接将主题分支 （即本例中的 `server`）变基到目标分支（即 `master`）上。 这样做能省去你先切换到 `server` 分支，再对其执行变基命令的多个步骤。

```console
$ git rebase master server
```

#### 变基的风险

**如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基。**

详见[Git - 变基](https://git-scm.com/book/zh/v2/Git-分支-变基)

总的原则是，只对尚未推送或分享给别人的本地修改执行变基操作清理历史， 从不对已推送至别处的提交执行变基操作，这样，你才能享受到两种方式带来的便利。
