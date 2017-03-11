# Git操作（本人所不知道的，常用的）

* `git checkout -b test` 创建并切换分支

* `git checkout -` 切换到上一个分支

* `git log --graph` 以图表方式查看分支

* `git reflog` 查看当前仓库的操作日志

* `git reset --hard 哈希值` 回溯到某个历史版本（这里的哈希值就是操作日志中的哈希值）

* `git commit --amend` 修改提交信息

* `git reset .` 删除所有被`add`但未`commit`的文件

* `git rm --cached <file_path>` 删除缓存中（即已经`add`但未`commit`）的某个文件，而不会删除物理文件

* `git rm --f <file_path>` 不仅将该文件从缓存中删除，还会将物理文件删除（不会回收到垃圾桶）
