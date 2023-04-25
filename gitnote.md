## git config
```shell
git config --system user.name fuguo
git config --system user.email fuguo19@mails.ucas.ac.cn
git config --global core.autocrlf input
git config --global core.safecrlf false
git config --global core.filemode false
git config --local user.name zuoyue
git config user.email zuoyue@gmail.com
```
`--system` for all users on this system, `--global` for one user of the system, `--local` or `none` for one repository.

## git clone
```shell
ssh-keygen -t rsa -C kitty0916@qq.com
git clone git@github.com:fuguo/helloworld.git hellokitty
```

## git reset
```shell
git reset main.py   # reset main.py from HEAD to staging area
git reset           # reset from HEAD to staging area
git reset --mixed   # reset from HEAD to staging area
git reset --hard    # reset from HEAD to staging area and working space
git reset HEAD --mixed
git reset HEAD^^ --hard
```

## git checkout
```shell
git checkout -- helloworld.c    # checkout helloworld.c from staging area to working space
```

## git restore
```shell
git restore somefile            # restore somefile from staging area to working space
git restore --staged somefile   # restore somefile from HEAD to staging area
```

## git commit
```shell
git commit -a -m "it adds all tracked files and then commits"
git commit --amend -m "the last commit won't show up but won't gone"
```

## git status
```shell
git status --short
```
The lefter column means staging area, while the right column means working space.

## git rm
```shell
rm file1                # remove from only working space
git rm file1            # remove from both working space and staging area
git rm --cached file1   # remove from only staging area, --staged and --cached are synonyms
```

## git mv
```shell
mv file1 file2              # rename files only in working space
git mv file1 file2          # rename files in both working space and staging area
git mv --staged file1 file2 # rename files only in staging area
```
