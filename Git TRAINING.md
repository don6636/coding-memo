[TOC]

# Git TRAINING

## Git Basic

- **cd** 命令 [忽略大小写]

  `cd d:` 移动到d盘

  `cd \repo` 移动到repo目录

  `cd d:\repo` 移动到d盘repo目录

- <a name="git-config"></a>**用户信息**

  `git config --global user.name "Shaun"`

  `git config --global user.email shaunu11@gmail.com`

  **_ps:_** 若想针对特定项目使用不同的用户名称和邮件地址时，可以在项目目录下运行不含 `--global` 选项的命令来配置。

- **检查配置信息**

  所有配置：`git config --list`

  单项配置：`git config <key>`

  **_eg:_** `git config user.name`

- <a name="git-help"></a>**获取帮助** `git help <verb>`

  **_eg:_** `git help config`



### 获取Git仓库

- <a name="git-init"></a>**在现有目录中初始化仓库** `git init`

  若仓库目录已经存在文件，可以通过 `git add` 命令来实现对指定文件的跟踪，然后执行 `git commit` 提交 `git add a.txt` [使用文件或目录路径作为参数；若参数是目录路径，将递归跟踪该目录下的所有文件]

  `git commit -m 'initial'`

- <a name="git-clone"></a>**克隆现有仓库** `git clone [url]`

  `git clone https://github.com/libgit2/libgit2` 默认本地仓库名称为 _libgit2_

  `git clone https://github.com/libgit2/libgit2 localrepo` 自定义本地仓库名称为 _localrepo_

### 记录每次更新到仓库

- <a name="git-status"></a>**查看当前文件状态** `git status`

- **暂存已修改文件** [已跟踪文件内容发生了变化，但还未放到暂存区]

  <a name="git-add"></a>`git add` [是个多功能命令]

  - 跟踪新文件
  - 已跟踪文件放到暂存区
  - 合并时把冲突文件标记为已解决状态

  **_ps:_** 若已修改文件在提交之前又做了修订，需要重新运行 `git add` 命令把最新版本暂存起来，否则提交的将是最后一次运行 `git add` 命令时的那个版本。[ **？？** 当前只有1个未暂存文件时，输入 `git add` 命令后按 **Tab** 键可以快速填充文件名（Tab补全代码）]

- **状态简览** `git status -s 或 git status --short`

  标记含义：

  **??** 新添加的未跟踪文件 [新文件，从未 _add_ 过]

  **A ** 新添加到暂存区的文件 [ _add_ 之后没改过，但还未 _commit_ 过]

   **M** [右红] 文件被修改但还未放入暂存区

  **M ** [左绿] 文件被修改并放入了暂存区 [曾 _commit_ 过，否则只会显示 **A ** ]

  **AM** [文件放入暂存区后又被修改，新的修改还未放入暂存区，从未 _commit_ 过]

  **MM** [文件放入暂存区后又被修改，新的修改还未放入暂存区，曾 _commit_ 过]

- **忽略文件**

  ==// TODO==



- <a name="git-diff"></a>**查看已暂存和未暂存的修改**

  尚未暂存：`git diff`

  已暂存：`git diff --staged`

- <a name="git-commit"></a>**提交更新** `git commit` [会启动默认或配置的文本编辑器以输入提交说明]

  便捷方式 `git commit -m 'fixed bugs'`

  **_ps:_** `git commit` 前记得使用 `git status` 查看还有哪些文件没有 `git add` 过。

- **跳过使用暂存区域** [把所有 **已跟踪过** 的文件暂存起来一并提交] 

  `git commit -a -m 'skip adds'` 或 `git commit -am 'skip adds'`

- <a name="git-rm"></a>**移除文件** `git rm delete.txt`

  若删除之前修改过，则必须要用强制删除选项 `git rm -f forcedelete.txt`

  若只想移除暂存区的文件但是保留工作区的文件 [适用于忘记添加.gitignore文件等情况]

  `git rm --cached remain.txt`

  [可删除文件或目录]

  **_eg:_** `git rm log/\*.log` 删除 _log/_ 目录下所有扩展名为 _.log_ 的文件 [ \ 为转义字符]

  ​      `git rm \*a` 删除以 _a_ 结尾的所有文件

- <a name="git-mv"></a>**移动文件(重命名文件)** `git mv file_from file_to`

  **_ps:_** 若不是在Git中进行的重命名操作，要记得在提交前删除老的文件名 `git rm oldfilename`，再添加新的文件名 `git add newfilename` 。



