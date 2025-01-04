# jetbrains ides

## file/live templates
haven't used much, but useful for CP starter code.
Another example: \begin{x} \end{x} in latex.

## refactoring
shift + f6 = rename current symbol

command + f6 = change signature

f6 = move

other cool things:
- extract method from selection

## code analysis
structural replace (advanced) - much more structure find and replace that allows you to use code semantics to search as oppose to raw regex

## search

shift shift or (custom) command o - search everywhere

open regular search and click on `.*` - search by regex

option + f7 - find usages

## actions
command shift a - show all possible actions

some useful actions:
1. optimize imports


## editor
command . = fold 

(custom) command shift w - close all tabs except current one

command w - close current tab

(custom) option m - maximize current window

generally use vim navigation for switching windows

### navigation

(custom) option h = left

(custom) option j = down

(custom) option k = down

(custom) option l = right


command up - show navigation bar


command e - recent files

command shift e - recent locations

option f1 - select in [finder, project window, etc]

command f3 - look at bookmarks (and easily go to them0

(custom) m{lower_case} - toggle {lower_case} bookmark


command click = go to declaration (or similar)

command b - go to declaration or usages

command option b - go to implementations

command shift b - go to type declaration

command u - go to super method


## windows
(custom) option s = split right

(custom) option shift s = split down

(custom) command 0 - build

command 1 - project 

command 2 - bookmarks

command 3 - find

command 4 - run

command 5 - debug

command 6 - problems

command 7 - file structure

(custom) command f8 = terminal 

command 9 - git


(custom) control shift f - toggle distraction free mode (custom)

## run/debugging
command + f2 = stop

(custom) command r - run

control option r - run arbitrary configuration 

(custom) option shift d - debug

control option d - debug arbitrary configuration

command + f8 = breakpoint
- note that in breakpoint settings, we can enable conditions, one shot, or even if it should suspend

command shift f8 = view all breakpoints

command option r - resume program

f8 = step over

f7 = step into

shift f8 = step out

option f9 = run to cursor

option f8 = evaluate expression. Use command shift enter to add to watch

## idea vim
many vim commands supported

note that when creating map commands, we can use the `action` command to invoke an intellij action
```
nnoremap ma ma:action ToggleBookmark A<CR>
```

## git
can see git blame for any line via clicking on the enigmurl*

command t = pull

command k = commit
- we can also optimize imports and reformat code upon commit

command shift K = push

we can perform diffs, rebase, merge, revert commits, squash, and many more from here

## etc

(custom) option z - show context menu

option enter - show context actions (e.g. show type explicitly)

command d when selecting two or more files - show their difference

select + option z + history = local history for selection

command shift v - command paste from history

