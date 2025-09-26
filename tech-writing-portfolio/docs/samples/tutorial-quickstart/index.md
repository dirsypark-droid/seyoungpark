# Git Tutorial: From Installation to Push

## Quick Navigation

- [What is Git?](#what-is-git)
- [Install Git](#install-git)
  - [Windows](#windows)
  - [macOS](#macos)
- [Configure your identity](#configure-your-identity)
  - [Verify your settings](#verify-your-settings)
- [Start a new project](#start-a-new-project)
- [Add and commit your first file](#add-and-commit-your-first-file)
- [Connect to GitHub and Push](#connect-to-github-and-push)
  - [Create a new repository on GitHub](#create-a-new-repository-on-github)
- [Add the remote repository](#add-the-remote-repository)
- [Push to GitHub](#push-to-github)
- [✅ Completed](#-completed)


## What is Git?

Git is the most widely used **Version Control System (VCS)** among developers around the world.

- It automatically records the change history of files (especially codes).
- Even with multiple people working simultaneously, it enables safe collaboration through **branches**.
-  It helps you manage version history locally even without an internet connection, and connect to remote repositories such as GitHub to collaborate from anywhere in the world.

This guide is intended for first-time Git users and walks you through installation, committing, and pushing step by step. All commands are executed in the Command Prompt (cmd).

---

## Install Git

### Windows

1. Download the installer from [git-scm.com](https://git-scm.com).

2. Run the installer and follow the setup wizard (default settings are fine for most users).

3. After installation, open **Command Prompt (cmd)** and check the version by running the code below:

```bash

git --version

```

Example output

```nginx

git version 2.x.x


```

### macOS

1. You can install Git via Homebrew:

```bash

brew install git

```

2. Or install **Xcode Command Line Tools**:

```bash

xcode-select --install

```

3. Verify the installation:

```bash

git --version

```

Example output

```nginx

git version 2.x.x


```


---

## Configure your identity

Before making commits, configure your username and email. These will be attached to each commit you make.

```bash

git config --global user.name "Your Name"
git config --global user.email "you@example.com"


```

- `--global` ensures the setting applies to all repositories on your machine.

- You can override these per project by running the same commands without `--global` inside a repository.

### Verify your settings

```bash

git config --list


```

Example output

```ini

user.name=Your Name
user.email=you@example.com

```

---

## Start a new project

1. Create a new folder.

```bash

mkdir my-first-git
cd my-first-git

```

2. Initialize Git.

```init

git init

```

Example output

```perl

Initialized empty Git repository in C:/Users/.../my-first-git/.git/


```

This means a hidden `.git` folder has been created to track changes.


---

## Add and commit your first file

1. Create a file.

```bash

echo "# My First Git Project" > README.md

```

2. Check the status.

```bash

git status

```

3. Stage the file.

```bash

git add README.md


```

4. Commit the change.

```bash

git commit -m "Add initial README"


```

Example output

```sql

[main (root-commit) abc1234] Add initial README
 1 file changed, 1 insertion(+)
 create mode 100644 README.md

```

---

## Connect to GitHub and Push

### Create a new repository on GitHub

1. Go to GitHub and log in.

2. Click **New repository**.

3. Enter a name (e.g., my-first-git).

4. Do not initialize with README (since you already created one locally).

5. Click **Create repository**.

You’ll see a page with repository URL, for example:

HTTPS: `https://github.com/your-username/my-first-git.git`

SSH: `git@github.com:your-username/my-first-git.git`

---

## Add the remote repository

1. In your project directory, associate your local repository with the GitHub repository.

```bash

git remote add origin https://github.com/your-username/my-first-git.git


```

2. Verify that the remote was added correctly.

```bash

git remote -v


```

Example output

```perl

origin  https://github.com/your-username/my-first-git.git (fetch)
origin  https://github.com/your-username/my-first-git.git (push)

```

---

## Push to GitHub

1. Upload your commits to the remote repository.

```bash

git branch -M main
git push -u origin main

```
- `git branch -M main`: renames the default branch to `main` if necessary.

- `-u`: sets `origin/main` as the upstream branch for future pushes and pulls.

Example output

```bash

Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 240 bytes | 240.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/your-username/my-first-git.git
 * [new branch]      main -> main

```

---

## ✅ Completed

Your project has now been uploaded to GitHub.
You can confirm by visiting `https://github.com/your-username/my-first-git` in your browser.