### 查看提交历史<a name="git-log"></a>

`git log`

`git log -2` 仅显示最近两次提交信息 [同理3次、4次，可与下列命令组合使用]

`git log -p` 显示每次提交的内容差异 [ `git log -p -3` ]

`git log --stat` 查看每次提交的简略统计信息 [ `git log --stat -2` ]

`git log --graph` 提交历史图形化展示 [ `git log --graph -2` ]

`git log --oneline` 简化输出提交历史[缩略SHA-1值]

`git log --pretty=oneline` 指定提交历史的展示方式 [oneline(完整SHA-1值)、short、full、fuller、format]

`git log --pretty=format:"%h - %an, %ar : %s"` 定制记录格式

| 选项  | 说明                                        |
| :---: | ------------------------------------------- |
| `%H`  | 提交对象（commit）的完整哈希字串            |
| `%h`  | 提交对象的简短哈希字串                      |
| `%T`  | 树对象（tree）的完整哈希字串                |
| `%t`  | 树对象的简短哈希字串                        |
| `%P`  | 父对象（parent）的完整哈希字串              |
| `%p`  | 父对象的简短哈希字串                        |
| `%an` | 作者（author）的名字                        |
| `%ae` | 作者的电子邮件地址                          |
| `%ad` | 作者修订日期（可以用 --date= 选项定制格式） |
| `%ar` | 作者修订日期，按多久以前的方式显示          |
| `%cn` | 提交者（committer）的名字                   |
| `%ce` | 提交者的电子邮件地址                        |
| `%cd` | 提交日期                                    |
| `%cr` | 提交日期，按多久以前的方式显示              |
| `%s`  | 提交说明                                    |

- `git log` 的常用选项表

| 选项              | 说明                                                       |
| ----------------- | ---------------------------------------------------------- |
| `-p`              | 按补丁格式显示每个更新之间的差异                           |
| `--stat`          | 显示每次更新的文件修改统计信息                             |
| `--shortstat`     | 只显示 `--stat` 中最后的行数修改添加移除统计               |
| `--name-only`     | 仅在提交信息后显示已修改的文件清单                         |
| `--name-status`   | 显示新增、修改、删除的文件清单                             |
| `--abbrev-commit` | 仅显示 SHA-1 的前几个字符（默认7个），而非所有的 40 个字符 |
| `--relative-date` | 使用较短的相对时间显示（比如，“2 weeks ago”）              |
| `--graph`         | 显示 ASCII 图形表示的分支合并历史                          |
| `--pretty`        | 使用其他格式显示历史提交信息                               |

- **限制输出长度（筛选）**

  `git log --since=2.weeks`

| 选项                   | 说明                               |
| ---------------------- | ---------------------------------- |
| `-(n)`                 | 仅显示最近的 n 条提交              |
| `--since` , `--after`  | 仅显示指定时间之后的提交           |
| `--until` , `--before` | 仅显示指定时间之前的提交           |
| `--author`             | 仅显示指定作者相关的提交           |
| `--committer`          | 仅显示指定提交者相关的提交         |
| `--grep`               | 仅显示含指定关键字的提交           |
| `-S`                   | 仅显示添加或移除了某个关键字的提交 |



### 撤销操作

`git commit --amend` [该命令会提交暂存区的文件并修改上一条提交信息]

- **取消暂存的文件** `git reset HEAD reset.txt`

- **撤销对未暂存文件的修改** `git checkout -- temp.txt`



### 远程仓库的使用

- <a name="git-remote"></a>**查看远程仓库** `git remote`

  `git remote -v` [显示所有远程仓库简写及对应URL]

- **添加远程仓库** `git remote add <shortname> <url>`

  `git remote add al https://github.com/alpha/alphabet` [ _al_ 可以在命令行中代替整个URL]

  **_eg:_** `git fetch al`

- **从远程仓库中抓取与拉取** `git fetch [remote-name]`

  `git fetch origin` [抓取远程仓库中所有本地没有的数据，但并不会自动合并或修改当前的工作]

  `git pull origin` [拉取远程跟踪分支更新然后自动尝试合并到当前分支]

- **推送到远程仓库** `git push [remote-name] [branch-name]`

  `git push origin master` [推送master分支至远程仓库]

  **_ps:_** 只有拥有写入权限，并且之前没人推送过时，该命令才会生效。否则必须 **先拉取更新**，合并后才能推送。

