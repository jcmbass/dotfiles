# dotfiles 

Here's a bunch of settings for the tools I use. 
This documentation is ment to get everything installed and
configured.

### Documentation
- [Quickly get set up with these dotfiles](#quickly-get-set-up-with-these-dotfiles)
  - [Arch](#arch)(native or Manjaro)
  - [Other](#other-linux-distribution)
  - [Windows](#windows-wsl)
- [Setup](#setup)
- [Extra WSL 1 and WSL 2 steps](#extra-wsl-1-and-wsl-2-steps)
- [FAQ](#faq)
  - [How to personalize these dotfiles?](#how-to-personalize-these-dotfiles)
  - [How to fix Vim taking a long time to open when inside of WSL?](#how-to-fix-vim-taking-a-long-time-to-open-when-inside-of-wsl)
- [About the author](#about-the-author)

## Quickly Get Set Up with These Dotfiles

Beginning to end installation instructions for Arch-based
distribution (Manjaro) along with WSL.

### OS / distro specific installation steps

This set up targets tmux 3.0+ and Vim 8.1+. As long those
requirements are met we are good to go.

We are going to use terminator and zsh as default shell in 
order to get proper theming and extended functionallity

## Arch

If you are running base Arch, you'll want to install yay for
managing packages.

### Manjaro 20.2 Nibia

#### zsh

I'll be using ZSH and [powerlevel10k](https://github.com/romkatv/powerlevel10k) for cool theme.

```sh
# Install zsh first
yay -S zsh
```

Finish the conversion by changing your user in /etc/passwd to /bin/zsh instead of /bin/bash

or typing `chsh $USER` and entering `/bin/zsh`


```sh
# Run this befor movin on.
sudo yay -S \
  tmux \
  vim \
  terminator \
  autojump \
  chromium \
  pass \
  jq \
  zsh-syntax-highlighting \
  autojump \
  zsh-autosuggestions \
  fzf \
  ripgrep \
  nodejs \    # javascript
  npm         # javascript packages
```

###### macOS like gestures

If you like to have macOS gestures on Linux. It can be achieve with [Fusuma](https://github.com/iberianpig/fusuma)

read carefully to correctly implement on your linux distros.

### Other Linux distros
If you're using Debian or Ubuntu that's ok. You can change
the `yay -S` commands above for `apt` command. Just be mindfull
you might have to add the proper "ppa" repository to install 
some packages or compile it from source.

### Windows WSL

First follow the next steps and once your Linux Subsystem for Windows is up and Running you can 
install all the dependencies we saw previously. 

[Source Article](https://docs.microsoft.com/en-us/windows/wsl/install-win10) 
I copy these because Microsoft is notorious for changing URLs
and to make additions.

Before installing any Linux distributions on Windows, 
you must enable the “Windows Subsystem for Linux” optional 
feature.

```sh
# Open PowerShell as Administrator and run:
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

To only install WSL 1, you should now restart your machine 
and move on to Install your Linux distribution of choice, 
otherwise wait to restart and move on to update to WSL 2.

##### Update to WSL 2

To update to WSL 2, you must meet the follow criteria:
- Running Windows 10, updated to version 2004, Build 19041 or higher
- Check your Windows version by selecting the Windows logo key + R, type `winver`, select OK. (Or enter the ver command in Windows Command Prompt). Please update to the latest Windows version if your build is lower than 19041. Get Windows Update Assistant.
- Enable the ‘Virtual Machine Platform’ optional component

Before installing WSL 2, you must enable the “Virtual Machine Platform” optional feature.

```sh 
# Open PowerShell as Administrator and run:
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Restart your machine to complete the WSL install and 
update to WSL 2.

##### Set WSL 2 as your default version

Run the following command in Powershell to set WSL 2 as 
the default version when installing a new Linux distribution:

```sh
wsl --set-default-version 2
```

###### Troubleshooting WSL 2 set-default error

If you can't set the default version after enabling the 
virtual machine platform and linux subsystem for windows. 
Then you need to do a kernel upgrade. This done by running 
the following msi file:
[https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

##### Install your Linux distribution of choice

Open the Microsoft Store and select your favorite Linux distribution.

##### Install New Windows Terminal

Follow the instructions as per the following site:
[https://github.com/microsoft/terminal](https://github.com/microsoft/terminal)

## Setup

Once we are on ZSH and installed all the dependencies we can move forward setting the dotfiles.

```
touch "$HOME/.cache/zshhistory"
#-- Setup Alias in $HOME/zsh/aliasrc
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >> ~/.zshrc
```

### Install these dotfiles and various tools on your system

```sh
# Clone down this dotfiles repo to your home directory. Feel free to place
# this anywhere you want, but remember where you've cloned things to.
git clone https://github.com/jcmbass/dotfiles ~/dotfiles

# Create symlinks to various dotfiles. If you didn't clone it to ~/dotfiles
# then adjust the ln -s symlink source (left side) to where you cloned it.
#
# NOTE: The last one is WSL 1 / 2 specific. Don't do it on native Linux / macOS.
mkdir -p ~/zsh && mkdir -p ~/.local/bin && mkdir -p ~/.vim/spell && mkdir -p ~/.config/fusuma/ \
  && ln -s ~/dotfiles/zsh/aliasrc ~/zsh/aliasrc \
  && ln -s ~/dotfiles/.gitconfig ~/.gitconfig \
  && ln -s ~/dotfiles/.tmux.conf ~/.tmux.conf \
  && ln -s ~/dotfiles/.vimrc ~/.vimrc \
  && ln -s ~/dotfiles/.vim/spell/en.utf-8.add ~/.vim/spell/en.utf-8.add \
  && ln -s ~/dotfiles/.config/fusuma/config.yml ~/.config/fusuma/config.yml \ 
  && sudo ln -s ~/dotfiles/etc/wsl.conf /etc/wsl.conf

# Create your own personal ~/.gitconfig.user file. After copying the file,
# you should edit it to have your name and email address so git can use it.
cp ~/dotfiles/.gitconfig.user ~/.gitconfig.user

# Install Plug (Vim plugin manager).
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
  https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

# Install TPM (Tmux plugin manager).
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# Install FZF (fuzzy finder on the terminal and used by a Vim plugin).
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && ~/.fzf/install

# Install Node through ASDF. Even if you don't use Node / Webpack / etc., there
# is one Vim plugin (Markdown Preview) that requires Node and Yarn.
asdf plugin-add nodejs https://github.com/asdf-vm/asdf-nodejs.git
bash ~/.asdf/plugins/nodejs/bin/import-release-team-keyring
asdf install nodejs 12.18.3
asdf global nodejs 12.18.3

# Install Yarn.
npm install --global yarn

# Install system dependencies for Ruby on Debian / Ubuntu.
#
# Not using Arch or Manjaro? Here's alternatives for macOS and other Linux distros:
#   https://github.com/rbenv/ruby-build/wiki#suggested-build-environment
yay  -S --needed base-devel libffi libyaml openssl zlib

# Install Ruby through ASDF.
asdf plugin-add ruby https://github.com/asdf-vm/asdf-ruby.git
asdf install ruby 2.7.1
asdf global ruby 2.7.1

# Install Ansible.
#
# If not using Arch you might need to use pip3 instead
pip install --user ansible
```

### Install plugins for Vim and tmux

```sh
# Open Vim and install the configured plugins. You would type in the
# :PlugInstall command from within Vim and then hit enter to issue the command.
vim .
:PlugInstall

# Start a tmux session and install the configured plugins. You would type in
# ` followed by I (capital i) to issue the command.
tmux
`I

# Swaping Escape key on your keyboard for the CapsLk key can be helpfull if you
# decide to use Vim as text editor.
# This can be easly done in Arch Linux by simply changing the Xorg.configutaion
# and changin system-wide configutaion by adding the next line to
# /etc/X11/xorg.conf.d/00-keyboard.conf 

  Option "XkbOptions" "caps:escape_shifted_capslock"

# Read your linux distro documentation to be sure the proper configutaion method
```

#### Optionally confirm that a few things work after closing and re-opening your terminal

```sh
# Sanity check to see if you can run some of the tools we installed.
ruby --version
node --version
ansible --version

# Check to make sure git is configured with your name, email and custom settings.
git config --list
```

Before you start customizing certain config files, take a look at the
[personalization question in the FAQ](#how-to-personalize-these-dotfiles).

### Extra WSL 1 and WSL 2 steps

In addition to the Linux side of things, there's a few config files that I have
in various directories of this dotfiles repo. These have long Windows paths.

It would be expected that you copy those over to your system while replacing
"jcmbass" with your Windows user name if you want to use those things, such as my
Microsoft Terminal `settings.json` file and others. Some of the paths may
also contain unique IDs too, so adjust them as needed on your end.

Some of these configs expect that you have certain programs or tools installed
on Windows.

Pay very close attention to the `c/Users/jcmbass/.wslconfig` file because it has
values in there that you will very likely want to change before using it.

Also, you should reboot to activate your `/etc/wsl.conf` file (symlinked
earlier). That will be necessary if you want to access your mounted drives at
`/c` or `/d` instead of `/mnt/c` or `/mnt/d`.

## FAQ

### How to personalize these dotfiles?

Chances are you'll want to personalize some of these files, such as various Vim
settings. Since this is a git repo you can always do a `git pull` to get the
most up to date copy of these dotfiles, but then you may find yourself
clobbering over your own personal changes.

Since we're using git here, we have a few reasonable options.

For example, from within this dotfiles git repo you can run `git checkout -b
personalized` and now you are free to make whatever changes that you want on
your custom branch.  When it comes time to pull down future updates you can run
a `git pull origin master` and then `git rebase master` to integrate any
updates into your branch.

Another option is to fork this repo and use that, then periodically pull and
merge updates. It's really up to you.

### How to fix Vim taking a long time to open when inside of WSL?

It primarily comes down to either VcXsrv not running or a firewall tool
blocking access to VcXsrv and it takes a bit of time for the connection to time
out.

You can verify this by starting Vim with `vim -X` instead of `vim`. This
will prevent Vim from connecting to an X server. This also means clipboard
sharing to your system clipboard won't work, but it's good for a test.

Vim will try to connect to that X server by default because `DISPLAY` is
exported in the `.bashrc` file. Installing and configuring VcXsrv as per these
dotfiles will fix that issue.

If it still persists, it might be a software firewall issue. You can open TCP
port 6000 and also restrict access to it from only WSL 2. This will depend on
which tool you're using to configure that but that should do the trick.

## About the Author

I'm a Computer Science Engenier Student and have been coding for the last 3+ years.
