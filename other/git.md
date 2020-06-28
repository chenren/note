# 常用命令
cmd | meaning
:-: | :-:
git clone xxx | -
git status | -
git pull | -
git branch -r | 远程分支
git branch -vv | branch 详细信息
git branch xxx origin/...	| 创建新分支
git add xxx | 添加文件
git commit -m 'xxx' | 本地提交
git push origin master:refs/for/master | 远程提交，origin为远程仓库，master为本地分支
git commit --amend | 在上一次提交的基础上提交  
git reset --hard HEAD~2	| 版本回退，文件重置为之前的版本，覆盖之后的提交记录
git revert -n	xxx | 版本回退，创建新的提交记录，保留之前的提交记录
git config --list | 查看配置信息
git blame xxx | 查看文件每一行由谁更改  
git remote -v | 查看远程服务器名  
git remote update origin --prune | 更新远程分支列表
git log --pretty=format:"%H","%cd","%s" | 格式化log输出
git log --since="2020-04-17 16:00:00" --pretty=format:"%H","%an<%ae>","%cd","%s\n%b" --no-merges | 指定时间的格式化log输出

# SVN命令
cmd | meaning
:-: | :-:
svn st | 看状态
svn commit  XXX文件 -m "XX" | 提交文件
svn co --depth=immediates https:xxx | 不拉子文件夹下的内容

# ssh配置  
cd ~/.ssh  
ssh-keygen -t  rsa -C "xxx@mail.com"  
cat id_rsa.pub 		//查看公钥  
cat config			//配置用户  
git config --global user.name "username"
git config --global user.email "email"

# repo
repo 安装：
```
mkdir ~/bin
PATH=~/bin:$PATH
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

repo 命令包含：
将PATH=~/bin:$PATH 加到文件 ~/.bashrc
每次终端调用source ~/.bashrc

repo命令无法使用时：
```
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc  
export PATH=$PATH:$HOME/bin
```