- **远程仓库详情** `git remote show [remote-name]`

  `git remote show origin`

- **移除与重命名远程仓库**

  `git remote rename al alpha` [重命名远程仓库简写名 _al_ 为 _alpha_，远程分支名字也会改变]

  `git remote rm al` [移除远程仓库 _al_ ]



### 标签<a name="git-tag"></a>

通常用于标记发布节点（v1.0等）

- **列出标签** `git tag`

  筛选 `git tag -l 'v0.1*'` [ _v0.1_ 系列标签]

- **创建标签**

  - 附注标签（annotated）

    `git tag -a v0.1 -m 'beta version 0.1'` [ _-m_ 指定了一条标签存储信息]

  - 轻量标签（lightweight）

    `git tag v0.1-lw`

  **_ps:_** `git show v0.1` [查看标签信息]

- **对过去的信息打标签** `git tag -a v0.0.9 -m 'tag former' f6db651` 或 `git tag v0.0.8-lw 276d507`

- **共享标签**

  `git push origin v0.1` [推送指定标签至远程仓库]

  `git push origin --tags` [推送所有不在远程仓库的标签]

- **从标签中创建分支** `git checkout -b [branchname] [tagname]`

  `git checkout -b version0.1 v0.1` [从标签 _v0.1_ 处创建分支 _version0.1_ ]



### Git别名

通过 `git config` 为命令设置别名 [ `--global` 选项用于全局设置]

`git config alias.co checkout` [ _checkout_ 别名 _co_ ]

`git config --global alias.unstage 'reset HEAD --'`

[ `git unstage filename` 等价于 `git reset HEAD -- filename` ]

`git config --global alias.last 'log -1 HEAD'` [ `git last` 轻松查看最后一次提交]

`git config --global alias.visual '!gitk'` [为外部命令设置别名，前加 _!_ ]

`git config --global --unset alias.last` 删除 *last* 别名

`git config --global --remove-section alias` 删除所有alias，即删除 *.gitconfig* 文件中[alias]小节

## Git分支

### 分支管理<a name="git-branch"></a>

- **创建分支** `git branch develop`

  `git log --oneline --decorate` [查看各个分支当前所指对象]

- <a name="git-checkout"></a>**切换分支** `git checkout develop` [此时HEAD指针指向develop分支]

  **_ps:_** 切换分支会改变工作目录里的文件。

  `git log --oneline --decorate --graph --all` [输出提交历史、各分支指向及分支分叉情况]

- **创建并同时切换分支** `git checkout -b feature-login`

- <a name="git-merge"></a>**合并分支** `git merge feature-login` [合并 _feature-login_ 分支至当前分支]

  若当前分支是要合并分支的直接Parent，合并时会采用 **fast-forward** 模式；否则会对两个分支末端所指快照以及两个分支的共同Parent快照进行三方合并，并生成一个新的快照（合并提交，有不止一个父提交）。

- **删除分支** `git branch -d feature-login` [删除 _feature-login_ 分支]

- **解决合并冲突** [在解决了所有冲突后，对每个文件使用 `git add` 命令来将其标记为冲突已解决]

  `git mergetool` [启动图形化工具]

- **分支管理**

  `git branch` [无参数时会展示分支列表，_*_ 表示当前所在分支]

  `git branch -v` [查看每个分支最后一次提交]

  `git branch --merged` [查看已合并到当前分支的分支]

  `git branch --no-merged` [查看尚未合并到当前分支的分支]



### 远程分支

- `git ls-remote [remote-name]` [获取远程引用完整列表]

- **远程跟踪分支** 形如 `[remote-name]/[branch-name]` [origin/master]

  _oringin_ 是 _clone_ 时默认的远程仓库名称，运行 `git clone -o boo`，则默认远程分支名称为 _boo/master_。

- <a name="git-fetch"></a>**同步远程跟踪分支** `git fetch origin` [移动 _origin/master_ 指针至更新的位置]

- <a name="git-push"></a>**推送** `git push origin hotfix` [推送hotfix分支至远程仓库，等价于 `git push origin hotfix:hotfix` ]

  `git push origin hotfix:develop` [将本地hotfix分支推送至远程develop分支]

