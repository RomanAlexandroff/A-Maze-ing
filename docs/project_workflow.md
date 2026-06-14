# Team Git Workflow
How to Work Together in a GitHub Repository as a Professional Development Team while working on a 42 Project

<br>

## The setup:

- 2 teammates, each uses a computer at school and another computer at home, each has a GitHub account;
- 1 GitHub repository

Before any serious work starts, every teammate should configure Git on every computer they use:
```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```
The email should be the same email connected to your GitHub account, so commits are correctly linked to the right GitHub profile.

<br>

# Working with Branches

Every professional GitHub repository shall operate with 3 types of branches:

- Production branch — usually it is main if not renamed. This branch shall contain only working, fully tested, bugs and memory-leaks free, code style compliant version of the project code. The Production branch shall be highly protected: deletion protection, direct commit block, incomming branch merge block. The only awailable way to change the Production branch shall be through Pull Requests
- Development branch — usually called dev. It is a branch of the Production branch. Can contain untested code, that is not fully compliant with the code style and is missing features but it always shall be able to compile - it can be just an empty main() function, but it has to compile. Can contain development-supporting scripts, documents, side programs, tests, etc. The Development branch shall be highly protected: deletion protection, direct commit block, incomming branch merge block. The only awailable way to change the Production branch shall be through Pull Requests
- Feature branches — It is your personal branch of the Development branch. A feature branch should normally be short-lived. It should exist only for one task or one small group of tightly connected changes. Feature branches are usually called like this DeveloperName_TaskNumber_TaskNameOrFeatureName, e.g.: roaleksa_#0007_user_input_secure_parsing. Every feature branch belongs to its developer, and the developer can do whatever he/she pleases in there in order to fulfill the undertaken task. The only thing to keep in mind: before opening the Pull Request to merge your feature branch into the Development branch, the program in the Feature branch must not only contain the newly developed feature, but also compile.

In school projects, this structure may feel strict, but it teaches the same habits used in professional software teams.
The most important rule is that unfinished work goes into feature branches, integrated work goes into `dev`, and stable work goes into `main`.

Surely, in some cases there can be more branches. There can be a Test branch where you push your code to be tested by the QnA team. You can have your own test branches from your feature branches. And so on.

It is completely fine that locally you do not have all the branches that the GitHub repository has. It is only important to have locally on your computer the main branch, the dev branch and the feature branch that you are currently working on. Everything else is optional.

You work only on your feature branches. Under common circumstances you would not work on your colleague's feature branches. There can be exceptions when two teammates intentionally work on the same feature branch together, but that should be discussed clearly before it happens.
You would absolutely NEVER work on the Dev branch and the Product branch!

Before switching branches, you should always make sure you do not have unfinished local changes:
```bash
git status
```
If `git status` shows uncommitted work, either commit it, stash it, or intentionally discard it before moving to another branch. Otherwise you may unintentionally create a mess in your git that you surely don't want to have on your computer and you ABSOLUTELY don't want to accidentally push to the GitHub repository!

<br>

<br>

# Typical Branches Workflow


### Getting started with a new task and a new feature branch
- You've taken a new task, now you need to create yourself a branch to work on it.
- First, update the local Development branch because someone could have updated the remote Development branch within the last minute and you DO want to have the latest version of it
```bash
git checkout dev
git pull origin dev
```
- Now, while still on the local dev branch, create yourself a local feature branch
```bash
git checkout -b username_#xxxx_some_cool_feature
```
- The previous command created your new local branch and switched to it at the same time. Now, immediately push this local feature branch to GitHub, even without any changes. That will create a remote mirror of your local branch. Stay on the feature branch and call
```bash
git push -u origin username_#xxxx_some_cool_feature
```
The `-u` part connects your local feature branch with the remote feature branch. After that, while you are on this branch, a simple `git push` usually knows where to push.


