| POSIX      | Perl/Tcl | Vim   | ASCII                              | Description                                |
|------------|----------|-------|------------------------------------|--------------------------------------------|
|            |          |       | [\x00-\x7F]                        | ASCII characters                           |
| [:alnum:]  |          |       | [A-Za-z0-9]                        | Alphanumeric characters                    |
|            | \w       | \w    | [A-Za-z0-9_]                       | Alphanumeric characters plus "_"           |
|            | \W       | \W    | [^A-Za-z0-9_]                      | Non-word characters                        |
| [:alpha:]  |          | \a    | [A-Za-z]                           | Alphabetic characters                      |
| [:blank:]  |          | \s    | [ \t]                              | Space and tab                              |
|            | \b       | \< \> | (?<=\W)(?=\w)∣(?<=\w)(?=\W)        | Word boundaries                            |
|            |          |       | (?<=\W)(?=\W)∣(?<=\w)(?=\w)        | Non-word boundaries                        |
| [:cntrl:]  |          |       | [\x00-\x1F\x7F]                    | Control characters                         |
| [:digit:]  | \d       | \d    | [0-9]                              | Digits                                     |
|            | \D       | \D    | [^0-9]                             | Non-digits                                 |
| [:graph:]  |          |       | [\x21-\x7E]                        | Visible characters                         |
| [:lower:]  |          | \l    | [a-z]                              | Lowercase letters                          |
| [:print:]  |          | \p    | [\x20-\x7E]                        | Visible characters and the space character |
| [:punct:]  |          |       | [][!"#$%&'()*+,./:;<=>?@\^_`{∣}~-] | Punctuation characters                     |
| [:space:]  | \s       | \_s   | [ \t\r\n\v\f]                      | Whitespace characters                      |
|            | \S       | \S    | [^ \t\r\n\v\f]                     | Non-whitespace characters                  |
| [:upper:]  |          | \u    | [A-Z]                              | Uppercase letters                          |
| [:xdigit:] |          | \x    | [A-Fa-f0-9]                        | Hexadecimal digits                         |

## GNU Regular Expressions
The **Basic Regular Expressions** or BRE flavor is pretty much the oldest regular expression flavor still in use today. The GNU utilities `grep`, `ed` and `sed` use it. One thing that sets this flavor apart is that most metacharacters require a backslash to give the metacharacter its flavor.

The **Extended Regular Expressions** or ERE flavor is used by the GNU utilities `egrep` and `awk` and the `emacs` editor. All metacharacters have their meaning without backslashes, just like in modern regex flavors. You can use backslashes to suppress the meaning of all metacharacters. Escaping a character that is not a metacharacter is an error. The Extended Regular Expressions can sometimes be used with Unix utilities by including the command line flag `-E`.

- https://en.wikipedia.org/wiki/Regular_expression
- https://www.regular-expressions.info/gnu.html
