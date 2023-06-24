---
layout: post
category: linux
title: Vi cheat sheet
---

## VI Editing commands

-   `i` – Insert at cursor (goes into insert mode)
-   `a` – Write after cursor (goes into insert mode)
-   `A` – Write at the end of line (goes into insert mode)
-   `ESC` – Terminate insert mode
-   `u` – Undo last change
-   `U` – Undo all changes to the entire line
-   `o` – Open a new line (goes into insert mode)
-   `dd` – Delete line
-   `3dd` – Delete 3 lines.
-   `:<start line>,<end line>d` - Delete line from start to end
-   `D` – Delete contents of line after the cursor
-   `C` – Delete contents of a line after the cursor and insert new text. Press ESC key to end insertion.
-   `dw` – Delete word
-   `4dw` – Delete 4 words
-   `cw` – Change word
-   `x` – Delete character at the cursor
-   `r` – Replace character
-   `R` – Overwrite characters from cursor onward
-   `s` – Substitute one character under cursor continue to insert
-   `S` – Substitute entire line and begin to insert at the beginning of the line
-   `~` – Change case of individual character

**Note**: You should be in the “**command mode” to execute these commands**. VI editor is **case-sensitive** so make sure you type the commands in the right letter-case.

Make sure you press the right command otherwise you will end up making undesirable changes to the file. You can also enter the insert mode by pressing a, A, o, as required.

## Moving within a file

-   `k` – Move cursor up
-   `j` – Move cursor down
-   `h` – Move cursor left
-   `l` – Move cursor right
-   `0` - Move to beginning of the current line
-   `$` - Move to end of line
-   `H` - Move to the top of the current window (high)
-   `M` - Move to the middle of the current window (middle)
-   `L` - Move to the bottom line of the current window (low)
-   `1G` - Move to the first line of the file
-   `20G` - Move to the 20th line of the file
-   `G` - Move to the last line of the file

You need to be in the command mode to move within a file. The default keys for navigation are mentioned below else; You can **also use the arrow keys on the keyboard**.

## Saving and Closing the file

-   `Shift+zz` – Save the file and quit
-   `:w` – Save the file but keep it open
-   `:q!` – Quit vi and do not save changes
-   `:wq` – Save the file and quit
-   `:x` - Save the file and quit

# Copy-Cut-Paste (Yanking) [#](https://linuxize.com/post/how-to-copy-cut-paste-in-vim//#copying-yanking)

-   `yy` - Yank (copy) the current line, including the newline character.
-   `3yy` - Yank (copy) three lines, starting from the line where the cursor is positioned.
-   `y$` - Yank (copy) everything from the cursor to the end of the line.
-   `y^` - Yank (copy) everything from the cursor to the start of the line.
-   `yw` - Yank (copy) to the start of the next word.
-   `yiw` – Yank (copy) the current word.
-   `y%` - Yank (copy) to the matching character. By default supported pairs are `()`, `{}`, and `[]`. Useful to copy text between matching brackets.

-   `dd` - Delete (cut) the current line, including the newline character.
-   `3dd` - Delete (cut) three lines, starting from the line where the cursor is positioned,
-   `d$` - Delete (cut) everything from the cursor to the end of the line.

-   `p` - To put the yanked or deleted text, move the cursor to the desired location and press `p` to put (paste) the text after the cursor or `P` to put (paste) before the cursor.
