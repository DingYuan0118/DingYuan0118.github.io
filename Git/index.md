# Git学习笔记


- [Git学习笔记](#git学习笔记)
    - [如何将branch的文件有选择的mergy至master中？](#如何将branch的文件有选择的mergy至master中)
    - [如何将特定文件的修改历史调出？](#如何将特定文件的修改历史调出)


## 如何将branch的文件有选择的mergy至master中？
- `git checkout "branch" "filename"`可手动的挑选特定文件mergy至master中。
## 如何将特定文件的修改历史调出？
- `git log "filename"`可以直接调出特定文件的历史，通过增加`-p`参数可以看到特定文件的修改详情。
- 在`vscode`中可以通过选中特定文件，在命令面板上使用`View File History`命令调出文件修改历史界面。