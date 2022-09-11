---
draft: false
title: 'dot file automation'
categories: [ blog ]
tags: [ tools ]
date: 2018-12-02-T08:08:50-04:00
---
# dot file automation

Sharpen and clean your tools to make them last longer. Hone your tools to make them more precise. The tools of a SRE start with the terminal. The terminal is honed sharp with dotfiles.

Live your life long enough in a terminal and tweaks and customizations happen. Live longer and when those tweaks are lost from a catastrophe or when you switch machines, you will be hamstrung and saddened going back to nothing.

Phase 1: maintain my dot files for tmux, bash, git and vim in a GitHub repo, and then manually symlink from the repo to my home directory.

Phase 2: automate all of the configuration setup process and the supporting software.

Complexity 1: half a dozen machines.

Complexity 2: the machines were split between Mac and linux.

Complexity 3: a couple of the linux VMs, want to be run as root.
-Rage note about root- These are kali and security distributions that are not meant to be long lived, but I will be on the long enough to want a toolset.

The dot files are arranged in a shell script that will be run and then the proper bash git tmux and vim files are symlinked and used based on the os and the user that I am running as.

If there is anything that I’ve done horribly wrong with these I would really like to hear about it, these are the dot files that I have dragged around and revised for the past 5 years now or so, and we all know that looking t anything that was written even 6 months ago, we can think of way better ways of doing things.

Let’s dive right in.
```
#!/bin/bash
#..setup.sh .  double dots because gist, gets auto sorted (ugh)
# Hi im a penguin
if [ "Linux" = "$(uname -a | awk '{printf $1}')" ] 

then
	# this is handled is submodoules
	#git clone https://github.com/magicmonty/bash-git-prompt.git ~/.bash-git-prompt --depth=1

	#create symlinks
	#root because kali
    if [ "root" = "$(whoami)" ] 
    then
	    ln -s "$(pwd)"/.tmux.conf /"$(whoami)"/.tmux.conf 
	    ln -s "$(pwd)"/.vimrc /"$(whoami)"/.vimrc 
	    ln -s "$(pwd)"/.bashrc /"$(whoami)"/.bashrc 
	    ln -s "$(pwd)"/.gitconfig /"$(whoami)"/.gitconfig 

	    #not root like a sane person
	    else
	    ln -s "$(pwd)"/.tmux.conf /home/"$(whoami)"/.tmux.conf 
	    ln -s "$(pwd)"/.vimrc /home/"$(whoami)"/.vimrc 
	    ln -s "$(pwd)"/.bashrc /home/"$(whoami)"/.bashrc 
	    ln -s "$(pwd)"/.gitconfig /home/"$(whoami)"/.gitconfig 
    fi

	#install latest ripgrep
	rg_tag=$(curl --silent "https://api.github.com/repos/BurntSushi/ripgrep/releases/latest" |grep '"tag_name":'  | sed -E 's/.*"([^"]+)".*/\1/' )
	curl -LO https://github.com/BurntSushi/ripgrep/releases/download/"$rg_tag"/ripgrep_"$rg_tag"_amd64.deb
	sudo dpkg -i ripgrep_"$rg_tag"_amd64.deb
	rm ripgrep_"$rg_tag"_amd64.deb

fi

# hi I'm a mac
if [ "Mac" = "$(sw_vers|grep ProductName |awk '{printf $2}')" ]
then
    ln -s "$(pwd)"/.tmux.conf /Users/"$(whoami)"/.tmux.conf 
    ln -s "$(pwd)"/.vimrc /Users/"$(whoami)"/.vimrc 
    ln -s "$(pwd)"/.bash_profile /Users/"$(whoami)"/.bash_profile
    ln -s "$(pwd)"/.gitconfig /Users/"$(whoami)"/.gitconfig 

    brew install ripgrep
    brew install ranger

fi

mkdir -p ~/.vim/autoload ~/.vim/bundle && \
    curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
```

```
#bash_profile
GIT_PROMPT_ONLY_IN_REPO=1
  source ~/.bash-git-prompt/gitprompt.sh

export PATH=/usr/local/sbin:/usr/local/bin:$PATH
export PATH="/Users/$(whoami)/platform-tools/:/usr/local/opt/python/libexec/bin:$PATH"
export PATH=~/.local/bin:$PATH

alias gpox='git push origin `git rev-parse --abbrev-ref HEAD`'
alias weather="curl wttr.in/<your city>"
alias docker_logs_latest="docker logs -f "$(docker ps -l -q)" "

## a quick way to get out of current directory ##
alias ..='cd ..'
alias ...='cd ../../../'
alias ....='cd ../../../../'
alias .....='cd ../../../../'
alias .4='cd ../../../../'
alias .5='cd ../../../../..'

alias vi=vim
alias ping='ping -c 5'
```