- **跟踪分支** [也叫上游分支，是与远程分支有直接关系的本地分支]

  `git checkout -b hotfix origin/hotfix` [fetch新的远程跟踪分支后，可使用此命令创建本地跟踪分支（可与远程分支不同名 `git checkout -b hf origin/hotfix`），或使用 `git merge origin/hotfix` 合并到当前工作分支]

  **_ps:_** `git checkout --track origin/hotfix` [快捷方式]

  `git branch -u origin/hotfix` 或 `git branch --set-upstream-to origin/hotfix` [<u>设置已有本地分支跟踪远程分支或修改正在跟踪的上游分支</u>]

  ***ps:*** 当设置好跟踪分支后，可通过 `@{upstream}` 或 `@{u}` 快捷方式来引用它。
  
  `git branch -vv` [查看设置的所有跟踪分支以及它们的领先、落后信息。此命令并未连接服务器，只会显示最后一次从服务器抓取的数据；若想要统计最新的领先落后信息，需要先抓取所有的远程仓库 `git fetch --all`，然后再运行此命令]
  
  ```bash
  $ git branch -vv
  * dev    a3250d0 [origin/dev: ahead 2, behind 1] refine timer
    master 01d3d4f [origin/master] add files
  ```
  
  此时位于*dev*分支上，它正在跟踪 `origin/dev` 并且领先***2***落后***1***，意味着服务器上有***1***次提交还未合并入本地同时本地有***2***次提交还没有推送。*master*分支正在跟踪 `origin/master` 并且是最新的。



- <a name="git-pull"></a>**拉取** `git pull`

  `git fetch` 命令从服务器抓取本地没有的数据，但只会更新跟踪分支，只有手动将跟踪分支 `git merge` 到本地分支后才会修改工作目录中的内容。而 `git pull` 命令在大多数情况下的含义都是 `git fetch` 与 `git merge` 的组合，执行拉取命令会查找当前分支所跟踪的远程分支，并从服务器抓取数据然后尝试合并入本地分支。

  ***ps:*** 通常建议显式地分别使用*fetch*和*merge*组合命令来避免直接使用*pull*命令可能带来的麻烦。



- **删除远程分支**

  假设*hotfix*分支上的工作已经完成并且也已经合并入*master*等远程稳定代码分支，可以执行

  `git push origin --delete hotfix` 命令从服务器上删除*hotfix*分支。



### Rebase<a name="git-rebase"></a>

> 在Git中整合来自不同分支的修改主要由两种方法：**merge** 以及 **rebase**。

- 



## Git第三方托管的选择

 https://git.wiki.kernel.org/index.php/GitHosting 

- 协议：*本地协议(Local)*、*HTTP协议(Smart、Dumb)*、*SSH(Secure Shell)协议*及*Git协议*。



## 分布式Git

### 分布式工作流程

- 集中式工作流



- 集成管理者工作流



- 司令官与副官工作流



## GitHub





## Git工具

### 选择修订版本——查找历史提交信息

- 简短的SHA-1：通过SHA-1值得前几个字符（不少于4个，唯一）获得对应提交的详情

  如下输出的都是同一次提交详情

  `git show f7a74f0c32b70b3e48f9a73d87b5efaa706856bd`

  `git show f7a74f0c32b70b3e48f9`

  `git show f7a74f0`

- 分支引用

  还可以通过分支的引用来代替对应的提交对象或SHA-1值，假设当前master引用指向*f7a74f0*，那么：

  `git show master` 和<u>简短的SHA-1</u>中的3条命令是等价的；另外，还可以通过`git rev-parse`命令解析引用对应的完整SHA-1值，如 `git rev-parse master` 输出 *f7a74f0c32b70b3e48f9a73d87b5efaa706856bd*。

- <a name="git-reflog"></a>**引用日志**

> 使用Git时，它会在后台保存一个引用日志（reflog），记录了最近几个月**HEAD**和**分支引用**所指向的历史。
>
> 每当**HEAD**指针的位置发生变化时，Git就会在reflog中存储一个message：commit(initial/merge/amend)、checkout、merge、reset等。通过这些记录就能获取之前的提交历史，并用**HEAD@{n}**标记每一条，其中**HEAD@{0}**是当前**HEAD**指针所在位置，依次递增到最旧的记录。
>
> ***ps:*** reflog只存在于本地仓库，一个记录用户在仓库里做过什么的日志。新clone仓库的reflog是空的；也不会和copy来的仓库中的reflog相同。

