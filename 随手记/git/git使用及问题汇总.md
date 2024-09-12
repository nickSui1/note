## git拉取远程项目到本地

- 在本地磁盘创建一个文件夹用于存储远程项目
- 在cmd或者terminal中定位到刚才的文件目录
- 初始化本地git库：git init
- 关联远程仓库：git remote add origin <your git url>
- 使用 git remote -v 查看关联的远程仓库
- git fetch 从远程仓库获取最新的分支和提交信息
- git pull origin <your current branch>

### 更新代码建议

- 新建一个分支 git branch <new branch name>
- checkout 到新分支，git checkout <new branch name>
- 修改后，git status 查看改动
- 使用git commit -m "改动详情" 提交
- git push origin <new branch name>
- 在github中新建pull request，请求将代码合并到主分支
- merge pull request，并删除此分支
- 本地库切换到主分支后删除<new branch name>，pull更新。

## git 文件路径有中文会乱码的情况

在git status时候如果有中文文件，会显示乱码，通过git add xxx的时候也会报错

### windows git bash

- git bash 右键选择option > text > 选择zh_cn或者c，后面的label选择utf-8
- 使用 `git config --global core.quotepath false` 将core.quotepath设置为false，quotepath表示引用路径

完成以上两步就可以使用中文路径了。

### mac terminal

使用命令“git config --global core.quotepath false”解决，即将core.quotepath设为false，就不会对0×80以上的字符进行quote，中文显示正常。具体解决效果如下：

git config --global core.quotepath false

使用 git config -l 可以查看是否生效。