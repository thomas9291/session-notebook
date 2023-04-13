# Basic Setup for the Bootcamp 💻

## 0. Important note for Windows Users (version 10 or newer)

Windows hasn't always been an easy environment for software development. But Microsoft now offers a way to run Linux on top of a Windows system, with a tool called WSL (Windows Subsystem for Linux). If you're running Windows 10 or newer, please [install WSL](https://learn.microsoft.com/en-us/windows/wsl/install). This will make it a lot easier to install and run many of the tools and programs we will work with.

Please install the Ubuntu Linux distribution when installing WSL. This is the default recommendation in the link above.

## 1. Terminal

### Windows

Please use the Ubuntu terminal, which you installed with WSL earlier.

### Mac: iTerm2

You can use the integrated Terminal. In case you want some more convenience and a nicer look you can use iTerm2.
Install it [here](https://iterm2.com).

### Linux

You can use the integrated terminal installed in your Linux distribution.

## 2. Oh My Zsh - for an enhanced terminal experience

### Linux (including WSL)

- First, install `zsh` as follows:
- Open your terminal (WSL users, please be sure to always use the Ubuntu/WSL terminal! Never the Windows terminal)
- Paste the following command, and press Enter. Type in your password if asked.
- Please note that some systems will not show any characters as you type your password -- this is normal!

```bash
sudo apt-get install zsh
```

### All systems: Linux (including WSL), MacOS

- To install Oh My Zsh, please paste and run the following command in your terminal:

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

- At a later stage, if you want to update everything (e.g. plugins) in Oh My Zsh to the latest version:

```bash
omz update
```

## 3. VS Code

Visual Studio Code is the code editor that we will use to read and write code during the course.

1. Download and install Visual Studio Code from [here](https://code.visualstudio.com/download)

2. After downloading it, please install the following extensions:

   - [Auto Close Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag)
   - [Auto Rename Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)
   - [Markdown Preview Github Styling](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-preview-github-styles)
   - [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
   - [Live Preview](https://marketplace.visualstudio.com/items?itemName=ms-vscode.live-server)
   - [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
   - [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
   - _(**Optional**: [Excalidraw](https://marketplace.visualstudio.com/items?itemName=pomdtr.excalidraw-editor))_

3. Open Visual Studio Code settings (<kbd>⌘</kbd><kbd>,</kbd> on a Mac, or <kbd>Ctrl</kbd><kbd>,</kbd> on Windows/Linux)

   - Search for "Default Formatter" and set it to "Prettier - Code formatter"
   - Search for "Format On Save" and check the checkbox
   - > 💡 This will set up Visual Studio Code to automatically format your code with Prettier whenever you save it.

4. Allow opening VS Code with the `code` command from the terminal:
   - Open the command palette by pressing <kbd>⇧</kbd><kbd>⌘</kbd><kbd>P</kbd> (Mac) or <kbd>⇧</kbd><kbd>Ctrl</kbd><kbd>P</kbd> (Windows/Linux).
   - Type "shell command" into the prompt.
   - Select _"Shell Command: Install 'code' command in PATH"_ and confirm the installation by
     pressing <kbd>Enter</kbd>.
   - > 💡 This allows you to open a folder in Visual Studio Code from the command line by typing `code .`.

## 4. Node JS

### Mac and Windows (when not using WSL)

- Go to [nodejs.org](https://nodejs.org). Download and install the latest **LTS** (Long Term Support) version of Node. Installation should be quite straightforward.

### Linux (including WSL)

To install Node in Ubuntu (including WSL), please run each of the following commands:

```bash
sudo apt-get update
```

```bash
sudo apt-get install nodejs npm
```

Please enter your password if requested.

### Additional packages to install globally

This package will be used later to help run automated tests

```bash
npm install -g cross-env
```

If you see an error message about not having permission to do this action, please run the following instead:

```bash
sudo npm install -g cross-env
```

Please enter your password if requested.

## 5. Install Git

### Windows (if you are NOT using WSL)

Download and install the Git For Windows application from [here](https://gitforwindows.org). Once downloaded, find the .exe file and open it to install.

### Mac

- If you are using a relatively recent version of Mac's operating system, it is quite likely that you already have Git installed.
- To check it, open the Terminal.
  - First, open Spotlight search (you can do this by typing <kbd>⌘</kbd><kbd>Spacebar</kbd>)
  - Next, type _terminal_. If you installed `iTerm2` earlier, type `iterm2`. Press <kbd>Enter</kbd> or select the application with your touchpad.
- In the terminal, type `git` and press `<Enter>`. If you see a list of commands, Git is installed. If Git is not installed, you will be prompted to install it.
- You can also install Git [here](http://git-scm.com/download/mac).

### Linux (including WSL)

```bash
sudo apt-get install git
```

Please type your password if prompted.

## 6. Configure Git

When you've made sure Git is installed, you should set it up correctly by following [these instructions](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup#Your-Identity). The important commands to run are the following:

```bash
git config --global user.name "YOUR NAME"
```

Replace `YOUR NAME` with your real name, eg. Sally Jones.

```bash
git config --global user.email YOUREMAIL@example.com
```

Replace `YOUREMAIL@example.com` with your email address.

## 7. GitHub authentication: SSH setup

Sometimes you will interact with GitHub via the command line. GitHub will need to know who you are - so you will need to **authenticate**. The newest way to do this is with [personal access tokens](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) over HTTPS. An alternative is to use [SSH to connect](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh). Please follow these steps to set this up.

### Generate an SSH key

If you don't already have one, you will need to generate an **SSH key**. You can do this on the command line.

```bash
ssh-keygen -t ed25519 -C "YOUR_EMAIL@example.com"
```

Please replace `YOUR_EMAIL@example.com` with your email address.

You will be asked to confirm the location to store the files. Please use the default value.

You will then be prompted to enter a passphrase, and then confirm it.

> You should enter a passphrase that you won't mind typing repeatedly because you will be asked for this passphrase whenever you use your SSH key.

> ⚠️ If you don't enter something and hit enter, you will never be asked for a passphrase -- but this also means that your key will be saved on your computer in unencrypted plain text, which is not secure!

### Add your SSH key to ssh-agent

Adding your SSH key to `ssh-agent` will ensure that your key is used when you communicate with GitHub via SSH. First, make sure that `ssh-agent` is running. Run the following in your terminal:

```bash
eval "$(ssh-agent -s)"
```

Then add your SSH key with the following command:

```bash
ssh-add ~/.ssh/id_ed25519
```

### Paste the public key into GitHub

Print your public key on the command line so you can copy it.

```bash
cat ~/.ssh/id_ed25519.pub
```

Then go to [the page on GitHub for adding a new key](https://github.com/settings/ssh/new) and paste the SSH key you printed above, into the text field. You should also give your key a title, and then click the **Add SSH key** button.

### Test that it works

Please follow [these instructions](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection) to test if your SSH key has been set up correctly. If it hasn't, please get help from a teacher ☺️

## 8. Generate a Github token for accessing challenges on the terminal

You will soon need to download your tasks and challenges to your local computer. For this, you need to generate a token on github.com and copy add it on your local computer. Then, it will be used when copying the tasks/challenges to your local computer.

### 1. Generate a token on GitHub.com

- Go to github.com and login with your username
- on the top right click on your profile picture and select `settings`
- In the `settings` section scroll you need to select the `developer settings` on the left navigation on the very bottom
- In the `developer settings` go on the left side to `Personal access tokens`. Select `Tokens (classic)`
- Click on the top `Generate new token` -> `Generate new token (classic)`
  - Give the token a note like `token_for_accessing_repos`
  - select only the checkbox with `repo`
  - select an `expiration` of 90 days
  - click on the bottom `Generate token`
- Copy the generated token

### 2. Add the generated token to your local computer

1. In your terminal, go to your home directory by running `cd $HOME`
2. Create a file named `.bash_profile` (or `.zprofile` if you are using **Oh My Zsh**) by running `touch .bash_profile` or `touch .zprofile`
3. Open VS Code in the current directory by running `code .` in the terminal.
4. Add the following as a new line in the end of the file (`.bash_profile` or `.zprofile`):

```bash
export GITHUB_TOKEN="YOUR_COPIED_TOKEN"
```

Please replace "YOUR COPIED TOKEN" with the token that you copied from github.com.
