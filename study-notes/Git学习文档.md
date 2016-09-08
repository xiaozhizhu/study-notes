# Git学习文档

## 创建版本库
1. 新建一个用作版本库的目录  

		# mkdir learngit  

2. 通过`git init`命令把这个目录变成 Git 可以管理的仓库：

		# git init
		Initialized empty Git repository in /Users/xiaozhu/learngit/.git/

## 添加文件到仓库
1. 将所添文件直接放在版本库目录下 
		
		# touch readme.txt
		
2. 通过`git add`命令将文件添加到仓库：

		# git add readme.txt
		
3. 用命令`git commit`告诉Git，把文件提交到仓库：

		# git commit -m "add a readme.txt file" 
简单解释一下git commit命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

## 版本管理
### 查看版本状态
1. 使用`git status`命令可以让我们时刻掌握仓库当前的状态：
		
		# git status
		
2. 使用`git diff`命令可以查看具体某个文件具体修改了什么内容：

		# git diff readme.txt
		
### 版本回退
在Git中，用HEAD表示当前版本，也就是最新的版本号，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
1. 使用`git log`命令可以显示从最近到最远的提交日志：
		
		# git log
		或者更易读的显示方式
		# git log --pretty=oneline
注意，以上命令将显示一大串类似3628164...882e1e0的是`commit id`（版本号），和SVN不一样，Git的`commit id`不是1，2，3……递增的数字，而是一个SHA1计算出来的一个非常大的数字，用十六进制表示，这样的做的目的是避免不同人的版本号冲突。
		
2. 使用`git reset --hard  版本号`命令可以跳转（回退或前进）到特定版本：

		# git reset --hard HEAD^
		回退到上一个版本
		# git reset --hard 3628164
		跳转到commit id前几位时3628164的版本
Git跳转的机制是：在Git内部有一个指向当前版本的HEAD指针，当你跳转版本的时候，Git仅仅是把HEAD从指向对应的版本号，然后顺便把工作区的文件进行更新，所以你让HEAD指向哪个版本号，你就把当前版本定位在哪。

3. `git reflog`用来记录操作git的每一次命令，所以通过该命令可以找到所有的版本号：

		# git log
		
### 撤销修改
1. 在Git中，使用命令`git checkout -- <file>`可以丢弃工作区文件的修改：
		
		# git checkout --readme.txt
命令`git checkout -- readme.txt`意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：  

	-  一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
	-  一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。  

	总之，就是让这个文件回到最近一次git commit或git add时的状态。  
	注意：`git checkout -- file`命令中的 `--`很重要，没有`--`，就变成了“切换到另一个分支”的命令。  
2. 用命令`git reset HEAD file`可以把暂存区的修改撤销掉（unstage），重新放回工作区：
	
		# git checkout HEAD readme.txt

### 删除文件
1. 在工作区删除文件，直接使用对应的删除命令即可。
2. 要彻底删除仓库中的文件用命令`git rm` ＋ `git commit`：
		
		# git rm readme.txt
		# git commit -m “remove readme.txt"

3. 命令`git checkout -- readme.txt`可以很轻松地把误删的文件恢复到最新版本。

## 分支管理
### 分支介绍
在版本回退里，已经知道，每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。在Git中永远会有一条时间线，这条时间线是在创建仓库的时候，Git自动生成的，这个分支叫主分支，即master分支。HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。  
一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点。之后每次提交，master分支都会向前移动一步，这样，随着不断提交，master分支的线也越来越长。  
当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，此时dev指向了master所指向的提交，当把HEAD指向dev示，就表示当前分支在dev上。所以说Git创建一个分支很快，因为除了增加一个dev指针，改改HEAD的指向，工作区的文件都没有任何变化。  
当HEAD指向dev时，对工作区的修改和提交就都针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变，仍然停留在原位置。  
当我们在dev上的工作完成了，就可以把dev合并到master上。Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并。所以Git合并分支也很快！就改改指针，工作区内容也不变！合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支。
### 分支相关命令
1. 创建dev分支，然后切换到dev分支`git checkout -b dev`：
		
		$ git checkout -b dev
		Switched to a new branch 'dev'
	git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
		
		$ git branch dev
		$ git checkout dev
		Switched to branch 'dev'
