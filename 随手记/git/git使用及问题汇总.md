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



## Ignore

To ignore a log file (or any other file) in a Git repository, you can use a `.gitignore` file. This file tells Git which files or directories to ignore in your project. Here’s how you can do it:

### Steps to Ignore a Log File

1. **Create or Open the `.gitignore` File**:
   - If your project doesn't already have a `.gitignore` file in the root directory, you can create one.
   - Open the existing `.gitignore` file if it already exists.

2. **Add the Log File to the `.gitignore`**:
   - Add the name of the log file you want to ignore. For example, if your log file is named `app.log`, you would add:
     ```
     app.log
     ```

   - If you want to ignore all `.log` files, you can add:
     ```
     *.log
     ```

3. **Save the `.gitignore` File**.

### Example `.gitignore` File

Here’s an example of what your `.gitignore` file might look like if you’re ignoring all log files:

```
# Ignore log files
*.log
```

### Important Note

- **If the Log File Is Already Tracked**: If you have already committed the log file and now want to ignore it, adding it to `.gitignore` alone won’t stop it from being tracked. You’ll need to untrack it first:
  
  ```bash
  git rm --cached app.log
  ```

  After running this command, the file will be removed from the staging area (but will remain in your working directory). Then, commit the changes:

  ```bash
  git commit -m "Stop tracking app.log"
  ```

### Committing Changes

After updating your `.gitignore` and removing the log file from tracking (if necessary), you can now commit your changes without including the log file.

```bash
git add .gitignore
git commit -m "Ignore log files"
```

### Summary

- Create or update your `.gitignore` file to include the log file.
- If the log file is already tracked, use `git rm --cached <filename>` to stop tracking it.
- Commit your changes to apply the `.gitignore` settings.

Now, your log file should be ignored by Git, and it won’t be included in future commits! If you have any more questions or need further assistance, feel free to ask.

If you’ve modified your `.gitignore` file and want to ensure that the changes take effect (especially for files that were already being tracked), here’s what you need to do:

### Steps to Refresh Git's Tracking After Changing `.gitignore`

1. **Edit Your `.gitignore` File**:
   - Make sure your `.gitignore` file correctly lists the files or patterns you want to ignore.

2. **Untrack Already Tracked Files**:
   - If any files that should now be ignored were previously tracked by Git, you'll need to untrack them. Use the following command:
     ```bash
     git rm --cached <file_or_directory>
     ```
   - For example, if you want to stop tracking a file named `app.log`, you would run:
     ```bash
     git rm --cached app.log
     ```

   - If you want to stop tracking all `.log` files, you can run:
     ```bash
     git rm --cached *.log
     ```

3. **Commit Your Changes**:
   - After untracking the files, commit the changes. This updates the repository to reflect that those files should no longer be tracked:
     ```bash
     git commit -m "Update .gitignore and untrack log files"
     ```

4. **Verify the Changes**:
   - After committing, you can verify that the log files are being ignored by running:
     ```bash
     git check-ignore -v <file>
     ```
   - For example:
     ```bash
     git check-ignore -v app.log
     ```
   - This command will tell you if the specified file is being ignored and which rule in `.gitignore` is causing it to be ignored.

### Example Steps

1. **Modify `.gitignore`**:
   ```plaintext
   # Ignore log files
   *.log
   ```

2. **Untrack the Log File**:
   ```bash
   git rm --cached app.log
   ```

3. **Commit the Change**:
   ```bash
   git commit -m "Stop tracking app.log"
   ```

4. **Verify**:
   ```bash
   git check-ignore -v app.log
   ```

### Note

- The `git rm --cached` command will not delete the file from your filesystem; it only stops tracking it in Git.
- If you want to untrack multiple files or a directory, you can use wildcards or specify a directory name.

### Summary

- Update your `.gitignore` file.
- Untrack any files that were previously tracked and should now be ignored.
- Commit your changes to reflect these updates in your Git history.

After following these steps, Git will respect your `.gitignore` settings, and any changes you made to the ignore file should now be in effect. Let me know if you need any more help!



## Count Your Project

To count the number of lines and words in your Git project, you can use a combination of commands in your terminal. Here’s how you can do it:

### 1. **Count the Total Lines in All Files in the Project**

