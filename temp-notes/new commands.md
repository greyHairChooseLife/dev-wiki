# quickfix list buffer

,,q: toggle qf list
dd: remove at cursor
DD: qf list clear
C-p,n: navigate

when telescope on, 

tab: select or unselect
C-q: take them all into qf list
A-q: selected ones into qf list

# Git-log & Commit-history

<leader>glg: GV --graph
<leader>glf: GV! (file only)

<leader>rer: commit history from range (manually type) 
<leader>rel: commit history --range=origin/main...HEAD (which is Local change)
<leader>rea: commit history (All but not so all actually)
<leader>ref: commit history current file

# code action by Coc

X: ,.arn: rename
X: ,.av: select
N: ,.ac: cursor
N: ,.as: total-source
X: ,.are: refactor

N,X: ,s: smart-select

# toggleTerm

C-\: terminal, it has individual instance
