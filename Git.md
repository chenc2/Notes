# Setup and Config

## Config

### Options
- **`--`add &lt;name> &lt;value>**
- **`--`get &lt;name>**
- **`--`global**
  - **For all users, path:{/home/xx/.gitconfig; C:\Users\xx\.gitconfig}**
- **`--`system**
  - **For current user, path:{/etc/gitconfig}**
- **`--`local**
  - **For project, path:{.git/config}**
- **`--`unset &lt;name>**
- **`--`list**

### Variables
- **user.name &lt;value>**
- **user.email &lt;value>**
- **http.proxy &lt;value>**               `http://proxy-shz.intel.com:911`
- **https.proxy &lt;value>**              `https://proxy-shz.intel.com:911`
- **apply.whitespace &lt;value>**
  - **warn: Apply the patch with whitespace and give a warning message**
  - **nowarn: Apply the patch with witespace and not give the messsage**
  - **fix: Remove whitespace and apply the patch, give a warning message**
  - **error: Fail to apply the patch, give an error message**
- **color.[diff|status|branch|ui] <true|false|auto>**
- **core.autocrlf <true|false|input>**
  - **true**
    - **commit: CRLF->LF**
    - **fetch: LF->CRLF**
  - **false**
  - **input**
    - **commit: CRLF-LF**

### MISC

# Getting and Creating Projects

## init

## clone

# Basic Snapshotting

## add
- **git add &lt;file>**

## status
- **git status**

## diff
- **git diff --cached**                                                         `Compare staged/index and repo`
- **git diff --staged**                                                         `Same as above`
- **git diff --stat**                                                           `Compare the difference of lines`
- **git diff --name-only**                                                      `Show only names of changed files`
- **git diff --name-status**                                                    `Show only names and status of changed files`
- **If staged/index space is empty, git will compare workspace and repo**
- **If staged/index space it not empty, git will compare workspace and stged/index**

## commit
- **git commit -m &lt;commit message>**
- **git commit --amend**

## reset
- **git reset &lt;file>**
  - **Use HEAD to restore staged/index space, no change in workspace**
- **git reset --[soft | hard] &lt;commit>**
  - **soft: Just revert commit information**
  - **hard: Not only revert commit information, but also for staged/index and workspace**
  - **commit: HEAD, HEAD~, HEAD~10, commit-id and so on**
  
# Branching and Merging

## branch
- **git branch**
- **git branch &lt;branch name>**
- **git branch -[d | D] &lt;branch name>**
- **git branch -[v | vv | r | a]**
- **git branch -u &lt;upstream>**
- **git branch --unset-upstream**
- **git branch &lt;branch name> &lt;remote branch name>**
- **git branch --no-merged**
- **git branch --merged**

## checkout
- **git checkout &lt;branch name>**
- **git checkout -b &lt;branch name>**
- **git checkout -b &lt;branch name &lt;remote branch name**
- **git checkout -- &lt;file>**
  - **If staged/index is not empty, Use them to restore file**
  - **If staged/index is empty, Use HEAD to restore file**
- **git checkout HEAD &lt;file>**
  - **Indicate to use HEAD to restore file and clear staged/index space**

## merge
- **git merge &lt;branch name>**

### fast forward
### merge conflict

## log

## stash
- **git stash**
- **git stash list**
- **git stash pop**
- **git stash drop**

# Sharing and Updating Projects

## fetch
- **git fetch**
- **git fetch &lt;host name>**
- **git fetch &lt;host name> &lt;remote branch name>**
- **git fetch &lt;host name> &lt;remote branch name> : &lt;branch name>**

## push
- **git push**
- **git push &lt;host name>**
- **git push &lt;host name> --delete &lt;remote branch name>**
- **git push &lt;host name> : &lt;remote branch name>**
- **git push &lt;host name> &lt;branch name> : &lt;remote branch name>**

## remote
- **git remote**
- **git remote -v**
- **git remote show &lt;host name>**
- **git remote add &lt;host name> &lt;url>**
- **git remote remove(rm) &lt;host name>**

# Patching

## apply
- **git apply `--` check &lt;patch file>**
- **git apply &lt;patch file>**

# Email

## format-patch
- **git format-patch -&lt;number>**
- **git format-patch -&lt;number> &lt;commit-id>**
- **git format-patch -s &lt;commit-id>**
- **git format-patch -1 &lt;commit-id>**
- **git format-patch `--`cover-letter**

# Administration

## clean
- **git clean -n**                `List which files will be remove`
- **git clean -f**                `Remove un-tracked files`
- **git clean -d**                `Remove un-tracked folders`
- **git clean -x**                `Remove ignored files`

## reflog

# Github

## Generate key
- **step 1: ssh-keygen -C &lt;user.email> -t rsa**
  - **Will genreate private and public key under the folder**
  - **Linux: /home/xx/.ssh**
  - **Windows: C:\Users\xx\.ssh**
  - **private key: id_rsa**
  - **public key: id_rsa.pub**
- **step 2: Add public key on github**
