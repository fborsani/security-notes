# John the Ripper

## Wordlist rules

### Reject rule

```
-:	no-op: don't reject
-c	reject this rule unless current hash type is case-sensitive
-8	reject this rule unless current hash type uses 8-bit characters
-s	reject this rule unless some password hashes were split at loading
-p	reject this rule unless word pair commands are currently allowed
```

### Reject word

```
!X     reject the word if it contains character X
!?C    reject the word if it contains a character in class C
/X     reject the word unless it contains character X
/?C    reject the word unless it contains a character in class C
=NX    reject the word unless character in position N is equal to X
=N?C   reject the word unless character in position N is in class C
(X     reject the word unless its first character is X
(?C    reject the word unless its first character is in class C
)X     reject the word unless its last character is X
)?C    reject the word unless its last character is in class C
%NX    reject the word unless it contains at least N instances of X
%N?C	 reject the word unless it contains at least N characters of class C
```

### String constants

```
0...9	for 0...9
A...Z	for 10...35
*	for max_length
-	for (max_length - 1)
+	for (max_length + 1)
a...k	user-defined numeric variables (with the "v" command)
l	initial or updated word's length (updated whenever "v" is used)
m	initial or memorized word's last character position
p	position of the character last found with the "/" or "%" commands
z	"infinite" position or length (beyond end of word)
```

### Char classes

```
??	matches "?"
?v	matches vowels: "aeiouAEIOU"
?c	matches consonants: "bcdfghjklmnpqrstvwxyzBCDFGHJKLMNPQRSTVWXYZ"
?w	matches whitespace: space and horizontal tabulation characters
?p	matches punctuation: ".,:;'?!`" and the double quote character
?s	matches symbols "$%^&*()-_+=|\<>[]{}#@/~"
?l	matches lowercase letters [a-z]
?u	matches uppercase letters [A-Z]
?d	matches digits [0-9]
?a	matches letters [a-zA-Z]
?x	matches letters and digits [a-zA-Z0-9]
?z	matches all characters
```

### Whole word commands

```
:        do nothing to the input word
l        convert to lowercase
u        convert to uppercase
c        capitalize
C        lowercase the first character, and uppercase the rest
t        toggle case of all characters in the word
TN       toggle case of the character in position N
r        reverse: "Fred" -> "derF"
d        duplicate: "Fred" -> "FredFred"
f        reflect: "Fred" -> "FredderF"
{        rotate the word left: "jsmith" -> "smithj"
}        rotate the word right: "smithj" -> "jsmith"
$X       append character X to the word
^X       prefix the word with character X
S        shift case: "Crack96" -> "cRACK(^"
V        lowercase vowels, uppercase consonants: "Crack96" -> "CRaCK96"
R        shift each character right, by keyboard: "Crack96" -> "Vtsvl07"
L        shift each character left, by keyboard: "Crack96" -> "Xeaxj85"
sXY      replace all characters X in the word with Y
s?CY	   replace all characters of class C in the word with Y
@X	     purge all characters X from the word
@?C	     purge all characters of class C from the word
```

### Length control

```
<N	reject the word unless it is less than N characters long
>N	reject the word unless it is greater than N characters long
'N	truncate the word at length N
```

### English grammar

```
p	pluralize: "crack" -> "cracks", etc. (lowercase only)
P	"crack" -> "cracked", etc. (lowercase only)
I	"crack" -> "cracking", etc. (lowercase only)
```

### Insert/Delete

```
[    delete the first character
]    delete the last character
DN   delete the character in position N
xNM	 extract substring from position N for up to M characters
iNX	 insert character X in position N and shift the rest right
oNX	 overstrike character in position N with character X
```



