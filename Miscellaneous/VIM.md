# Vim

- [Vim](#vim)
- [Overview](#overview)
- [Vim plugins](#vim-plugins)
  - [Install plugins manually (Vim 8 and above)](#install-plugins-manually-vim-8-and-above)
  - [Installing plugins using Vim package manager](#installing-plugins-using-vim-package-manager)
    - [Installing plugins with vim-plug](#installing-plugins-with-vim-plug)
    - [Update plugins with vim-plug](#update-plugins-with-vim-plug)
    - [Restore plugins](#restore-plugins)
- [ViM Cheatsheet](#vim-cheatsheet)
  - [Global](#global)
  - [Cursor movement](#cursor-movement)
  - [Insert mode - inserting/appending text](#insert-mode---insertingappending-text)
  - [Editing](#editing)
  - [Marking text (visual mode)](#marking-text-visual-mode)
  - [Visual commands](#visual-commands)
  - [Registers](#registers)
  - [Tip Special registers](#tip-special-registers)
  - [Marks and positions](#marks-and-positions)
  - [Macros](#macros)
  - [Cut and paste](#cut-and-paste)
  - [Indent text](#indent-text)
  - [Exiting](#exiting)
  - [Search and replace](#search-and-replace)
  - [Search in multiple files](#search-in-multiple-files)
  - [Tabs](#tabs)
  - [Working with multiple files](#working-with-multiple-files)
  - [Diff](#diff)
- [defaults.vim](#defaultsvim)
- [Retaining the format as original source while paste in Vim](#retaining-the-format-as-original-source-while-paste-in-vim)
- [Vimrc Configuration - Vim Code Editor Customisation](#vimrc-configuration---vim-code-editor-customisation)
- [Map Keyboard Shortcuts in Vim](#map-keyboard-shortcuts-in-vim)
- [Configuring Color Schemes](#configuring-color-schemes)
- [vim.rc example](#vimrc-example)

# Overview

Vim is a Unix text editor that's included in Linux, BSD, and macOS. It's known for being fast and efficient and is the default fallback editor on all POSIX systems.

Vim is also commonly referred to as Vi because when it was written by Bill Joy in the late 1970s, it was short for visual editor.

# Vim plugins

Vim is extensible through plugins. With a history spanning decades, Vim has are plenty of useful plugins to choose from and even entire sites, like [Vim Awesome](https://vimawesome.com/), dedicated to Vim plugins.

You can [install plugins manually](https://opensource.com/article/20/2/how-install-vim-plugins) or with a Vim package manager like [Vim-plug](https://github.com/junegunn/vim-plug). If you're a casual Vim user, you can easily become dependent upon Vim through plugins. From simple color schemes to file managers, plugins are Vim's way of keeping you coming back for more.

How to install Vim plugins

As of the Vim 8.x series, however, there's a structure around how plugins are intended to be installed and loaded. You may encounter old instructions online or in project README files, but as long as you're running Vim 8 or greater, you should install according to Vim's official plugin install method or with a Vim package manager. You can use a package manager regardless of what version you run (including releases older than 8.x), which makes the install process easier than maintaining updates yourself.

## Install plugins manually (Vim 8 and above)

A Vim package is a directory containing one or more plugins. By default, your Vim settings are contained in `~/.vim`, so that's where Vim looks for plugins when you launch it.

When you start Vim, it first processes your .vimrc file, and then it scans all directories in `~/.vim` for plugins contained in `pack/*/start`.

1) By default, your `~/.vim` directory (if you even have one) has no such file structure, so set that up with:

`mkdir -p ~/.vim/pack/vendor/start`

2) place Vim plugins in ~/.vim/pack/vendor/start, and they'll automatically load when you launch Vim.

```
git clone --depth 1 https://github.com/preservim/nerdtree.git ~/.vim/pack/vendor/start/nerdtree
```

3) Launch Vim or gvim, and type this command: `:NERDTree`
   A file tree will open along the left side of your Vim window.

4) If you don't want a plugin to load automatically every time you launch Vim, you can create an opt directory within your `~/.vim/pack/vendor directory`: `mkdir ~/.vim/pack/vendor/opt`

Any plugins installed into opt are available to Vim, but they're not loaded into memory until you add them to a session with the packadd command. : `:packadd foo`

> Officially, Vim recommends that each plugin project gets its own directory within ~/.vim/pack.

## Installing plugins using Vim package manager

There are several package managers to choose from, and they're each different, but vim-plug has some great features and the best documentation of them all, which makes it easy to start with and to explore in depth later.

### Installing plugins with vim-plug

1) Install vim-plug so that it auto-loads at launch with:

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
  https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

2) Create a ~/.vimrc file (if you don't have one already), and enter this text:

```
call plug#begin()
Plug 'preservim/NERDTree'
call plug#end()
```

Each time you want to install a plugin, you must enter the name and location of the plugin between the `plug#begin()` and `plug#end` lines.  

You can even "install" local plugins outside of your ~/.vim directory.

3) Finally, start Vim and prompt vim-plug to install the plugins listed in `~/.vimrc`:

`:PlugInstall`

Wait for the plugins to be downloaded.

### Update plugins with vim-plug

To update all installed plugins, issue this Vim command:

`:PlugUpdate`

If you don't want to update all plugins, you can update any subset by adding the plugin's name:

`:PlugUpdate NERDTree`

### Restore plugins

Vim-plug has this command to generate a script for restoring all current plugins:

`:PlugSnapshot ~/vim-plug.list`

# ViM Cheatsheet

## Global

```
:h[elp] keyword - open help for keyword
:sav[eas] file - save file as
:clo[se] - close current pane
:ter[minal] - open a terminal window
K - open man page for word under the cursor
```

Tip Run vimtutor in a terminal to learn the first Vim commands.

## Cursor movement

```
h - move cursor left
j - move cursor down
k - move cursor up
l - move cursor right
gj - move cursor down (multi-line text)
gk - move cursor up (multi-line text)
H - move to top of screen
M - move to middle of screen
L - move to bottom of screen
w - jump forwards to the start of a word
W - jump forwards to the start of a word (words can contain punctuation)
e - jump forwards to the end of a word
E - jump forwards to the end of a word (words can contain punctuation)
b - jump backwards to the start of a word
B - jump backwards to the start of a word (words can contain punctuation)
ge - jump backwards to the end of a word
gE - jump backwards to the end of a word (words can contain punctuation)
% - move cursor to matching character (default supported pairs: '()', '{}', '[]' - use :h matchpairs in vim for more info)
0 - jump to the start of the line
^ - jump to the first non-blank character of the line
$ - jump to the end of the line
g_ - jump to the last non-blank character of the line
gg - go to the first line of the document
G - go to the last line of the document
5gg or 5G - go to line 5
gd - move to local declaration
gD - move to global declaration
fx - jump to next occurrence of character x
tx - jump to before next occurrence of character x
Fx - jump to the previous occurrence of character x
Tx - jump to after previous occurrence of character x
; - repeat previous f, t, F or T movement
, - repeat previous f, t, F or T movement, backwards
} - jump to next paragraph (or function/block, when editing code)
{ - jump to previous paragraph (or function/block, when editing code)
zz - center cursor on screen
zt - position cursor on top of the screen
zb - position cursor on bottom of the screen
Ctrl + e - move screen down one line (without moving cursor)
Ctrl + y - move screen up one line (without moving cursor)
Ctrl + b - move screen up one page (cursor to last line)
Ctrl + f - move screen down one page (cursor to first line)
Ctrl + d - move cursor and screen down 1/2 page
Ctrl + u - move cursor and screen up 1/2 page
```

Tip Prefix a cursor movement command with a number to repeat it. For example, 4j moves down 4 lines.

## Insert mode - inserting/appending text

```
i - insert before the cursor
I - insert at the beginning of the line
a - insert (append) after the cursor
A - insert (append) at the end of the line
o - append (open) a new line below the current line
O - append (open) a new line above the current line
ea - insert (append) at the end of the word
Ctrl + h - delete the character before the cursor during insert mode
Ctrl + w - delete word before the cursor during insert mode
Ctrl + j - add a line break at the cursor position during insert mode
Ctrl + t - indent (move right) line one shiftwidth during insert mode
Ctrl + d - de-indent (move left) line one shiftwidth during insert mode
Ctrl + n - insert (auto-complete) next match before the cursor during insert mode
Ctrl + p - insert (auto-complete) previous match before the cursor during insert mode
Ctrl + rx - insert the contents of register x
Ctrl + ox - Temporarily enter normal mode to issue one normal-mode command x.
Esc or Ctrl + c - exit insert mode
```

## Editing

```

r - replace a single character.
R - replace more than one character, until ESC is pressed.
J - join line below to the current one with one space in between
gJ - join line below to the current one without space in between
gwip - reflow paragraph
g~ - switch case up to motion
gu - change to lowercase up to motion
gU - change to uppercase up to motion
cc - change (replace) entire line
c$ or C - change (replace) to the end of the line
ciw - change (replace) entire word
cw or ce - change (replace) to the end of the word
s - delete character and substitute text (same as cl)
S - delete line and substitute text (same as cc)
xp - transpose two letters (delete and paste)
u - undo
U - restore (undo) last changed line
Ctrl + r - redo
. - repeat last command
```

## Marking text (visual mode)

```

v - start visual mode, mark lines, then do a command (like y-yank)
V - start linewise visual mode
o - move to other end of marked area
Ctrl + v - start visual block mode
O - move to other corner of block
aw - mark a word
ab - a block with ()
aB - a block with {}
at - a block with <> tags
ib - inner block with ()
iB - inner block with {}
it - inner block with <> tags
Esc or Ctrl + c - exit visual mode
```

Tip Instead of b or B one can also use ( or { respectively.

## Visual commands

```

> - shift text right
< - shift text left
y - yank (copy) marked text
d - delete marked text
~ - switch case
u - change marked text to lowercase
U - change marked text to uppercase
```

## Registers

```

:reg[isters] - show registers content
"xy - yank into register x
"xp - paste contents of register x
"+y - yank into the system clipboard register
"+p - paste from the system clipboard register
```

Tip Registers are being stored in ~/.viminfo, and will be loaded again on next restart of vim.

## Tip Special registers

```

 0 - last yank
 " - unnamed register, last delete or yank
 % - current file name
 # - alternate file name
 * - clipboard contents (X11 primary)
 + - clipboard contents (X11 clipboard)
 / - last search pattern
 : - last command-line
 . - last inserted text
 - - last small (less than a line) delete
 = - expression register
 _ - black hole register
```

## Marks and positions

```
:marks - list of marks
ma - set current position for mark A
`a - jump to position of mark A
y`a - yank text to position of mark A
`0 - go to the position where Vim was previously exited
`" - go to the position when last editing this file
`. - go to the position of the last change in this file
`` - go to the position before the last jump
:ju[mps] - list of jumps
Ctrl + i - go to newer position in jump list
Ctrl + o - go to older position in jump list
:changes - list of changes
g, - go to newer position in change list
g; - go to older position in change list
Ctrl + ] - jump to the tag under cursor
```

Tip To jump to a mark you can either use a backtick (`) or an apostrophe ('). Using an apostrophe jumps to the beginning (first non-blank) of the line holding the mark.

## Macros

```text
qa - record macro a
q - stop recording macro
@a - run macro a
@@ - rerun last run macro
```

## Cut and paste

```

yy - yank (copy) a line
2yy - yank (copy) 2 lines
yw - yank (copy) the characters of the word from the cursor position to the start of the next word
yiw - yank (copy) word under the cursor
yaw - yank (copy) word under the cursor and the space after or before it
y$ or Y - yank (copy) to end of line
p - put (paste) the clipboard after cursor
P - put (paste) before cursor
gp - put (paste) the clipboard after cursor and leave cursor after the new text
gP - put (paste) before cursor and leave cursor after the new text
dd - delete (cut) a line
2dd - delete (cut) 2 lines
dw - delete (cut) the characters of the word from the cursor position to the start of the next word
diw - delete (cut) word under the cursor
daw - delete (cut) word under the cursor and the space after or before it
:3,5d - delete lines starting from 3 to 5
```

Tip You can also use the following characters to specify the range:
e.g.

```

:.,$d - From the current line to the end of the file
:.,1d - From the current line to the beginning of the file
:10,$d - From the 10th line to the beginning of the file
```

```
:g/{pattern}/d - delete all lines containing pattern
:g!/{pattern}/d - delete all lines not containing pattern
d$ or D - delete (cut) to the end of the line
x - delete (cut) character
```

## Indent text

```

>> - indent (move right) line one shiftwidth
<< - de-indent (move left) line one shiftwidth
>% - indent a block with () or {} (cursor on brace)
<% - de-indent a block with () or {} (cursor on brace)
>ib - indent inner block with ()
>at - indent a block with <> tags
3== - re-indent 3 lines
=% - re-indent a block with () or {} (cursor on brace)
=iB - re-indent inner block with {}
gg=G - re-indent entire buffer
]p - paste and adjust indent to current line
```

## Exiting

```

:w - write (save) the file, but don't exit
:w !sudo tee % - write out the current file using sudo
:wq or :x or ZZ - write (save) and quit
:q - quit (fails if there are unsaved changes)
:q! or ZQ - quit and throw away unsaved changes
:wqa - write (save) and quit on all tabs
```

## Search and replace

```

/pattern - search for pattern
?pattern - search backward for pattern
\vpattern - 'very magic' pattern: non-alphanumeric characters are interpreted as special regex symbols (no escaping needed)
n - repeat search in same direction
N - repeat search in opposite direction
:%s/old/new/g - replace all old with new throughout file
:%s/old/new/gc - replace all old with new throughout file with confirmations
:noh[lsearch] - remove highlighting of search matches
```

## Search in multiple files

```

:vim[grep] /pattern/ {`{file}`} - search for pattern in multiple files
```

e.g. :vim[grep] /foo/ **/*

```
:cn[ext] - jump to the next match
:cp[revious] - jump to the previous match
:cope[n] - open a window containing the list of matches
:ccl[ose] - close the quickfix window
```

## Tabs

```

:tabnew or :tabnew {page.words.file} - open a file in a new tab
Ctrl + wT - move the current split window into its own tab
gt or :tabn[ext] - move to the next tab
gT or :tabp[revious] - move to the previous tab
#gt - move to tab number #
:tabm[ove] # - move current tab to the #th position (indexed from 0)
:tabc[lose] - close the current tab and all its windows
:tabo[nly] - close all tabs except for the current one
:tabdo command - run the command on all tabs (e.g. :tabdo q - closes all opened tabs)
```

## Working with multiple files

```

:e[dit] file - edit a file in a new buffer
:bn[ext] - go to the next buffer
:bp[revious] - go to the previous buffer
:bd[elete] - delete a buffer (close a file)
:b[uffer]# - go to a buffer by index #
:b[uffer] file - go to a buffer by file
:ls or :buffers - list all open buffers
:sp[lit] file - open a file in a new buffer and split window
:vs[plit] file - open a file in a new buffer and vertically split window
:vert[ical] ba[ll] - edit all buffers as vertical windows
:tab ba[ll] - edit all buffers as tabs
Ctrl + ws - split window
Ctrl + wv - split window vertically
Ctrl + ww - switch windows
Ctrl + wq - quit a window
Ctrl + wx - exchange current window with next one
Ctrl + w= - make all windows equal height & width
Ctrl + wh - move cursor to the left window (vertical split)
Ctrl + wl - move cursor to the right window (vertical split)
Ctrl + wj - move cursor to the window below (horizontal split)
Ctrl + wk - move cursor to the window above (horizontal split)
Ctrl + wH - make current window full height at far left (leftmost vertical window)
Ctrl + wL - make current window full height at far right (rightmost vertical window)
Ctrl + wJ - make current window full width at the very bottom (bottommost horizontal window)
Ctrl + wK - make current window full width at the very top (topmost horizontal window)
```

## Diff

```
zf - manually define a fold up to motion
zd - delete fold under the cursor
za - toggle fold under the cursor
zo - open fold under the cursor
zc - close fold under the cursor
zr - reduce (open) all folds by one level
zm - fold more (close) all folds by one level
zi - toggle folding functionality
]c - jump to start of next change
[c - jump to start of previous change
do or :diffg[et] - obtain (get) difference (from other buffer)
dp or :diffpu[t] - put difference (to other buffer)
:diffthis - make current window part of diff
:dif[fupdate] - update differences
:diffo[ff] - switch off diff mode for current window
```

Tip The commands for folding (e.g. za) operate on one level. To operate on all levels, use uppercase letters (e.g. zA).
Tip To view the differences of files, one can directly start Vim in diff mode by running vimdiff in a terminal. One can even set this as git difftool.

# defaults.vim

If you haven't created a vimrc file, the defaults.vim file that comes with Vim installation will be used. This file aims to provide saner defaults like enabling syntax highlighting, filetype settings and so on.

- `source $VIMRUNTIME/defaults.vim` add this to your vimrc file if you want to keep these defaults
- `:h defaults.vim-explained` describes the settings provided by defaults.vim

Alternatively, you can copy only the parts you want to retain from the `defaults.vim` file to your `vimrc` file.

# Retaining the format as original source while paste in Vim

Type `:set paste`

Then paste your code. Note that the text in the tooltip now says `-- INSERT (paste) --`.

After you pasted your code, turn off the paste-mode, so that auto-indenting when you type works correctly again.

`:set nopaste`

You can also switch between paste and nopaste modes while editing the text by adding this to `.vimrc`

`set pastetoggle=<F2>`

To paste from another application and retain the existing indentation of the pasted text

- Start insert mode.
- Press F2 (toggles the 'paste' option on).
- Use your terminal to paste text from the clipboard.
- Press F2 (toggles the 'paste' option off).

If the autoindent is still messing with you.

```
set autoindent
set smartindent
```

# Vimrc Configuration - Vim Code Editor Customisation

The conventional wisdom for vimrc is to add a .vimrc dotfile in the root directory ~/.vimrc (it might be different depending on your OS).

Behind the scene, Vim looks at multiple places for a vimrc file. Here are the places that Vim checks:

- `$VIMINIT`
- `$HOME/.vimrc`
- `$HOME/.vim/vimrc`
- `$EXINIT`
- `$HOME/.exrc`
- `$VIMRUNTIME/default.vim`

When you start Vim, it will check the above six locations in that order for a vimrc file. The first found vimrc file will be used and the rest is ignored.

In the nutshell, a vimrc is a collection of:

- Plugins
- Settings
- Custom Functions
- Custom Commands
- Mappings

There are other things not mentioned above, but in general, this covers most use cases.

Over time, your vimrc will grow large and become convoluted. There are two ways to keep your vimrc to look clean:

- Split your vimrc into several files.
- Fold your vimrc file.

__Splitting Your Vimrc__

You can split your vimrc to multiple files using Vim's source command. This command reads command-line commands from the given file argument. For example inside `~/.vimrc`

```
source $HOME/.vim/settings/plugins.vim
source $HOME/.vim/settings/configs.vim
source $HOME/.vim/settings/functions.vim
source $HOME/.vim/settings/mappings.vim
```

__Keeping One Vimrc File__

If you prefer to keep one vimrc file to keep it portable, you can use the marker folds to keep it organized. A marker fold uses `{{{` and `}}}` to indicate the starting and ending folds (don't forget to comment them with `"`).Add this at the top of your vimrc:

Refer example [vim.rc example](#vimrc-example)

Now, once you move your cursor on a fold you can press:

- `zo` to open a single fold under the cursor.
- `zc` to close the fold under the cursor.
- `zR` to open all folds.
- `zM` to close all folds.

Type `:help folding` for more information.

__Running Vim With Or Without Vimrc And Plugins__

| Command | Description |
| :------ | :---------- |
`vim -u NONE`     | run Vim without both vimrc and plugins
`vim -u NORC`     | launch Vim without vimrc but with plugins
`vim --noplugin`  | run Vim with vimrc but without plugins
`vim -u ~/.vimrc-backup` | run Vim with a different vimrc, say `~/.vimrc-backup`

# Map Keyboard Shortcuts in Vim

Key mapping syntax is like this:

`map_mode <what_you_type> <what_is_executed>`

few popular mapping modes and probably the most useful and important.

- `nnoremap` – Allows you to map keys in normal mode.
- `inoremap` – Allows you to map keys in insert mode.
- `vnoremap` – Allows you to map keys in visual mode

A common mapping example is to map 'jj' to the escape key. You will be pressing the escape key a lot. The escape key is in the far corner of the keyboard. The letter 'j' is in the middle of the keyboard so it is easier to press 'jj' instead of reaching for the escape key.

This is how you would map the escape key to jj.

`inoremap jj <esc>`


# Configuring Color Schemes

Installing a color scheme is a simple as adding a `<colorscheme>.vim` file to the `~/.vim/colors/` directory. To set the color scheme, type this command: `:colorscheme molokai`


where
- `%F` – Display the full path of the current file.
- `%M `– Modified flag shows if file is unsaved.
- `%Y `– Type of file in the buffer.
- `%R` – Displays the read-only flag.
- `%b` – Shows the ASCII/Unicode character under cursor.
- `0x%B` – Shows the hexadecimal character under cursor.
- `%l` – Display the row number.
- `%c` – Display the column number.
- `%p%%` – Show the cursor percentage from the top of the file.

# vim.rc example

some basic settings that will improve your editing experience

```
" Disable compatibility with vi which can cause unexpected issues.
set nocompatible

" Enable type file detection. Vim will be able to try to detect the type of file is use.
filetype on

" Enable plugins and load plugin for the detected file type.
filetype plugin on

" Load an indent file for the detected file type.
filetype indent on

" Turn syntax highlighting on.
syntax on

" Add numbers to the file.
set number

" Highlight cursor line underneath the cursor horizontally.
set cursorline

" Highlight cursor line underneath the cursor vertically.
set cursorcolumn

" Set shift width to 4 spaces.
set shiftwidth=4

" Set tab width to 4 columns.
set tabstop=4

" Use space characters instead of tabs.
set expandtab

" Do not save backup files.
set nobackup

" Do not let cursor scroll below or above N number of lines when scrolling.
set scrolloff=10

" Do not wrap lines. Allow long lines to extend as far as the line goes.
" set nowrap

" While searching though a file incrementally highlight matching characters as you type.
set incsearch

" Ignore capital letters during search.
set ignorecase

" Override the ignorecase option if searching for capital letters.
" This will allow you to search specifically for capital letters.
set smartcase

" Show partial command you type in the last line of the screen.
set showcmd

" Show the mode you are on the last line.
set showmode

" Show matching words during a search.
set showmatch

" Use highlighting when doing a search.
set hlsearch

" Set the commands to save in history default number is 20.
set history=1000

" Enable auto completion menu after pressing TAB.
set wildmenu

" Make wildmenu behave like similar to Bash completion.
set wildmode=list:longest

" There are certain files that we would never want to edit with Vim.
" Wildmenu will ignore files with these extensions.
set wildignore=*.docx,*.jpg,*.png,*.gif,*.pdf,*.pyc,*.exe,*.flv,*.img,*.xlsx

" PLUGINS ---------------------------------------------------------------- {{{

call plug#begin('~/.vim/plugged')

  Plug 'dense-analysis/ale'

  Plug 'preservim/nerdtree'

call plug#end()

" }}}

" MAPPINGS --------------------------------------------------------------- {{{

" Set the backslash as the leader key.
let mapleader = "\"

" Press \\ to jump back to the last cursor position.
nnoremap <leader>\ ``

" Press \p to print the current file to the default printer from a Linux operating system.
" View available printers:   lpstat -v
" Set default printer:       lpoptions -d <printer_name>
" <silent> means do not display output.
nnoremap <silent> <leader>p :%w !lp<CR>

" Type jj to exit insert mode quickly.
inoremap jj <Esc>

" Press the space bar to type the : character in command mode.
nnoremap <space> :

" Pressing the letter o will open a new line below the current one.
" Exit insert mode after creating a new line above or below the current line.
nnoremap o o<esc>
nnoremap O O<esc>

" Center the cursor vertically when moving to the next word during a search.
nnoremap n nzz
nnoremap N Nzz

" Yank from cursor to the end of line.
nnoremap Y y$

" Map the F5 key to run a Python script inside Vim.
" We map F5 to a chain of commands here.
" :w saves the file.
" <CR> (carriage return) is like pressing the enter key.
" !clear runs the external clear screen command.
" !python3 % executes the current file with Python.
nnoremap <f5> :w <CR>:!clear <CR>:!python3 % <CR>

" You can split the window in Vim by typing :split or :vsplit.
" Navigate the split view easier by pressing CTRL+j, CTRL+k, CTRL+h, or CTRL+l.
nnoremap <c-j> <c-w>j
nnoremap <c-k> <c-w>k
nnoremap <c-h> <c-w>h
nnoremap <c-l> <c-w>l

" Resize split windows using arrow keys by pressing:
" CTRL+UP, CTRL+DOWN, CTRL+LEFT, or CTRL+RIGHT.
noremap <c-up> <c-w>+
noremap <c-down> <c-w>-
noremap <c-left> <c-w>>
noremap <c-right> <c-w><

" NERDTree specific mappings.
" Map the F3 key to toggle NERDTree open and close.
nnoremap <F3> :NERDTreeToggle<cr>

" Have nerdtree ignore certain files and directories.
let NERDTreeIgnore=['\.git$', '\.jpg$', '\.mp4$', '\.ogg$', '\.iso$', '\.pdf$', '\.pyc$', '\.odt$', '\.png$', '\.gif$', '\.db$']

" }}}

" VIMSCRIPT -------------------------------------------------------------- {{{

" Enable the marker method of folding.
augroup filetype_vim
    autocmd!
    autocmd FileType vim setlocal foldmethod=marker
augroup END

" If the current file type is HTML, set indentation to 2 spaces.
autocmd Filetype html setlocal tabstop=2 shiftwidth=2 expandtab

" If Vim version is equal to or greater than 7.3 enable undofile.
" This allows you to undo changes to a file even after saving it.
if version >= 703
    set undodir=~/.vim/backup
    set undofile
    set undoreload=10000
endif

" You can split a window into sections by typing `:split` or `:vsplit`.
" Display cursorline and cursorcolumn ONLY in active window.
augroup cursor_off
    autocmd!
    autocmd WinLeave * set nocursorline nocursorcolumn
    autocmd WinEnter * set cursorline cursorcolumn
augroup END

" If GUI version of Vim is running set these options.
if has('gui_running')

    " Set the background tone.
    set background=dark

    " Set the color scheme.
    colorscheme molokai

    " Set a custom font you have installed on your computer.
    " Syntax: <font_name>\ <weight>\ <size>
    set guifont=Monospace\ Regular\ 12

    " Display more of the file by default.
    " Hide the toolbar.
    set guioptions-=T

    " Hide the the left-side scroll bar.
    set guioptions-=L

    " Hide the the left-side scroll bar.
    set guioptions-=r

    " Hide the the menu bar.
    set guioptions-=m

    " Hide the the bottom scroll bar.
    set guioptions-=b

    " Map the F4 key to toggle the menu, toolbar, and scroll bar.
    " <Bar> is the pipe character.
    " <CR> is the enter key.
    nnoremap <F4> :if &guioptions=~#'mTr'<Bar>
        \set guioptions-=mTr<Bar>
        \else<Bar>
        \set guioptions+=mTr<Bar>
        \endif<CR>

endif

" }}}

" STATUS LINE ------------------------------------------------------------ {{{

" Clear status line when vimrc is reloaded.
set statusline=

" Status line left side.
set statusline+=\ %F\ %M\ %Y\ %R

" Use a divider to separate the left side from the right side.
set statusline+=%=

" Status line right side.
"set statusline+=\ ascii:\ %b\ hex:\ 0x%B\ row:\ %l\ col:\ %c\ percent:\ %p%%

" Show the status on the second to last line.
set laststatus=2

" }}}
```