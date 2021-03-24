# GIT BASICS

1. `git config user.name <name>`
Define author name to be used for all commits in current repo.
Devs commonly use `--global` flag to set config options for current user.

2. `git log`
Display the entire commit history using the default format.
For customization see additional options.

3. `git diff`
Show unstaged changes between your `index` and `working directory`.

`git diff --cached`: changes between `repository` and `index`

# UNDOING CHANGES

1. `git revert <commit>`
Create new commit that undoes all of the changes made in `<commit>`,
then apply it to the current branch.

2. `git reset <file>`
Remove <file> from the staging area, but leave the working directory
**unchanged**. This unstages a file without overwriting any changes.

3. `git clean -n`
Shows which files would be removed from working directory.
Use the `-f` flag in place of the `-n` flag to execute the clean.\

# REWRITING GIT HISTORY

1. `git commit --amend`
Replace the last commit with the staged changes and last commit
combined. Use with nothing staged to edit the last commit’s message.

2. `git rebase <base>`
Rebase the current branch onto `<base>`. `<base>` can be a commit ID,
branch name, a tag, or a relative reference to HEAD.

3. `git reflog`
Show a log of changes to the local repository’s HEAD.
Add `--relative-date` flag to show date info or `--all` to show all refs.

# GIT BRANCHES
1. `git branch`
List all of the branches in your repo. Add a <branch> argument to
create a new branch with the name <branch>.

2. `git checkout -b <branch>`
Create and check out a new branch named <branch>.
Drop the -b flag to checkout an existing branch.

3. `git merge <branch>`
Merge `<branch>` into the current branch

# REMOTE REPOSITORIES

1. `git remote add <name> <url>`
Create a new connection to a remote repo. After adding a remote,
you can use `<name>` as a shortcut for `<url>` in other commands.

2. `git fetch <remote> <branch>`
Fetches a specific `<branch>`, from the repo.
Leave off `<branch>` to fetch all remote refs.

3. `git pull <remote>`
Fetch the specified remote’s copy of current branch and
immediately merge it into the local copy.

4. `git push <remote> <branch>`
Push the branch to <remote>, along with necessary commits and
objects. Creates named branch in the remote repo if it doesn’t exist.