如下是一个不严谨的示例：

```bash
$ git reflog
f7a74f0 (HEAD -> master) HEAD@{0}: reset: moving to HEAD~
883fa9b HEAD@{1}: commit: blame test
043c3af HEAD@{2}: commit (merge): conflict resolved
037b2ee HEAD@{3}: checkout: moving from develop to master
03f2637 (develop) HEAD@{4}: commit: conglict test
037b2ee HEAD@{5}: checkout: moving from master to develop
037b2ee HEAD@{6}: merge develop: Fast-forward
037b2ee HEAD@{7}: merge feature-login: Merge made by the 'recursive' strategy.
779b730 HEAD@{8}: commit: switch branches
f85c196 HEAD@{9}: checkout: moving from develop to master
6c88f05 HEAD@{10}: commit: checkout branch develop
074edf4 (tag: v0.1) HEAD@{11}: commit (amend): modified e.txt
12e6830 HEAD@{12}: commit: modified files
1792a76 HEAD@{13}: commit (initial): add files
```

***ps:***  同样也可以使用 `git show HEAD@{n}` 来输出具体的历史提交信息；

- 或者使用 `git show master@{yesterday}` 、`git show HEAD@{2.months.ago}` 之类的语法输出一定时间前的历史记录（只限于还在reflog中的记录，==无法查找更久之前的？==）；
- 另外，还可以执行 `git log -g` 来输出类似于 `git log` 格式的reflog：

```bash
$ git log -g -1
commit f7a74f0c32b70b3e48f9a73d87b5efaa706856bd (HEAD -> master)
Reflog: HEAD@{0} (Shaun <shaunu11@gmail.com>)
Reflog message: reset: moving to HEAD~
Author: Shaun <shaunu11@gmail.com>
Date:   Thu Dec 5 14:39:01 2019 +0800

    modify remained file
```



- 祖先引用

  - **HEAD^** [表示一个引用的父提交]
  - **043c3af^2** [表示*043c3af*的第二父提交，其中*043c3af*必须是一个合并提交（拥有多个父提交）]
  - **HEAD~** [等价于后缀单个 **^** 符号的形式]
  - **HEAD~2** [表示一个引用的父提交的父提交，即祖父提交，等价于 **HEAD^^** ]
  - **HEAD~2^2** [表示一个引用的祖父提交的第二父提交，其中该引用的祖父提交必须是一个合并提交]

  现在，假设有以下一部分提交历史：

  ```bash
  $ git log --graph --oneline -5
  * f7a74f0 (HEAD -> master) modify remained file
  * 039edf7 stash test
  *   043c3af conflict resolved
  |\
  | * 03f2637 (develop) conflict test
  * | 1325754 master conflict
  |/
  ```

  则：

  - **f7a74f0^** 指向 **039edf7**
  - **043c3af^2** 指向 **03f2637**
  - **f7a74f0~** 指向 **039edf7**
  - **f7a74f0~2** 指向 **043c3af**
  - **f7a74f0^^** 指向 **043c3af**
  - **f7a74f0~2^2** 指向 **03f2637**

- **提交区间**
  - 双点
  
    选出在一个分支中而不在另一个分支中的提交。
  
    例如查看dev分支还有哪些提交尚未合并入master分支：
  
    `git log master..dev`
  
    另一个常用场景是查看即将推送到远端的提交：
  
    `git log origin/master..HEAD`（等价于 `git log origin/master..`）
  
    ***ps:*** *Git*使用*HEAD*来代替留空的一边。
  
  - 多点
  
    选出不在一个分支而在其它分支中的提交（可以指定超过两个分支）。
  
    例如以下命令是等价的，输出在refB分支而不在refA分支中的提交：
  
    `git log refA..refB`
  
    `git log ^refA refB`
  
    `git log refB --not refA`
  
    该语法还能筛选两个以上的引用：
  
    `git log refA refB ^refC`
  
    `git log refA refB --not refC`
  
    ***ps:*** 前缀 **^** 符号或 **--not** 选项指明要排除的分支。
  
  - 三点
  
    选出不同时被两个分支包含，只包含在其中一个的提交。
  
    `git log master...dev`
  
    另外，常用选项 **--left-right** 会以 **<** 和 **>** 标记出每个提交是属于 **...** 左侧的还是右侧的分支：
  
    ```bash
    $ git log master...dev --left-right --oneline
    < f7a74f0 (HEAD -> master) confict resolved
    < 039edf7 merge test
    > 043c3af feature test
    ```



