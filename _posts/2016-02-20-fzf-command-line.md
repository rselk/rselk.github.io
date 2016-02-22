---
layout: ryan
title: "FZF command-line fuzzy finder"
description: ""
category: 
tags: []
---

# FZF command-line fuzzy finder

[FZF](https://github.com/junegunn/fzf) has quickly become one of my all time favorite tools. I use FZF 100s of times per day. FZF has greatly increased my productivity. Simply, FZF is a fuzzy command line search tool. FZF has 3 main search modes file search (Ctrl-t), directory search (Alt-c) and command line history search (Ctrl-r). It's one of the first tools I recommend to people who are at least intermediate at the unix command line.

The author, Junegunn Choi, better known as simply [junegunn](https://github.com/junegunn) is an amazing developer. Junegunn has and continues to make huge contributions to the open source ecosystem. I first learned of Junegunn because of several excellent vim plugins he created. Since following him I have adopted a very large percentage of his work in my daily workflow.

The functionality of FZF is excellent, no doubt, however the biggest selling point for FZF is the speed. With other fuzzy finding tools I had performance issues when dealing with large codebases. FZF is so fast I now no longer have to deal with the error prone practice of caching filenames.

### Tmux
FZF has tmux integration which I highly suggest using. Without Tmux integration the fuzzy search results are just opened up and take the full screen of the terminal. With the Tmux integration the results show up in a split pane.

## Usage
There are several ways to use FZF. 

### Opening single files
~~~bash
vim (Ctrl-T)
~~~
If you want to open a file, first select the program and then press Ctrl-T. Press enter again. Its that Simple.

Alternatively you can call vim with a fzf argument.

~~~bash
vim $(fzf)
~~~
 
### Opening multiple files
If you are using Ctrl-T to open files, you can just press the TAB key when you find the files you want. If you are calling fzf from the command directly line you will need to append the -m (multiple) flag. 

~~~bash
vim $(fzf -m)
~~~

### Searching
By default FZF uses "extended-search-mode", this means that you can provide multiple words separated by a space and it will filter based on both terms. 

Imagine the following files appear when we call fzf(Ctrl-T) and type "user", a situation very common in a Rails app:

~~~bash
/app/models/user.rb
/app/models/user_metadata.rb
/app/controller/users_controller.rb
/app/controller/users_metadata_controller.rb
/app/helpers/user_helper.rb
/spec/models/user_spec.rb
/spec/factories/user_factory.rb
/spec/controller/users_controller_spec.rb
/spec/controller/users_metadata_controller_spec.rb
/spec/helpers/user_helper_spec.rb
/spec/features/sign_in_user.rb
/spec/features/remove_user_account.rb
/spec/features/sign_in_user.rb
~~~

Pretend we are looking for spec/user_factory.rb

We may begin our search with the word "user", and then begin typing the word "factory" and we will only have to start typing the second word before we get the result we want. Infact we can go further and just type “u fa” in this case.


### Advanced Searching

#### Exact Searching
Add ^ to the beginning of the search term.

#### Extension Matching
Add $ to the end of the search term.

#### Wildcard Matching
Add ' when wildcard needed.

#### Inverse Matching
Add ! to beginning of search term


We can combine multiple search terms with the advanced options to come up with some very specific files without knowing their full path or even name.

### Conclusion

FZF can be customized far more then I covered in this post. Check it out and I assure you that you will be impressed with the speed and functionality of this tool.

Support [junegunn](https://github.com/junegunn) and [FZF](https://github.com/junegunn/fzf) by contributing on [Github](https://github.com).


