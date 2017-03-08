---
layout: ryan
title: "Underused Vim Features"
description: ""
category: 
tags: []
---

<style>
img {
    display: block;
    margin: 0 auto;
}
</style>

# Underused Vim Features

I was first introduced to Vim 11 years ago by a Computer Science professor I was working for. He told me after I learned vim, I would be hooked. He was correct. Below are a couple of features I feel are often underused or even unknown to many vim users.

## Vim Completion

Vim has very powerful completion commands. They are controlled with Ctrl-p which finds the previous match, and Ctrl-n which finds the next match. Vim can autocomplete many different things including lines and filepaths.

### Line Autocompletion

Ctrl-x-l then select with Ctrl-p and Ctrl-n

I typically use it by typing a few characters, pressing Ctrl-x-l and navigating with Ctrl-p and Ctrl-n.

<img src="http://i.imgur.com/PZJ0rij.gif" />

### File Autocompletion

Ctrl-x-f then select with Ctrl-p and Ctrl-n

I typically use it by typing Ctrl-x-f, selecting the directory I want and continuing down the tree with Ctrl-x-f.

<img src="http://i.imgur.com/x1YpFvG.gif" />
### Vim Expression Evaluation

When in insert mode, you can get vim to work as a calculator! While in insert mode simply use Ctrl-r then the = key. This will open a prompt on the bottom where you can type what you want evaluated. Press enter to get the result in your buffer.

<img src="http://i.imgur.com/wIzTh07.gif" />