### 储藏与清理<a name="git-stash"></a>

> 情景：在一个功能分支上已经工作了一段时间，而此时必须要切换到另一个分支修复bug，但是又不想仅仅因为过会儿就要切回来而 为做了一半的工作创建一次提交。此时就可以使用 `git stash` 命令解决问题。
>
> `git stash` 会处理工作目录，将未完成的修改保存到一个栈上，之后可以在任何时候重新应用这些改动。

- 储藏工作
  - 进入项目改动几个文件，并暂存其中一个文件，然后执行 `git stash` 或 `git stash save` 命令将这些改动储藏起来，接下来就能切换分支而不丢失工作了；
  - 查看存储列表 `git stash list`，每条记录用 **stash@{n}** 标记；
  - 重新应用改动 `git stash apply`，默认应用*stash@{0}*，指定应用 `git stash apply stash@{n}`
  - 改动被重新应用后，之前暂存的文件并不会被重新暂存，可以使用 **--index** 选项恢复到和原来完全相同的状态 `git stash apply --index`；
  - 尝试应用暂存的工作并不会从栈上移除存储记录，可以使用 `git stash drop stash@{n}` 命令来移除；
  - 也可以运行 `git stash pop` 来应用存储并同时从栈中移除储藏记录（同样支持 **--index** 选项）。



### 搜索<a name="git-grep"></a>

> 帮助在源代码甚至是项目的老版本中的任意文件中查找字符串或正则表达式。

- **-n** 选项返回匹配结果在文件中的行号：

  查找*change*字符串并标注行号 `git grep -n change`

- **--count** 选项输出不同文件中的关键字匹配数量：`git grep --count change`

- **-p** 选项输出匹配行所属的段落、方法或函数：

  查找*change*字符串在*txt*文件中的所属 `git grep -p change *.txt`





### 重写历史





### 重置揭密<a name="git-reset"></a>





### 高级合并





### 交互式暂存 [见ProGit p.208]



### 签署工作 [见ProGit p.217]



### 子模块 [见ProGit p.278]<a name="git-submodule"></a>



### 打包 [见ProGit p.297]



### 替换 [见ProGit p.300]



### 凭证存储 [见ProGit p.309]



## 自定义Git

### 配置Git

- 客户端基本配置

  - *core.editor*　`git config --global core.editor emacs` [终端编辑器]

  - *commit.template*　`git config --global commit.template ~/.gitmessage.txt` [提交模板，配置模板路径，之后commit时会在编辑器中回显自定义模板信息，适用于对提交信息有格式要求的团队]
  - *core.pager*　`git config --global core.pager ''` [指定执行诸如`log`和`diff`等命令所使用的分页器，默认less，可设置成more在一页显示所有命令输出，或者空字符串来关闭该选项（显示所有输出）]
  - *user.signingkey*　`git config --global user.signingkey <gpg-key-id>` [将此项配置为GPG签署密钥，使得每次运行`git tag -s <tag-name>`时即可直接创建经签署的含附注的标签]
  - *core.excludesfile*　`git config --global core.excludesfile ~/.gitignore_global` [此配置项类似于全局生效的*.gitignore*文件，如配置为`~/`路径下的`.gitignore_global`模板文件]
  - *help.autocorrect*　`` [在模糊匹配到正确命令的情况下自动纠错，接受一个代表^1^/~10~秒的整数，如设置为10代表1秒后自动执行纠正后的命令]



- 格式化与多余的空白字符
  - *core.autocrlf*　`git config --global core.autocrlf true` [Windows系统上，设置为*true*，提交时自动把回车(CR)和换行(LF)转换成换行(LF)，检出代码时，换行会被转换成回车和换行；Linux或Mac系统上，设置为*input*，提交时把回车和换行转换成换行，检出时不转换。这样在Windows上的检出文件会保留回车和换行，在Linux和Mac上以及<u>版本库</u>中会保留换行。如果一个Windows程序员开发仅运行于Windows上的项目，可以设置*false*取消此功能，把回车也保留在版本库中]
  - *core.whitespace*　`` [见ProGit p.323]



