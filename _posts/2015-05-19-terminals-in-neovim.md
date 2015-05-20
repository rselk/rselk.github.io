---
layout: ryan
title: "Terminals in Neovim"
description: ""
category: 
tags: []
---

#Terminals in Vim With Neovim
[![My Vim Setup](http://i.imgur.com/UMTi3cF.png)](http://i.imgur.com/rpwqMTx.png "Full Size")

I have been using Vim + [Tmux](http://tmux.sourceforge.net/ "Tmux") for years but recently my primary use case for using Tmux has been replaced with [Neovim](http://neovim.io/ "Neovim"). When running tests I am a fan of being able to split my screen so I can view the specs alongside code. Tmux worked well for this, however it comes with a few disadvantages. Pasting code between Tmux and Vim panes required use of the system register which is a pain. I could not use the power of Vim resisters on my console output. The other big disadvantage of tmux is that switching between tmux and vim panes can be difficult. Most setups have separate keys for switching between windows within vim and switching between windows in tmux. 


Neovim now includes terminal emulation. This means that I can run my terminal of choice (zsh) right from inside vim. I can still Ctrl-z / fg in and out of vim if I wish but this is a rare occurrence in my new workflow. Unlike Tmux splits Normal mode can be entered in the terminal. This allows for full access to vim commands and access to all vims registers. With Neovim we can have a standard keyboard configuration for switching windows.
Basics

To start a new Terminal session you can use :terminal or simply :term. This converts the current buffer to a terminal. The terminal has its own modes which are independent of Vim editing modes. When you startup a new terminal you will be put into its "Insert" mode. To escape this mode the default command is 

~~~bash
<C-\><C-n>.
~~~

###Commands
Neovim has put the terminal commands under a "t" namespace. To remap keys in the terminal mode you must do it like the following:

~~~bash
" .vimrc
tnoremap <Leader>e <C-\><C-n> 
“ This maps Leader + e to exit terminal mode. 
~~~

###Navigation

The power of these terminals will not be realized without becoming very efficient with Vim window navigation. <C-w> is the base for these commands.  Here is a quick list of commands which I use very frequently:

####Change Windows:
~~~bash
<C-w>h , <C-w>j , <C-w>k , <C-w>l
~~~

####Resize Windows:
~~~bash
<C-w>- , <C-w>+ , <C-w>< , <C-w>>
~~~

####Maximize Current Buffer:
~~~bash
<C-w>_
~~~

####Even out splits
~~~bash
<C-w>=
~~~

####Move Buffer:
~~~bash
<C-w>H , <C-w>J , <C-w>K , <C-w>L
~~~

####Rotate Buffers:
~~~bash
<C-w>R
~~~

####Break Buffer into own tab:
~~~bash
<C-w>T
~~~

Once you get used to these commands it becomes far quicker to move to a terminal window then to use Ctrl-z and fg to get in and out of vim. 

###Configuration

The default Neovim terminal settings are a little bit occward so I will share some options. <C-w> for the window commands I have always found difficult to use. My preference is to map <C-w> to the tab key. I create 100's of splits over the course of the day and this makes navigation quite a bit easier. I also suggest to re-map the escape terminal key. <C-\><C-n> feels very unnatural to me and I have remapped it to Esc using tnoremap <Esc> <C-\><C-n>.


###Conclusion
This is likely to be a popular feature in Neovim. Many plugins will be able to use this feature in ways we haven’t thought of yet. While similar functionality has been available via Tmux, having one program deal with this makes window navigation far more fluid. I will still be using Tmux for session managment but its role is greatly reduced. This feature has me sold on Neovim. I look forward to seeing this project proceed.  

