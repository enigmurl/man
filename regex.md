# regex

## character classes/special characters

`\d` digit
`\D` not a digit
`\w` word character, ascii letter or digit or underscore (variable name basically)
`\W` not a word
`\s` whitespace (including newline)
`\S` not whitespace
`\b` word boundary, exactly one side is letter or digit or underscore (e.g. in `middle    earth`, four word boundaries)
`\B` not a word boundary
`.` matches any character (except line break?)

`[x-y]` matches all characters in ascii range x to y, inclusive
`[abcd]` matches a or b or c or d
`[^abcd]` matches anything except a or b or c or d. 
note: use `\^` or `\-` to escape

`^` start of string (or possibly line)
`$` end of string (or possibly line)

`?` matches previous pattern zero or one times

## quantifiers
`+` matches previous pattern one or more times
`*` matches previous pattern zero or more times
`{a}` matches previous pattern up to a times, inclusive
`{a, b}` matches previous pattern a to b times, inclusive

note that by default, this is greedy. `?` after `+` or `*` or `{a, b}` makes a lazy match
e.g. `\w{1, 2}` on `xy` matches `xy` but `\w{1, 2}?` on `xy` matches just `x`

## logic
`|` matches the pattern to the left or right. Note that it has low precedence so xy|wz is (xy)|(wz)
`(...)` matches a pattern and allows that pattern to be references as `\1` or `\2`... useful for replace
`(?<name> ...)` named captured group. syntax varies for how to refer to it during replace.
`(?: ...)` matches the pattern and does not capture it

## look ahead
`(?= ...)` positive lookahead. example: for `(?=\d{10})\d{5}`, `01234` is the match in `0123456789`, but `01234` does not match the pattern. Essentially, verify something exists, but not consume it
`(?! ...)` negative lookahead
`(?<= ...)` positive lookbehind
`(?<! ...)` negative lookbehind

`(?> ..)` atomic group. relatively niche, but essentially prohibits back tracking and can improve efficiency (in essence once you find a match for this, never backtrack)

## inline modifiers
not supported by all engines (namely js). in essence, allows for various settings
e.g. `(?i)` turns on case insensitivity

## references
[rexegg](https://www.rexegg.com/regex-quickstart.php)
