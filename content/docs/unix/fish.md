## Command line editor

### Shared bindings

* **Tab** [completes](https://fishshell.com/docs/current/index.html#completion) the current token. _Shift_- **Tab** completes the current token and starts the pager's search mode.
* **↑** and **↓** search the command history for the previous/next command containing the string that was specified on the commandline before the search was started. If the commandline was empty when the search started, all commands match.
* **←** and **→** move the cursor left or right by one character. If the cursor is already at the end of the line, and an autosuggestion is available, the **→** key and the _Control_-**F** combination accept the suggestion.
* _Alt_-**←** and _Alt_-**→** move the cursor one word left or right, or moves forward/backward in the directory history if the command line is empty. If the cursor is already at the end of the line, and an autosuggestion is available, _Alt_-**→** accepts the first word in the suggestion.
* _Alt_-**↑** and _Alt_-**↓** search the command history for the previous/next token containing the token under the cursor before the search was started. If the commandline was not on a token when the search started, all tokens match.
* _Alt_-**d** moves the next word to the killring.
* _Alt_-**Backspace** moves the previous word to the killring.
* _Alt_-**h** (or **F1**) shows the manual page for the current command, if one exists.
* _Alt_-**l** lists the contents of the current directory, unless the cursor is over a directory argument, in which case the contents of that directory will be listed.
* _Alt_-**e** edit the current command line in an external editor. The editor is chosen from the first available of the `$VISUAL` or `$EDITOR` variables.

### Emacs mode commands

* _Control_-**A** moves the cursor to the beginning of the line.
* _Control_-**E** moves to the end of line. If the cursor is already at the end of the line, and an autosuggestion is available, **End** or _Control_-**E** accepts the autosuggestion.
