# This note is to record command line of github

+ To configure your ssh
```shell
git config --global user.name "your_name"
git config --global user.email "your_email"
ssh-keygen -t rsa -C "yangruiwen2002@163.com"
cd ~/.ssh/
cat id_rsa.pub
ssh -T git@github.com
```
+ To create a new repo 
```shell
echo "# My_learning_note" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/MT-YRW/My_learning_note.git
git push -u origin main
```
+ To push my code
```shell
git add *
git commit -m "annotations"
git push origin main
```
