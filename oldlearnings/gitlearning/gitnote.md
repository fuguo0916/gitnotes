```c
git help <命令名>
git init //创建本地仓库

git config user.name //设置项目级别的用户名
git config user.email
cat .git/config //项目级别的用户信息保存在.git/config中

cd ~ && pwd //Windows中的home目录实际上用户名目录
git config --global user.name //设置系统用户级别的用户名
git config --global user.email 
cat ~/.gitconfig  //系统用户级别的git用户信息保存至 ~/.gitconfig中 

git status //查看文件状态
git add .
git rm --cached gitnode.pdf //从暂存区移到工作区
git restore --staged gitnote.pdf //从暂存区移到工作区
git add gitnote.pdf
git commit gitnote.pdf //不加 -m 就会进入文本编辑器
git commit 
git restore hello.txt //从暂存区恢复在工作区已经修改的文件

git log //查看版本历史记录
git log --pretty=oneline
git log --oneline //只能向后查看
git reflog 

git reset --hard <索引值(全部/部分)>
git reset --hard HEAD^^^ //后退三步
git reset --hard HEAD~2  //后退两步

git reset --soft //改变本地库
git reset --mixed //改变本地库和暂存区
git reset --hard //改变本地库、暂存区、工作区

git diff hello.txt //将工作区与暂存区的文件比较，将工作区视为最新版本
git diff HEAD hello.txt //将工作区与本地库的文件比较
git diff HEAD^ hello.txt //将工作区与指定版本的本地库比较
git diff //不写文件名则进行所有文件的比较

git branch -v //查看分支
git branch hot_fix //创建新的分支
git checkout hot_fix //切换分支
git merge hot_fix //当前分支是被修改的分支，把指定的分支merge到当前分支
//merge时以文件为单位，将文件更新为最新版本即可
//解决merge冲突：修改冲突文件，然后 add && commit

git remote -v //查看远程库信息
git remote add origin https://github.com/fuguo916/hello.git 
    //添加远程库，并命名为origin
git push origin master
git clone https://github.com/fuguo916/hello.git
git fetch origin master
git merge origin/master
git pull origin master //相当于fetch + merge
//在线fork会复制一份自己的远程库，修改后可以提出Pull Request。

ssh-keygen -t rsa -C fuguo19@mails.ucas.ac.cn //生成密钥

// 更新.gitignore的方法
git rm -r --cached .
git add .
git commit -m "update .gitignore"

// 清空远程仓库
git checkout --orphan nb
git add -A
git commit -m "first commit"
git branch -D master
git branch master
git push -f origin master

// 解决clone失败问题
git config --global http.sslVerify "false"
```