### Working on the task in progress
- You are free to work on the feature implementation. Do not forget to git commit and git push frequently.
- While you are working on your feature, your teammate may make some Pull Requests and merge their work from their feature branches into the Dev branch. You want to make sure, that you're always working with the latest version of the code. This is why once or twice a day you need to update your local dev branch from the remote dev branch:
```bash
git checkout dev
git pull origin dev
```
- If there are any changes to the dev branch, you must update the local feature branch you're currently working on with those updates. For that you need to merge the local dev branch into your local feature branch:
```bash
git checkout username_#xxxx_some_cool_feature
git merge dev
```
- Sometimes it merges without any issue, but sometimes you have to handle a Merge Conflict.
During conflict resolution, do not randomly delete your teammate's code. Read both versions carefully and keep the final version that makes sense. After resolving a Merge Conflict, compile and test the project again. A conflict can be textually resolved but still break the program.
After the Merge Conflict is resolved, save the files, then stage them, then complete the merge commit, and only then push:
```bash
git status
git add .
git commit
git push
```


### The task is finished, Time to make a Pull Request
- Before calling it a day, update this local feature branch from the remote dev branch for the last time. This is the same process you've been doing every day before working:
```bash
git checkout dev
git pull origin dev
git checkout username_#xxxx_some_cool_feature
git merge dev
```
- Resolve Merge Conflicts if necessary, run final tests, make sure the project compiles.
- When everything looks good
```bash
git push
```
- Now, you can go to the GitHub repository and open a Pull Request so your hard work can be merged into the remote Dev branch.
**SUPER IMPORTANT:** Make sure that the Pull Request target branch is set to `dev`, and NOT `main`. If you see it set to `main`, just click on it and set to `dev`.
A good Pull Request should contain:
- a title formatted the following way: Developer_name #Task_number Task_name
- a short explanation of what was changed
- screenshots or terminal output if they may help the reviewer;
- a mention of known limitations or questionable implementation decisions if any.


### Cleaning up before starting anew
- When the Pull Request you created is aproved, the remote feature branch gets merged into the remote `dev` branch and usually after that gets deleted. At this point you do not need to keep the local feature branch and can freely delete it locally too:
```bash
git checkout dev
git branch -d username_#xxxx_some_cool_feature
```
- Now, get yourself a new task and start it all over again.

<br>

<br>

# Typical Real-Life Work Day

Morning:
```bash
git checkout dev
git pull origin dev

git checkout username_#xxxx_some_cool_feature
git merge dev
```

Work:
```bash
git add .
git commit -m "implement parser"
```

More work:
```bash
git add .
git commit -m "add parser validation"
```

Save Backup to GitHub:
```bash
git push
```

Feature complete:
```bash
git push
```

Open Pull Request:
```text
username_#xxxx_some_cool_feature  →  dev
```

Review.
Merge.
Everyone updates:
```bash
git checkout dev
git pull origin dev
```

Repeat.


<br>

# Situations That are out of the Typical Workflow

## Situation:
I have a computer that I've been working on the project on. But now I am using a computer that does not have the project Git repository locally. I want to be able to work on the project on this computer just as I do it on another computer. I have access to my GitHub account.
## Desired outcome:
The second computer completely set up for flawless work on the project just like the first computer.
## Solution:
First, configure your Git identity:
```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```
Then clone the repository:
```bash
git clone https://github.com/OWNER/REPOSITORY.git A_Maze_ing_Work_Repo
cd A_Maze_ing_Work_Repo
```
Then fetch all remote branches:
```bash
git fetch origin
```
Then create local copies of the branches you need to have on this computer:
```bash
git checkout main
git pull origin main
git checkout -b dev origin/dev
```
If you also need your current feature branch:
```bash
git checkout -b username_#xxxx_some_cool_feature origin/username_#xxxx_some_cool_feature
```

<br>

## Situation:
I created a new local feature branch and pushed it to GitHub repository on the computer at work/school and now I need to get that feature branch onto my home/personal computer so I can continue working from where I stoped at work/school. I already have the project cloned onto my home/personal computer, it is only that particular feature branch that I do not have locally on the home/personal computer.
## Desired outcome:
To have a local feature branch on my home/personal computer that is a perfect copy of the local feature branch on my work/school computer so that I can continue working on it.
## Solution:
First, let your local Git know what is going on in the remote repository:
```bash
git fetch origin
```
Now, create a new local „username_#xxxx_new_cool_feature" branch and fill it up with everything that is contained in the remote repository „username_#xxxx_new_cool_feature" branch:
```bash
git checkout -b username_#xxxx_new_cool_feature origin/username_#xxxx_new_cool_feature
```
The local branch name and the remote repository branch name do not have to be identical. People usually give them the same names just for convenience. The solution works equally well if you create a new feature branch on your home computer and now need it on your work/school computer.