```
#.bashrc
# things for git
GIT_PROMPT_ONLY_IN_REPO=1
source ~/.bash-git-prompt/gitprompt.sh

alias gpox='git push origin `git rev-parse --abbrev-ref HEAD`'

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# If set, the pattern "**" used in a pathname expansion context will
# match all files and zero or more directories and subdirectories.
#shopt -s globstar

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color|*-256color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
#force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
	# We have color support; assume it's compliant with Ecma-48
	# (ISO/IEC-6429). (Lack of such support is extremely rare, and such
	# a case would tend to support setf rather than setaf.)
	color_prompt=yes
    else
	color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
unset color_prompt force_color_prompt

# If this is an xterm set the title to user@host:dir
case "$TERM" in
xterm*|rxvt*)
    PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
    ;;
*)
    ;;
esac

# enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    #alias dir='dir --color=auto'
    #alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# Add an "alert" alias for long running commands.  Use like so:
#   sleep 10; alert
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
```

```
#.gitconfig
[alias]
    # abbreviations
    st = status -s
    ch = checkout
    co = commit
    br = branch
    # watch log
    watch = "!f() { while [ 1 ]; do clear; git log --all --oneline --decorate --graph; sleep 3; done; }; f"
    # pretty log with graph output
    lg = log --graph --pretty=format:'%C(yellow)%h%Cred -%C(auto)%d%Creset %s %Cgreen(%cr) %C(cyan)<%an>%Creset' --decorate --abbrev-commit --date=relative
    # pretty log file details
    ll = log --pretty=format:'%C(yellow)%h%Cred%d %Creset%s%Cblue [%cn]' --decorate --numstat
    # pretty log with dates
    ld = log --pretty=format:'%C(yellow)%h %Cred%ad %Cblue%an%Cgreen%d %Creset%s' --date=local
    # pretty log with dates, first parent only
    lf = log --pretty=format:'%C(yellow)%h %Cred%ad %Cblue%an%Cgreen%d %Creset%s' --date=local
    # prettier blame
    who = blame -c --show-stats --date=relative
    # prettier and concise whatchanged
    what = whatchanged --pretty=format:'%C(yellow)%h %Cred%ad %Cblue%an%Cgreen%d %Creset%s' --date=local --stat
    # prettier whatchanged with full diffs
    whatwhat = whatchanged --pretty=format:'%C(yellow)%h %Cred%ad %Cblue%an%Cgreen%d %Creset%s' --date=local -p
    # prettier whatchanged with full diffs based on text search
    whatwhen = whatchanged --pretty=format:'%C(yellow)%h %Cred%ad %Cblue%an%Cgreen%d %Creset%s' --date=local -p -S
    # search the log for when given text was introduced
    when = log -S
    # list of files that have changed (with status)
    changes = diff --name-status
    # list of files that have changed
    changed = diff --name-status
    # diff - ignore whitespace
    wdiff = diff -w
    # smart diff shows the changes that matter on a very granular level
    smartdiff = diff -w -b --patience
    # list of changes since current branch diverge from master branch
    mdiff = "!f() { git fetch; git diff origin/master...HEAD; }; f"
    # list of changes since current branch diverge from master branch - ignore whitespace
    mwdiff = "!f() { git fetch; git diff -w origin/master...HEAD; }; f"
    # list of files that have changed (with status) since current branch diverge from master branch
    mchanges = "!f() { git fetch; git diff --name-status origin/master...HEAD; }; f"
    # list of files that have changed since current branch diverge from master branch
    mchanged = "!f() { git fetch; git diff --name-only origin/master...HEAD; }; f"
    # diff between two branchs
    bdiff = "!f() { git diff $1...HEAD; }; f"
    # list of files that have changed (with status) between two branches
    bchanges = "!f() { git diff --name-status $1...HEAD; }; f"
    # list of files that have changed between two branches
    bchanged = "!f() { git diff --name-only $1...HEAD; }; f"
    # get diff between HEAD and origin/HEAD
    odiff = diff HEAD...origin/HEAD
    # get diff between HEAD and origin/HEAD
    ochanges = diff --name-status HEAD...origin/HEAD
    # find files
    f = "!git ls-files | grep -i"
    # find files containing text
    g = grep -Ii
    # find out which branch contains a change
    contains = branch --contains
    # pretty print all commits which were introduced by a given merge. NOTE: requires merge commit as an argument!
    introduced = "!f() { git log $(git merge-base --octopus $(git show $1 --format='%P'))..$(git show $1 --format='%H') --boundary --graph --pretty=oneline --abbrev-commit; }; f"
    # used after a revert to show commands you'll need to run in order to reintroduce commits previously introduced by a given merge commit. NOTE: requires merge commit as an argument!
    reintroduce = "!f() { git log $(git merge-base --octopus $(git show $1 --format='%P'))..$(git show $1 --format='%H') --format='git cherry-pick %H' --no-merges --reverse; }; f"
    # list branchs that exist on local only
    localonly = "!f() { LOCALS=$(git branch --list --no-color | cut -c 3-);REMOTES=$(git branch --all --no-color | grep 'remotes/origin' | grep -v HEAD | awk -F'/' '{print $3}');for R in $REMOTES; do LOCALS=$(echo \"$LOCALS\" | grep -v $R); done; echo \"$LOCALS\"; }; f"
    # leaderboard
    leaderboard = shortlog -s -n
    # hard reset
    hard = reset --hard
    # hard reset to HEAD
    hardhead = reset --hard HEAD
    # hard reset to first parent
    undo = reset --hard @^
    # head commit
    head = rev-parse HEAD
    # head commit
    shorthead = rev-parse --short HEAD
    # cherry pick
    pick = cherry-pick -x
    # stash
    save = stash -u
    # stash pop
    pop = stash pop
    # copy given commit hash
    copy = "!f() { REF=${1:-HEAD}; git rev-parse --short $REF | tr -d '\n' | pbcopy; }; f"
    # fixup commit
    fixup = !sh -c 'git commit -m \"fixup! $(git log -1 --format='\\''%s'\\'' $@)\"' -
    # squash commit
    squash = !sh -c 'git commit -m \"squash! $(git log -1 --format='\\''%s'\\'' $@)\"' -
    # interactive rebase
    ri = rebase --interactive --autosquash
    # svn update commit
    svnupdate = "!f() { svn update; git add -A; git commit -m 'svn update'; }; f"
    # unapply stash
    stash-unapply = !git stash show -p --no-color | git apply -R
    # reset to guarenteed state
    goto = "!f() { git fetch; git reset --hard $1; git clean -d -f; }; f"
    # pull rebase
    pullr = pull --rebase
    # freebase
    freebase = "!f() { git fetch; git rebase `git remote show | head -1`/$1; }; f"
    # freeset
    freeset = "!f() { git fetch; git reset --hard `git remote show | head -1`/$1; }; f"
```

