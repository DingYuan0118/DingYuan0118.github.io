# Git学习笔记


- [Git学习笔记](#git学习笔记)
  - [如何将branch的文件有选择的mergy至master中？](#如何将branch的文件有选择的mergy至master中)
  - [如何将特定文件的修改历史调出？](#如何将特定文件的修改历史调出)
  - [`git fetch`与`git pull`有什么区别](#git-fetch与git-pull有什么区别)
  - [裸仓库(bare repo)与普通仓库的区别(repo)](#裸仓库bare-repo与普通仓库的区别repo)


## 如何将branch的文件有选择的mergy至master中？
- `git checkout "branch" "filename"`可手动的挑选特定文件mergy至master中。
## 如何将特定文件的修改历史调出？
- `git log "filename"`可以直接调出特定文件的历史，通过增加`-p`参数可以看到特定文件的修改详情。
- 在`vscode`中可以通过选中特定文件，在命令面板上使用`View File History`命令调出文件修改历史界面。

## `git fetch`与`git pull`有什么区别
- 本地`git clone`的仓库在 *refs* 文件夹下有 *heads* 、*remotes* 两个文件夹，分别记录了**本地**仓库各个分支的版本记录与正在跟踪的**远程**仓库各个分支的版本记录。
- `git fetch` 是当远程仓库有更新时，将远程仓库提交的记录同步至 *remotes* 中，随后通过使用`git mergy`更新本地仓库的记录，如果远程仓库与本地仓库出现冲突，`git mergy`需要解决冲突。

- `git pull` 是直接将远程仓库更新的内容直接同步至本地仓库，同时更新 *remotes* 中的记录。效果相当于 `git fetch + git mergy`。

  通常推荐使用 `git fetch + git mergy`，原因未知。一般情况下使用`git pull`也没问题。

## 裸仓库(bare repo)与普通仓库的区别(repo)
- `git init --bare <repo.git>` 用于初始化一个裸仓库，裸仓库内不含有`working tree`。因此裸仓库只能记录版本，不能提交代码，常用作中心仓库，用于分享包含更改记录的版本信息。**Github**上存储的仓库均为裸仓库。一般是`git push`指令的目的地。
> 注意源代码在裸仓库中也是有备份的，只不过是隐式的。没有显式的`working tree`。通过`git clone repo.git`即可克隆出一个包含工作树的普通仓库。

- `git init <repo>`用于初始化一个一般仓库，该仓库用于编辑、删除、提交等工作，`<repo>`目录下存在着一个 *.git* 文件夹，里面存储的版本信息。