- 服务器端配置
  - *receive.fsckObjects*　`git config --system receive.fsckObjects true` [推送生效前强制确认每个对象的有效性以及SHA-1校验和是否保持一致，检查库的完整性]
  - *receive.denyNonFastForwards*　`git config --system receive.denyNonFastForwards true` [禁止非快进推送]
  - *receive.denyDeletes*　`git config --system receive.denyDeletes true` [禁止通过推送删除分支和标签]



### Git属性

配置 `.gitattributes` 文件（位于项目根目录），针对特定的路径配置某些设置项。

- 二进制文件
  - `*.pbxproj binary` [让Git把所有pbxproj文件当成二进制文件，这样Git不会尝试转换或修正回车换行问题，执行`git show`和`git diff`时也不会比较或打印该文件的变化]
  - `*.docx diff=word` [见ProGit p.325]
- 关键字展开 [见ProGit p.328]
- 导出版本库
  - *export-ignore*　`test/ export-ignore` [归档时忽略某些已纳入项目版本管理的子目录或文件，如在执行 `git archive` 创建的项目压缩包中排除*test/*子目录下的测试文件]
  - *export-subst* [见ProGit p.331]
- 合并策略 [见ProGit p.332]



### Git钩子 [见ProGit p.332]



## 图形界面

- ***gitk*** [历史记录图形化查看器。使用时先*cd*到一个仓库中，然后直接输入`gitk`命令]

  常用选项（*--all*）`gitk --all`

> 可视化界面中每个点代表一个提交，线代表父子关系，彩色块标示引用（指针）；黄点表示HEAD，红点表示尚未提交的本地变动；下方的窗口用来显示当前选中提交的具体信息，提交和补丁显示在左侧，摘要显示在右侧，中间是一组用来搜索筛选的控件。

![gitk历史记录图形化查看器](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/gitk.png)



- ***git gui*** [制作提交的图形化工具。使用时同样先*cd*到一个仓库中，然后输入`git gui`命令]

> 左侧是索引区：未暂存的修改在左上方，已暂存的在左下方；点击文件图标切换staged或unstaged状态；点击文件名查看详情。
>
> 右侧上方以diff格式显示当前选中文件的变动详情，右击某一区块或行展开更多选项。
>
> 右侧下方是编辑和操作区域：编辑日志后点击提交就执行`git commit`命令；选择*Amend Last Commit*就会回显上一次提交以便修订日志，最后点击提交覆盖上一次的提交。

![gitk历史记录图形化查看器](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/git-gui.png)



- GitHub桌面客户端 [见ProGit p.445]



# QUICK NOTES

- **Tab** 键补全命令
- 出现（END）无法操作时按 **Q** 键
- 按方向 **↑** 键可以快速填充之前的命令
- `git add *` [添加所有新增或修改文件至暂存区]





# APPENDIX

## Git命令速查

### 设置与配置

- <a href="#git-config">*git config*</a>

- <a href="#git-help">*git help*</a>

### 获取与创建项目

- <a href="#git-init">*git init*</a>
- <a href="#git-clone">*git clone*</a>

### 快照基础

- <a href="#git-add">*git add*</a>

- <a href="#git-status">*git status*</a>
- <a href="#git-diff">*git diff*</a>
- `git difftool` [启动外部工具展示差异]
- <a href="#git-commit">*git commit*</a>
- <a href="#git-reset">*git reset*</a>
- <a href="#git-rm">*git rm*</a>
- <a href="#git-mv">*git mv*</a>
- `git clean` [从工作区移除文件]

### 分支与合并

- <a href="#git-branch">*git branch*</a>
- <a href="#git-checkout">*git checkout*</a>
- <a href="#git-merge">*git merge*</a>
- `git mergetool` [启动外部工具帮助合并]
- <a href="#git-log">*git log*</a>
- <a href="#git-stash">*git stash*</a> [在切换分支时临时保存未提交的工作，而不用非得提交才能切换分支]
- <a href="#git-tag">*git tag*</a> [为代码历史记录中的某一个点指定一个永久的书签]
- `git rerere` [reuse recorded resolution，记录冲突的解决方法，并在下次碰到相同冲突时尝试自动解决]

### 项目分享与更新

- <a href="#git-fetch">*git fetch*</a>
- <a href="#git-pull">*git pull*</a>
- <a href="#git-push">*git push*</a>
- <a href="#git-remote">*git remote*</a>
- `git archive` [创建项目指定快照的归档文件用于分享]
- <a href="#git-submodule">*git submodule*</a> [用来管理一个仓库的其他外部仓库]

### 检查与比较

- `git show` [以一种简单的可读的方式显式一个Git对象，通常用于显示一个标签或提交信息]

- `git shortlog` [归纳`git log`的输出，展示一个根据作者分组的提交记录的概括性信息]

  可以用来创建changelog文件，如输出master分支*v1.0*以后的所有非合并提交，并且按照作者分组：

  `git shortlog --no-merges master --not v1.0`

- `git describe` [生成一个更具可读性的提交描述，如生成一个构建号]

### 调试

- `git bisect` [见ProGit p.276 二分查找，查找导致 bug或问题 的第一个提交]

- `git blame` [向指定文件的行标注最后一次变更的信息]

  如标注文件*a.txt*的2到4行

  ```bash
  $ git blame -L 2,4 a.txt
  fe5d612b (Shaun 2019-08-20 16:58:10 +0800 2) change
  276d507f (Shaun 2019-08-21 11:47:03 +0800 3) modified
  276d507f (Shaun 2019-08-21 11:47:03 +0800 4) change today
  ```

  如上输出信息中，首列为SHA-1缩略值，括号内依次为作者、时间、行号，尾列为文件内容

- <a href="#git-grep">*git grep*</a> 搜索

### 补丁

- `git cherry-pick` [从一个分支拣选一或多个（==连续的？==）提交，然后尝试作为一个新的提交引入到当前分支，而非合并整个分支。<u>类似于对某些特定提交的变基</u>，适用于只想引入其他分支的某个提交，或者其他分支只有一个提交而又不想执行变基的情况。]

  假设dev分支上有几个提交以字母代指（执行Git命令时要输入完整SHA-1值）：

  A：*e43a6fd3e94888d76779ad79fb568ed180e5fcdf*

  B：*659ef797d181633c87ec71ac3f9ba29fe5775b92*

  C：*738ee872852dfaa9d6634e0dea7a324040193016*

  D：*aad881d154acdaeb2b6b18ea0e827ed8a6d671e6*

  则在master分支上执行 `git cherry-pick A` 引入一个提交；

  或 `git cherry-pick A B C` 引入多个提交；

  或 `git cherry-pick A..D` 引入从A到D但<u>不包含A</u>的多个提交，

  若要<u>包含A</u>，则执行 `git cherry-pick A^..C`。

  ***ps:*** ==当某些提交存在依赖关系时（如修改了同一文件）则不能只拣选一个提交，要cherry-pick所有关联的提交才可能不出错==

- <a href="#git-rebase">*git rebase*</a>

- `git revert` [撤销、还原]

### 邮件

- `git apply` [应用由`git diff`命令生成的补丁]
- `git am` [应用由`git format-patch`命令生成的补丁]
- `git format-patch`
- `git imap-send` [将一个由`git format-patch`生成的补丁上传至邮箱IMAP草稿文件夹]
- `git send-email` [通过邮箱发送使用`git format-patch`生成的补丁]
- `git request-pull` [简单地生成一个可通过邮件发送给其他人的示例信息体]

### 外部系统

- `git svn` [使Git作为一个客户端来与SVN通信，意味着可以使用Git来检出内容，或者提交到SVN服务器]
- `git fast-import` [快速地将其他格式（其他版本控制系统等）映射到Git可以轻松记录的格式]

### 管理

- `git gc` [收集所有松散对象放置到包文件中，将多个包文件合并为一个大的包文件，移除与任何提交都不相关的陈旧对象。一般在后台自动运行]

- `git fsck` [检查数据库的完整性]

  如执行`git fsck --full`输出所有未被其他对象指向的对象，其中dangling commit后跟的SHA-1就是丢失的提交（例如reset掉的提交）。

- <a href="#git-reflog">*git reflog*</a>

- `git filter-branch` [用来根据某些规则重写大量的提交记录，例如从每个提交中删除同一个文件，或通过过滤仓库中的一个单独的子目录以提取一个项目]

### 底层命令

- `git ls-remote` [查看服务端的所有原始引用]

  `git ls-remote https://github.com/schacon/blink`

- `git ls-file` [查看暂存区更原始的样子]

- `git rev-parse` [解析输出指针指向对象的完整SHA-1值]

  `git rev-parse master` 输出master指针当前指向提交对象的SHA-1值

  `git rev-parse HEAD` 输出HEAD指针当前指向提交对象的SHA-1值
