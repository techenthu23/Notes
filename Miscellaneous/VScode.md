# VScode Tips and Customisation

# Search and Replace

press `Ctrl + H`on Windows and Linux, or `⌥⌘F` on Mac to enable the search and replace tool:
if you want to use this regex instead, you may enable it in the icon: `.*` and use the regex:

| Search            | Replace | Description                                                                                              |
| :---------------- | :------ | -------------------------------------------------------------------------------------------------------- |
| `^$\n`            | Blank   | Remove blank lines from code                                                                             |
| `^(\s)*$\n`       | Blank   | Remove blank lines from code                                                                             |
| `<h1>(.+?)<\/h1>` | `# $1`  | Search HTML H1 header and replace it with                                                                |
| `a.o`             |         | `a.o` matches "aro" in "around" and "abo" in "about" but not "acro" in "across"                          |
| `a*r``            |         | `a*r` matches "r" in "rack", "ar" in "ark", and "aar" in "aardvark"                                      |
| ` c.*e `          |         | `c.*e` matches "cke" in "racket", "comme" in "comment", and "code" in "code"                             |
| `e+d`             |         | `e+d` matches "eed" in "feeder" and "ed" in "faded"                                                      |
| `e.+e`            |         | `e.+e` matches "eede" in "feeder" but finds no matches in "feed". Match any character one or more times. |
| `car\r?$`         |         | `car\r?$` matches "car" only when it appears at the end of a line                                        |
| `real(?!ity)`     |         | Invalidate a match. `real(?!ity)` matches "real" in "realty" and "really" but not in "reality." It also finds the second "real" (but not the first "real") in "realityreal".
|`^\s{0,}(.*):`       | `- __$1__:` | Converts pattern with containing a colon in a punctuation to Markdown bold
