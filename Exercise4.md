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
 
 Which combinations do not occur? Ans: Write a script to generate all the two-letter combinations, then do a `diff` with the previous result.
 
2. To do in-place substitution it is quite tempting to do something like `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt`. However this is a bad idea, why? Is this particular to `sed`? Use `man sed` to find out how to accomplish this.

 Answer:
 
 This command will return a blank file. the shell will first truncate the input file to zero bytes before running sed, and then write the output of sed to the input file. This means that any data that was originally in the input file will be lost, and replaced with the output of sed. It is generally a bad idea to in-place edit files as mistakes can be made when using regular expressions and without backups on the original file, it can be very difficult to recover. This is not particular to `sed` but in general to all forms of in-place editing files. To accomplish in-plface substitution add the `-i` tag.
