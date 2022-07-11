# Fuzzying and encoding

## Encoding

### URL Encoding

Reserved Characters

|       | URL Encoded |                              |
| ----- | ----------- | ---------------------------- |
| #     | %23         | Anchor point                 |
| ?     | %3F         | Arguments start point        |
| &     | %24         | Argument separator           |
| %     | %25         | Character encoding prefix    |
| /     | %2F         | URL separator                |
| +     | %2B         | Alternative symbol for space |
| SPACE | %20 OR +    |                              |

Common payload characters

| Symbol                                   | Encoding |
| ---------------------------------------- | -------- |
| "                                        |          |
| '                                        |          |
| /                                        |          |
| \\                                       |          |
| ;                                        |          |
| :                                        |          |
| ,                                        |          |
| .                                        |          |
| (                                        |          |
| )                                        |          |
| \[                                       |          |
| ]                                        |          |
| {                                        |          |
| }                                        |          |
| <                                        |          |
| >                                        |          |
| \*                                       |          |
| +                                        | %2B      |
| -                                        |          |
| \_                                       |          |
| &                                        | %24      |
| \|                                       |          |
| $                                        |          |
| #                                        | %23      |
| \0 (NULL)                                |          |
| \b (backspace)                           |          |
| \n (line feed)                           |          |
| \t (horizontal tab)                      |          |
| \v (vertical tab)                        |          |
| \f (form feed)                           |          |
| \r (carriage return often used as \n \r) |          |
| \e (escape)                              |          |
| SPACE                                    | %20 OR + |

### HTML Encoding



| Symbol                                   | Named Encoding | Numeric DEC | Numeric HEX |
| ---------------------------------------- | -------------- | ----------- | ----------- |
| "                                        |                |             |             |
| '                                        |                |             |             |
| /                                        |                |             |             |
| \\                                       |                |             |             |
| ;                                        |                |             |             |
| :                                        |                |             |             |
| ,                                        |                |             |             |
| .                                        |                |             |             |
| (                                        |                |             |             |
| )                                        |                |             |             |
| \[                                       |                |             |             |
| ]                                        |                |             |             |
| {                                        |                |             |             |
| }                                        |                |             |             |
| <                                        |                |             |             |
| >                                        |                |             |             |
| \*                                       |                |             |             |
| +                                        |                |             |             |
| -                                        |                |             |             |
| \_                                       |                |             |             |
| &                                        |                |             |             |
| \|                                       |                |             |             |
| $                                        |                |             |             |
| #                                        |                |             |             |
| \0 (NULL)                                |                |             |             |
| \b (backspace)                           |                |             |             |
| \n (line feed)                           |                |             |             |
| \t (horizontal tab)                      |                |             |             |
| \v (vertical tab)                        |                |             |             |
| \f (form feed)                           |                |             |             |
| \r (carriage return often used as \n \r) |                |             |             |
| \e (escape)                              |                |             |             |
| SPACE                                    |                |             |             |

### Base Encoding



| Symbol                                   | Base36 |
| ---------------------------------------- | ------ |
| "                                        |        |
| '                                        |        |
| /                                        |        |
| \\                                       |        |
| ;                                        |        |
| :                                        |        |
| ,                                        |        |
| .                                        |        |
| (                                        |        |
| )                                        |        |
| \[                                       |        |
| ]                                        |        |
| {                                        |        |
| }                                        |        |
| <                                        |        |
| >                                        |        |
| \*                                       |        |
| +                                        |        |
| -                                        |        |
| \_                                       |        |
| &                                        |        |
| \|                                       |        |
| $                                        |        |
| #                                        |        |
| \0 (NULL)                                |        |
| \b (backspace)                           |        |
| \n (line feed)                           |        |
| \t (horizontal tab)                      |        |
| \v (vertical tab)                        |        |
| \f (form feed)                           |        |
| \r (carriage return often used as \n \r) |        |
| \e (escape)                              |        |
| SPACE                                    |        |



### Unicode

| Symbol                                   | Unicode | UTF-8 | UTF-16 | UTF-32 |
| ---------------------------------------- | ------- | ----- | ------ | ------ |
| "                                        |         |       |        |        |
| '                                        |         |       |        |        |
| /                                        |         |       |        |        |
| \\                                       |         |       |        |        |
| ;                                        |         |       |        |        |
| :                                        |         |       |        |        |
| ,                                        |         |       |        |        |
| .                                        |         |       |        |        |
| (                                        |         |       |        |        |
| )                                        |         |       |        |        |
| \[                                       |         |       |        |        |
| ]                                        |         |       |        |        |
| {                                        |         |       |        |        |
| }                                        |         |       |        |        |
| <                                        |         |       |        |        |
| >                                        |         |       |        |        |
| \*                                       |         |       |        |        |
| +                                        |         |       |        |        |
| -                                        |         |       |        |        |
| \_                                       |         |       |        |        |
| &                                        |         |       |        |        |
| \|                                       |         |       |        |        |
| $                                        |         |       |        |        |
| #                                        |         |       |        |        |
| \0 (NULL)                                |         |       |        |        |
| \b (backspace)                           |         |       |        |        |
| \n (line feed)                           |         |       |        |        |
| \t (horizontal tab)                      |         |       |        |        |
| \v (vertical tab)                        |         |       |        |        |
| \f (form feed)                           |         |       |        |        |
| \r (carriage return often used as \n \r) |         |       |        |        |
| \e (escape)                              |         |       |        |        |
| SPACE                                    |         |       |        |        |