```
#.gitmodules
[submodule ".bash-git-prompt"]
	path = .bash-git-prompt
	url = git@github.com:magicmonty/bash-git-prompt.git
[submodule ".vim/bundle/nerdtree"]
	path = .vim/bundle/nerdtree
	url = git@github.com:scrooloose/nerdtree.git
[submodule ".vim/bundle/syntastic"]
	path = .vim/bundle/syntastic
	url = https://github.com/scrooloose/syntastic.git
```

```
#.tmux.conf
# color
set -g default-terminal "xterm-256color"
set -g status-fg white
set -g status-bg black
setw -g window-status-fg cyan
setw -g window-status-bg default
setw -g window-status-attr dim
setw -g window-status-current-fg white
setw -g window-status-current-bg red
setw -g window-status-current-attr bright
set -g pane-border-fg green
set -g pane-border-bg black
set -g pane-active-border-fg white
set -g pane-active-border-bg yellow
set -g message-fg white
set -g message-bg black
set -g message-attr bright
set-option -g history-limit 100000
#vim stuff
setw -g mode-keys vi
bind -Tcopy-mode-vi 'y' send -X copy-selection-and-cancel
bind -Tcopy-mode-vi 'y' send -X copy-pipe-and-cancel "reattach-to-user-namespace pbcopy"

set-option -g mouse on

# syncing sessions
bind s setw synchronize-panes 

# window & pane control
bind | split-window -h
bind - split-window -v
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R
bind -r C-h select-window -t :-
bind -r C-l select-window -t :+
bind K swap-pane -t 1
bind J swap-pane -t 2
bind L swap-pane -t 2
bind H swap-pane -t 1
bind b previous-window

#home and end keys
bind -n End send-key C-e
bind -n Home send-key C-a

set -g mode-keys vi

# easily reload conf
bind r source-file ~/.tmux.conf \; display "Reloaded!"

set-option -g default-command "/bin/bash -c 'which reattach-to-user-namespace >/dev/null && exec reattach-to-user-namespace $SHELL -l || exec $SHELL -l'"
```

```
.vimrc
syntax on
filetype plugin indent on
set nocompatible
execute pathogen#infect()
set statusline+=%#warningmsg#
set statusline+=%*
set laststatus=2

" [buffer number] followed by filename:
 set statusline+=[%n]\ %F
" for Syntastic messages:
set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*
" show line#:column# on the right hand side
set statusline+=%=%l:%c

set number

" no swap files
set noswapfile
" don't write to a backup file then delete the original and then rename the
" backup to the name of the original file... it's annoying
set nowritebackup

" persistent undo
set undofile 
set undodir=~/.vim/undodir


" easier switch to split
nmap <C-h> <C-w>h
nmap <C-l> <C-w>l
nmap <C-k> <C-w>k
nmap <C-j> <C-w>j

" changing tabs to spaces
set expandtab ts=4 sts=4 sw=4

"setting autoindent
set ai

" Ignore case when searching
set ignorecase

" copy paste from system clipboard
set clipboard=unnamed
vmap ç "*y
vmap ≈ "*d
nmap √ :set paste<CR>"*p:set nopaste<CR>
imap √ <ESC>√

" toggle paste mode with <F1> ... this way you can leave autocommenting on
" most of the time a quickly disable it for pasting in code
set pastetoggle=<F1>
set showmode
```

