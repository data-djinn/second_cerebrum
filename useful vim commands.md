[[bash]]

```vim
gg  # move to first line of file
G  # move to last line
gg=G  # reindent the whole file
gv  # reselect the last visual selection
`<  # jump to beginning of last visual selection
`>  # jump to end of last visual selection
^  # move to first non-blank character of the line
g_  # move the last non-blank character of the line
g_lD  # delete all the trailing whitespace on the line
ea  # append to the end of the current word
gf  # jump to the file name under the cursor
xp  # swap character forward
Xp  # swap character bakcward
yyp  # duplicate the current line
yapP  # duplicate the current paragraph
dat  # delete around an HTML tag, including the tag
dit  # delete inside an HTML tag, excluding the tag
w  # move one word to the right
b  # move one word to the let
dd  # delete the current line
zc  # close current fold
zo  # open the current fold
za  # toggle current fold
zi  # toggle folding entirely
<<  # outdent current line
>>  # indent current line
z=  # show spelling corrections
zg  # add to spelling dictionary
~  # toggle case of current character
gUw  # Uppercase until end of word (u for lower, ~ to toggle)
gUiW  # Uppercase entire word (u for lower, ~ to toggle)
gUU  # uppercase entire line
gu$  # lowercase until the end of the line
da"  # delete the next double-quoted string
+  # Move to the first non-whitespace character of the next line
S  # Delete current line and go into insert mode
I  # insert at the beginning of the line
ci"  # change what's inside the next double-quoted string
ca{  # change inside the curly braces (try [, ( too!)
vaw  # visually select word
dap  # delete the whole paragraph
r  # replace a character
`[  # Jump to beginning of last yanked text
`]  # jump to end of last yanked text
g;  # jump to the last change you made
g,  # jump back forward through the change list
&  # repeat last substitution on current line
g&  # repeat last substitution on all lines
ZZ  # Save current file & close it
```