1. Find the number of words (in `/usr/share/dict/words`) that contain at least three `a`s and don’t have a `'s` ending. What are the three most common last two letters of those words? `sed`’s `y` command, or the `tr` program, may help you with case insensitivity. How many of those two-letter combinations are there? And for a challenge: which combinations do not occur?
 
 Answer:
 
 Find the number of words that contain at least three `a`s and don’t have a `'s` ending. 
 
 Explain: `\w` and `$` exclude `'s`, another possible way is to use negative lookahead (?!...)
 ```
 grep -iE "(\w*a\w*){3}$" words | wc -l
 ```

 Get the last two letters of the words
 
 Explain: The replacement string `\1` replaces the entire line with the captured group `.*(..)$`, which contains the last 2 characters.
 ```
 grep -iE "(\w*a\w*){3}$" words | sed -E "s/.*(..)$/\1/"
 ```
 
 Find the three most common:
 
 Explain: `uniq -c` will collapse consecutive lines that are the same into a single line, prefixed with a count of the number of occurrences. `sort -n` will sort in numeric (instead of lexicographic) order. `-k1,1` means “sort by only the first whitespace-separated column”. There’s also `sort -r`, which sorts in reverse order.
 ```
 grep -iE "(\w*a\w*){3}$" words | sed -E "s/.*(..)$/\1/" | sort | uniq -c | sort -nk1,1 | tail -n3
 ```

 How many of those two-letter combinations are there?
 ```
 grep -iE "(\w*a\w*){3}$" words | sed -E "s/.*(..)$/\1/" | sort | uniq -c | wc -l
 ```
 
 Which combinations do not occur? Ans: Write a script to generate all the two-letter combinations, then do a `diff` with the previous result