2. 查看当前分支`git branch`：
		
		$ git branch
		* dev
		  master
	`git branch`命令会列出所有分支，当前分支前面会标一个*号。
3. 切换当前分支`git checkout <name>`：
		
		$ git checkout master
		Switched to branch 'master'
	`git branch master`命令切换当前分支到master上。
4. 合并分支－把dev分支的工作成果合并到master分支上`git merge dev`：

		$ git merge dev
		Updating d17efd8..fec145a
		Fast-forward
		 readme.txt |    1 +
		 1 file changed, 1 insertion(+)
	`git merge`命令用于合并指定分支到当前分支（注意：所以合并分支前要先确定当前分支和要合并的分支）。  
	注意：上面的Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。当然，也不是每次合并都能Fast-forward，我们后面会讲其他方式的合并。
5. 删除分支`git branch -d <name>`:
		
		$ git branch -d dev
		Deleted branch dev (was fec145a).
	合并完成后就可删除分支了，删除后，再查看branch，就只剩下master分支了。  
	因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但过程更安全。  
6. 强制删除分支`git branch -D <name>`: 用于删除已经提交但还没有合并的分支。
6. 用带参数的`git log `查看分支的合并情况：

		$ git log --graph --pretty=oneline --abbrev-commit
		*   59bc1cb conflict fixed
		|\
		| * 75a857c AND simple
		* | 400b400 & simple
		|/
		* fec145a branch test
		...


### 解决冲突
冲突产生：分支dev修改了文件，并进行了上传，此时切换到master后，没有将dev进行合并，并且改动和dev中的同一个文件，改动结束直接上传了文件，此时再将dev进行合并时会出现冲突。  

解决方案：Git用`<<<<<<<，=======，>>>>>>>`标记出不同分支的内容，进入有冲突的文件，删除掉Git用`<<<<<<<，=======，>>>>>>>`，并重新再master进行add和commit操作。

### 分支管理策略
1. 使用参数`--no-ff`(普通模式)保留分支的commit信息   
通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息，即合并后分支dev始终指向master所指向的版本，dev的commit信息会被丢弃，此时想在dev分支中进行会退是不可能的。  
如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。而要想实现这一功能，就得使用`git commit`的参数`--no-ff`：

		$ git merge --no-ff -m "merge with no-ff" dev
		Merge made by the 'recursive' strategy.
		 readme.txt |    1 +
		 1 file changed, 1 insertion(+)
本次合并会创建一个新的commit，所以加上-m参数，把commit描述写进去。  
此时再使用`git log`将会看到dev的时间线：

		$ git log --graph --pretty=oneline --abbrev-commit
		*   7825a50 merge with no-ff
		|\
		| * 6224937 add merge
		|/
		*   59bc1cb conflict fixed
		...
		
2. 分支策略  
在实际开发中，我们应该按照几个基本原则进行分支管理：  

	- 首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
	- 那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
	- 你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支（该分支是从dev分支上下来的），时不时地往dev分支上合并就可以了。
		
### bug分支
软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。修复前要首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支。  
假如要在master上修复，而此时再dev上的工作还没有完成，怎么办？

1. 使用命令`git stash`把当前工作现场“储藏”起来，等以后恢复现场后继续工作：
		
		# $ git stash
		Saved working directory and index state WIP on dev: 6224937 add merge
		HEAD is now at 6224937 add merge
2. 使用命令`git stash list`查看刚才储存的工作现场：

		$ git stash list
		stash@{0}: WIP on dev: 6224937 add merge
