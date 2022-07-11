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
| "                                        | %22      |
| '                                        | %27      |
| \`                                       | %60      |
| /                                        | %2F      |
| \\                                       | %5C      |
| ;                                        | %3B      |
| :                                        | %3A      |
| ,                                        | %2C      |
| .                                        | %2E      |
| (                                        | %28      |
| )                                        | %29      |
| \[                                       | %5B      |
| ]                                        | %5D      |
| {                                        | %7B      |
| }                                        | %7D      |
| <                                        | %3C      |
| >                                        | %3E      |
| \*                                       | %2A      |
| +                                        | %2B      |
| -                                        | %2D      |
| \_                                       | %5F      |
| &                                        | %24      |
| \|                                       | %7C      |
| $                                        | %24      |
| #                                        | %23      |
| \~                                       | %7E      |
| \0 (NULL)                                | %00      |
| \b (backspace)                           | %08      |
| \n (line feed)                           | %0A      |
| \t (horizontal tab)                      | %09      |
| \v (vertical tab)                        | %0B      |
| \f (form feed)                           | %0C      |
| \r (carriage return often used as \n \r) | %0D      |
| \e (escape)                              | %1B      |
| SPACE                                    | %20 OR + |

### HTML Encoding



| Symbol                                   | Named Encoding | Numeric DEC | Numeric HEX |
| ---------------------------------------- | -------------- | ----------- | ----------- |
| "                                        |                |             |             |
| '                                        |                |             |             |
| \`                                       |                |             |             |
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
| \~                                       |                |             |             |
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



| Symbol                                   | Base36 | Base64 |
| ---------------------------------------- | ------ | ------ |
| "                                        |        |        |
| '                                        |        |        |
| \`                                       |        |        |
| /                                        |        |        |
| \\                                       |        |        |
| ;                                        |        |        |
| :                                        |        |        |
| ,                                        |        |        |
| .                                        |        |        |
| (                                        |        |        |
| )                                        |        |        |
| \[                                       |        |        |
| ]                                        |        |        |
| {                                        |        |        |
| }                                        |        |        |
| <                                        |        |        |
| >                                        |        |        |
| \*                                       |        |        |
| +                                        |        |        |
| -                                        |        |        |
| \_                                       |        |        |
| &                                        |        |        |
| \|                                       |        |        |
| $                                        |        |        |
| #                                        |        |        |
| \~                                       |        |        |
| \0 (NULL)                                |        |        |
| \b (backspace)                           |        |        |
| \n (line feed)                           |        |        |
| \t (horizontal tab)                      |        |        |
| \v (vertical tab)                        |        |        |
| \f (form feed)                           |        |        |
| \r (carriage return often used as \n \r) |        |        |
| \e (escape)                              |        |        |
| SPACE                                    |        |        |



### Unicode

| Symbol                                   | Unicode | UTF-8 | UTF-16 | UTF-32 |
| ---------------------------------------- | ------- | ----- | ------ | ------ |
| "                                        |         |       |        |        |
| '                                        |         |       |        |        |
| \`                                       |         |       |        |        |
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
| \~                                       |         |       |        |        |
| \0 (NULL)                                |         |       |        |        |
| \b (backspace)                           |         |       |        |        |
| \n (line feed)                           |         |       |        |        |
| \t (horizontal tab)                      |         |       |        |        |
| \v (vertical tab)                        |         |       |        |        |
| \f (form feed)                           |         |       |        |        |
| \r (carriage return often used as \n \r) |         |       |        |        |
| \e (escape)                              |         |       |        |        |
| SPACE                                    |         |       |        |        |