<br>

## Situation:
I have a feature that I am working on. At some point I asked my teammate to help me. They got my feature branch from GitHub onto their computer and for a while we were working on that feature together on their computer. We did do git commit and git push from their computer frequently. How do I get everything we were working on onto my local feature branch on my computer?
## Desired outcome:
All the work on my feature branch from the teammate's computer ends up on my computer.
## Solution:
This is a super simple task. Let's say that your feature branch is called "username_#xxxx_my_cool_feature".
First, on your computer, make sure that you are on that feature branch:
```bash
git checkout username_#xxxx_my_cool_feature
```
Now, fetch all commits from the remote feature branch and update your local feature branch:
```bash
git pull origin username_#xxxx_my_cool_feature
```
That's literally it.

<br>

## Situation:
I accidentally made commits directly on `dev` instead of creating a feature branch.
## Desired outcome:
The work is saved on a proper feature branch, and local `dev` returns to the same state as remote `dev`.
## Solution:
Do NOT git push `dev`.
While you are still on `dev`, create a feature branch from your current commits:
```bash
git checkout -b username_#xxxx_my_cool_feature
```
Push the feature branch to GitHub:
```bash
git push -u origin username_#xxxx_my_cool_feature
```
Now return local `dev` back to the remote `dev` state:
```bash
git checkout dev
git fetch origin
git reset --hard origin/dev
```
After that, switch to the feature branch and continue working on the feature branch:
```bash
git checkout username_#xxxx_my_cool_feature
```

<br>

## Situation:
My Pull Request has conflicts and GitHub says it cannot be merged automatically.
## Desired outcome:
The feature branch is updated with the newest `dev`, conflicts are resolved locally, and the Pull Request becomes mergeable again.
## Solution:
Update local `dev`:
```bash
git checkout dev
git pull origin dev
```
Go back to the feature branch:
```bash
git checkout username_#xxxx_my_cool_feature
```
Merge `dev` into the feature branch:
```bash
git merge dev
```
Resolve the conflicts in the files, then run:
```bash
git status
git add .
git commit
git push
```
After the push, GitHub should update the Pull Request automatically.

<br>

## Situation:
I started working on a file, but now I need to switch branches and Git does not let me because I have local changes.
## Desired outcome:
The current unfinished work is not lost, and I can safely switch branches.
## Solution:
First check what was changed:
```bash
git status
```
If the work is meaningful, commit it:
```bash
git add .
git commit -m "save current progress"
```
If the work is temporary and not ready for a commit, stash it:
```bash
git stash
```
Then switch branches:
```bash
git checkout dev
```
Later, if you used stash and want your changes back, return to the correct branch and call:
```bash
git stash pop
```

<br>

# Never Do These Things


## Never Force Push
Avoid:
```bash
git push --force
```

Why?
Can overwrite teammate's work.
Professional teams usually restrict it.


## Never Work Directly on Main
Avoid:
```bash
git checkout main
# coding...
git commit
```

Why?
Makes releases unstable.


## Never Work Directly on Dev
Avoid:
```bash
git checkout dev
# coding...
git commit
```

Why?
Feature branches isolate unfinished work.


## Never Commit Secrets
Never commit passwords, tokens, API keys, `.env` files, private certificates, or personal credentials.
Avoid:
```bash
git add .env
git commit -m "add env file"
```

Why?
Secrets committed to Git history can be difficult to remove safely.


## Never Delete a Branch Before You Know It Was Merged
Deleting an already merged feature branch is normal. Deleting an unmerged feature branch can destroy work if nobody else has a copy.
Before deleting, check:
```bash
git branch --merged
```

<br>

