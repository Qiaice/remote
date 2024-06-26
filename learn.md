### 0. 前置

笔记中由 **[]** 括起来的内容表示 **可选项**，由 **\<>** 括起来的内容表示 **必填项**

### 1. 获得仓库

可以通过如下两种方式获得仓库：

- **git init [path]**：将一个本地目录初始化为 git 仓库
- **git clone \<url>**：将远程仓库克隆到本地

### 2. 工作区域

git 将工作区域分为了三部分，分别是：

- 工作区：就是当前目录
- 暂存区：一个抽象区域，用于临时存放需要提交的内容
- 仓库：同样是一个抽象区域，用于保存本地内容和版本信息

### 3. 添加和提交文件

**添加文件**：可以使用 **git add \<file>** 将文件添加到暂存区，支持通配符

**提交文件**：可以使用 **git commit [-m comment]** 将暂存区中的内容提交到本地仓库

提交完成可以使用 **git log [--oneline --graph]** 命令查看提交记录

### 4. git reset 回退版本

回退版本可以使用 **git reset [options] \<commit-id> ** 命令，该命令有如下三种参数：

- **--soft**：保留工作区和暂存区里的内容
- **--hard**：丢弃工作区和暂存区里的内容
  - 如果使用该参数回退到了错误的版本可以使用 **git reflog** 查看所有的提交记录
- **--mixed**：保留工作区、丢弃暂存区里的内容

ps：默认使用 mixed 参数

上述三种参数不是那么好理解，建议实际演示一遍

### 5. git diff 查看差异

使用 **git diff [options] [params]** 可以查看区域内容差异，文件差异，分支差异等等

- **git diff**：默认查看 **工作区和暂存区** 的差异，或者工作区和刚刚修改但还没有添加进暂存区的内容的差异
- **git diff HEAD**：查看 **工作区和当前最新提交** 的差异
  - HEAD：表示当前版本库最新提交的提交 id
- **git diff --cached**：查看 **暂存区和当前最新提交** 的差异
- **git diff \<commit-id-1> \<commit-id-2>**：查看 **提交之间** 的差异
  - 一般 **当前最新提交和上一次提交** 之间的差异对比用的比较多，该命令可以简写为 **git diff HEAD^ HEAD**
  - 或者是 **git diff HEAD~ HEAD**，^ 和 ~ 符号搭配 HEAD 都可以表示上一次提交
  - 同理，如果在其后加上数字就可以表示当前最新提交的前几次提交，比如：**HEAD~2** 表示 **当前最新提交的前两次提交**
- 如果需要精确对比提交之间某个文件的差异，可以在提交 id 后再加上需要对比的文件名，比如：**git diff HEAD^ HEAD file.txt** 这段命令表示对比当前最新提交和上一次提交 file.txt 文件的差异
- **git diff \<branch-name-1> \<branch-name-2>**：比较 **分支之间** 的差异（这个以后再来补全）

### 6. git rm 删除文件

使用 **git rm [options] [params]** 将文件从 git 中删除

- **git rm \<file>**：从工作区和暂存区中删除指定文件，需要提交才能真正删除
- **git rm --cached \<file>**：仅仅从暂存区中删除指定文件
- 当然使用 rm 命令将文件直接删掉也是可以的，但是删掉之后还需要再将删除的文件添加到暂存区，最后再进行提交才能将文件从 git 中删除
  - 这一步理解起来有点抽象，可以理解为只删掉了工作区中的指定文件，但暂存区中还是存在的，所以需要将其提交到暂存区再进行提交，才能完成删除

补充：

- **git restore \<file>**：将指定文件恢复到修改之前

### 7. .gitignore 文件

git 使用 .gitignore 文件将匹配的文件剔除 git 的版本控制，在 .gitignore 文件中使用 **glob 匹配模式** 进行匹配：

- **\*.a**：忽略仓库中的所有 a 文件

- **!lib.a**：此处的 ! 表示否定，意思是不忽略 lib.a 文件，如果文件中确实有 ! 符号，那么需要在 .gitignore 文件中使用 \ 转义字符转义

- 如下为一份测试的 .gitignore 文件
  ```.gitignore
  # 忽略所有的 .a 文件
  *.a
  # 但跟踪所有的 lib.a 文件
  !lib.a # ! 表示否定，也就是不忽略，如果文件名中确实存在 !，那么需要使用 \ 转义
  # 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
  /TODO # / 在前表示仅忽略当前目录下的特定文件或文件夹
  # 忽略任何目录下名为 build 的文件夹
  build/ # / 在后表示忽略的是文件夹
  # 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
  doc/*.txt
  # 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
  doc/**.pdf
  ```


### 8. 关联本地仓库和远程仓库

- **git remote add \<remote_alias> \<remote_address>**：为远程仓库设置一个别名（一般都是 **origin** ）并将其与本地仓库关联
- **git remote [options]**：查看本地仓库关联的远程仓库，使用 **-v 选项** 可以 **查看远程仓库的地址**
- **git pull \<remote_alias> \<remote_branch>:\<local_branch>**：拉取远程仓库的指定分支，并 **自动合并** 到指定的本地分支
  - 还可以使用 **git fetch** 命令拉取分支，和 git pull 的区别就是，**git fetch 仅拉取修改，不会自动合并**
- **git push -u \<remote_alias> \<local_branch>:\<remote_branch>**：将指定本地分支推送到远程仓库的指定分支，如果存在冲突则推送失败

### 9. 分支

- **git branch**：可以查看当前仓库有哪些分支，并且现在处于哪个分支
- **git branch \<new_branch>**：创建一个新的分支
  - 加上 **-m** 选项，也就是 **git branch -m \<new_branch>** 将重命名当前分支，如果新分支名已存在，则重命名失败
  - 加上 **-M** 选项，也就是 **git branch -M \<new_branch>** 将 **强制重命名当前分支**，如果新分支名已存在，将覆盖该分支，**请谨慎使用**
  - 加上 **-d** 选项，也就是 **git branch -d \<branch>** 将删除指定分支，如果该分支未合并，则删除失败
  - 加上 **-D** 选项，也就是 **git branch -D \<branch>** 将 **强制删除指定分支**，**请谨慎使用**
- **git switch \<branch>**：切换分支
  - **git switch -c \<new_branch>**：将在创建分支后自动切换到新分支
- **git merge \<branch> [-m \<info>]**：将指定分支合并到当前分支，并添加一些简要信息说明

### 10. 解决冲突

- 手动解决，手动合并冲突内容
- **git merge --abort**：取消此次合并

### 11. Rebase

**Rebase**，可以 **将当前分支的提交记录变基到指定分支上**，使用命令 **git rebase \<branch>**