3. 恢复储存的工作现场
	- 使用命令`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除;
	- 使用命令`git stash pop`，恢复的同时把stash内容也删了

### 远程管理&多人合作
当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。
1. 查看远程库信息，使用`git remote`或者`git remote -v`命令
2. 推送分支`git push <远程仓库名> <name>`：

		$ git push origin dev
3. 将远程仓库中的dev分支创建到本地：

		$ git checkout -b dev origin/dev
4. 抓取分支`git pull`:

		$ git pull
	`git pull`命令会最新的提交从origin/dev抓下来，并在本地与本地的dev分支进行合并，如果本地的dev与远程的origin/dev没有关联，则使用命令时会报错，此时需要使用命令`git branch --set-upstream dev origin/dev`进行设置。
5. 多人协作：
多人协作的工作模式通常是这样：

	- 首先，可以试图用git push origin branch-name推送自己的修改；
	- 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
	- 如果合并有冲突，则解决冲突，并在本地提交；
	- 没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！  

	如果`git pull`提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream branch-name origin/branch-name`。

## 标签管理
### 标签介绍
发布一个版本时，我们通常先在版本库中打一个标签，这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。  
Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

### 标签管理
1. 创建标签`git tag <tag name> <branch commit id>`,默认是给HEAD打标签：
		
		$ git tag v1.0
		$ git tag v0.9 6224937
2. 创建带有说明的标签，用-a指定标签名，-m指定说明文字`git tag -a <tag name> -m <tag description> <branch commit id>`：
		
		$ git tag -a v0.1 -m "version 0.1 released" 3628164
3. 创建PGP签名标签，通过-s表明用私钥签名一个标签`git tag -s <tag name> -m <tag description> <branch commit id>`：

		$ git tag -s v0.2 -m "signed version 0.2 released" fec145a
4. 查看标签`git tag`:
		
		$ git tag
	注意，标签不是按时间顺序列出，而是按字母排序的。
5. 查看标签信息`git show <tagname>`:

		$ git show v0.9
6. 推送标签到远程`git push <远程仓库名> <tagname>`:

		$ git push origin v1.0
		.....

7. 一次性推送全部尚未推送到远程的本地标签`git push <远程仓库名> --tags`:

		$ git push origin --tags
		Total 0 (delta 0), reused 0 (delta 0)
		To git@github.com:michaelliao/learngit.git
		 * [new tag]         v1.0 -> v1.0
8. 删除标签信息

	- 标签尚未推送到远程仓库`git tag -d <tagname>`:

			$ git tag -d v0.1
			Deleted tag 'v0.1' (was e078af9)
	- 标签已经被推送到远程仓库`git tag -d <tagname>` ＋ `git push <远程仓库名> :refs/tags/<tagname>`：
			
			＃首先从本地删除
			$ git tag -d v0.9
			Deleted tag 'v0.9' (was 6224937)
			＃然后从本地删除
			$ git push origin :refs/tags/v0.9
			To git@github.com:michaelliao/learngit.git
			 - [deleted]         v0.9


## 其它命令
1. 检查命令`git check-ignore -v <file name>`
如果再使用`git add <file name>`新增文件时会出错，有可能是文件.gitignore文件的问题，此时可使用以上的命令进行检查
2. 强制添加文件`git add -f <file name>`
3. 配置别名：
	
		$ git config --global alias.st status
		设置git status的别名，设置后 git st 等价与 git status  
		--global参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用，但--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用
4. 配置文件：
	
	- 每个仓库的Git配置文件都放在.git/config文件中
	- 当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中

设置别名时，如果使用--global则设置后会把别名放在.gitconfig中的[alias]后面，如果不是用--global则会把别名只放在本地仓库的.git/config中的[alias]后面

## 问题
1. 什么是工作区？什么是暂存区？
工作区就是你存放文件的目录。工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库（Repository）。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。  
我们把文件往Git版本库里添加的时候，是分两步执行的：  

	-  第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；  
	-  第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。  

因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，git commit就是往master分支上提交更改。
你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。
2. 为什么Git比其他版本控制系统设计得优秀？
因为Git跟踪并管理的是修改，而非文件。你会问，什么是修改？比如你新增了一行，这就是一个修改，删除了一行，也是一个修改，更改了某些字符，也是一个修改，删了一些又加了一些，也是一个修改，甚至创建一个新文件，也算一个修改。

## 学习资源
1. http://git-scm.com
2. http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000
3. https://guides.github.com/activities/hello-world/