You can use the `find` and `wc -l` (word count with line counting option) commands to count the total number of lines in all files of your Git project:

```bash
find . -type f -name '*' -exec wc -l {} + | awk '{ total += $1 } END { print total }'
```

### 2. **Count Total Words in All Files**

To count the number of words across all files in the project, use:

```bash
find . -type f -name '*' -exec wc -w {} + | awk '{ total += $1 } END { print total }'
```

### 3. **Break Down by File Types (Optional)**

If you want to focus on specific file types (like `.java`, `.js`, `.py`, etc.), you can modify the `find` command to search for only those file types. For example, to count the lines in all `.java` files:

```bash
find . -type f -name '*.java' -exec wc -l {} + | awk '{ total += $1 } END { print total }'
```

### 4. **Ignore Files Listed in `.gitignore` (Optional)**

If you want to ignore files that are listed in your `.gitignore`, you can use the `git ls-files` command, which lists all the files tracked by Git:

```bash
git ls-files | xargs wc -l | awk '{ total += $1 } END { print total }'
```

For counting words in tracked files:

```bash
git ls-files | xargs wc -w | awk '{ total += $1 } END { print total }'
```

### Summary

- **Total line count**: `find . -type f -name '*' -exec wc -l {} + | awk '{ total += $1 } END { print total }'`
- **Total word count**: `find . -type f -name '*' -exec wc -w {} + | awk '{ total += $1 } END { print total }'`
- **For specific file types**: Modify the file name pattern in the `find` command (e.g., `*.java` for Java files).
- **For only tracked files**: Use `git ls-files` to count lines and words only in files tracked by Git.

These commands will give you an overview of both the number of lines and words in your project.

## 基于指定远程分支创建本地分支

``` bash
# 进入项目目录
cd /path/to/your/project

# 获取远程分支信息
git fetch origin

# 基于远程分支创建本地分支
git checkout -b upgrade origin/upgrade 
```

## rebase

`git rebase` 是 Git 中用于**重新整理提交历史**的命令。它的作用是将一个分支上的提交**在另一个分支的最新提交上重新应用**，保持提交记录的清晰和线性。

### `git rebase` 的作用

主要用途是：

1. **保持提交历史整洁、线性**：`git rebase` 将指定分支的提交“移到”另一个分支之上，避免生成合并提交，提交记录看上去就像从头开始依次进行的。
2. **同步更新代码**：在协作开发中，如果其他成员提交了新的更改，你可以使用 `rebase` 来在自己的提交之前先“应用”这些新的更改，保持提交历史的顺序。
3. **简化功能分支的合并**：在把功能分支（如 `feature`）合并到主分支（如 `master`）时，`rebase` 可以消除分支间的分歧，避免生成不必要的合并提交，最终将功能分支“拍平”在主分支之上。

### 基本用法

#### 1. 基本命令格式

```
bash


复制代码
git rebase <branch>
```

这个命令的意思是将当前分支上的提交应用到 `<branch>` 的最新提交上。例如：

```
bash


复制代码
git checkout feature
git rebase master
```

上面这段代码会将 `feature` 分支上的提交在 `master` 最新的提交之上重新应用，就像从 `master` 开始开发 `feature` 一样。

#### 2. 常用的 `rebase` 情况

- **同步远程更新**：将主分支上的更新应用到当前分支上（例如 `feature` 分支），确保 `feature` 分支是基于最新的主分支代码。

  ```
  bash
  
  
  复制代码
  git checkout feature
  git rebase origin/master
  ```

- **交互式变基**（`-i`）：可以在变基时修改提交记录，例如合并、删除、重排序提交。使用方式：

  ```
  bash
  
  
  复制代码
  git rebase -i HEAD~3
  ```

  这个命令会让你交互式地编辑最近的 3 个提交。

### 注意事项

- **不适合在公共分支上变基**：`git rebase` 会重写提交历史，尽量只在本地或功能分支上使用，避免影响团队中其他人使用同一分支的提交。
- **遇到冲突时需手动解决**：如果在 `rebase` 过程中发生冲突，需要手动解决冲突并继续变基（`git rebase --continue`）。

### 总结

`git rebase` 是一种保持提交历史简洁清晰的工具，适合在分支开发和合并的过程中使用，帮助开发者更好地管理代码变更。