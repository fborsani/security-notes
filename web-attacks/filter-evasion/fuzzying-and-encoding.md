# Fuzzying and encoding

### HTML and Unicode Encoding

| Symbol                                   | Entity Name | Numeric         | Unicode |
| ---------------------------------------- | ----------- | --------------- | ------- |
| "                                        | \&quot;     | \&#34; \&#X22;  | U+0022  |
| '                                        | \&apos;     | \&#39; \&#X27;  | U+0027  |
| \`                                       | \&grave;    | \&#96; \&#X60;  | U+0060  |
| /                                        | \&sol;      | \&#47; \&#X2F;  | U+002F  |
| \\                                       | \&bsol;     | \&#92; \&#X5C;  | U+005C  |
| ;                                        | \&semi;     | \&#59; \&#X3B;  | U+003B  |
| :                                        | \&colon;    | \&#58; \&#X3A;  | U+003A  |
| ,                                        | \&comma;    | \&#44; \&#X2C;  | U+002C  |
| .                                        | \&period;   | \&#46; \&#X2E;  | U+002E  |
| (                                        | \&lpar;     | \&#40; \&#X28;  | U+0028  |
| )                                        | \&rpar;     | \&#41; \&#X29;  | U+0029  |
| \[                                       | \&lbrack;   | \&#91; \&#X5B;  | U+005B  |
| ]                                        | \&rbrack;   | \&#93; \&#X5D;  | U+005D  |
| {                                        | \&lbrace;   | \&#123; \&#X7B; | U+007B  |
| }                                        | \&rbrace;   | \&#125; \&#X7D; | U+007D  |
| <                                        | \&lt;       | \&#60; \&#X3C;  | U+003C  |
| >                                        | \&gt;       | \&#62; \&#X3E;  | U+003E  |
| \*                                       | \&ast;      | \&#42; \&#X2A;  | U+002A  |
| +                                        | \&plus;     | \&#43; \&#X2B;  | U+002B  |
| -                                        | NA          | \&#45; \&#X2D;  | U+002D  |
| \_                                       | \&lowbar;   | \&#95; \&#X5F;  | U+005F  |
| &                                        | \&amp;      | \&#38; \&#X26;  | U+0026  |
| \|                                       | \&vert;     | \&#124; \&#X7C; | U+007C  |
| $                                        | \&dollar;   | \&#36; \&#X24;  | U+0024  |
| #                                        | \&num;      | \&#35; \&#X23;  | U+0023  |
| \~                                       | \&tilde;    | \&#126; \&#X7E; | U+007E  |
| \0 (NULL)                                | NA          | \&#0; \&#X0;    | U+0000  |
| \b (backspace)                           | NA          | \&#8; \&#X8;    | U+0008  |
| \n (line feed)                           | NA          | \&#10; \&#XA;   | U+000A  |
| \t (horizontal tab)                      | NA          | \&#9; \&#X9;    | U+0009  |
| \v (vertical tab)                        | NA          | \&#11; \&#XB;   | U+000B  |
| \f (form feed)                           | NA          | \&#12; \&#XC;   | U+000C  |
| \r (carriage return often used as \n \r) | NA          | \&#13; \&#XD;   | U+000D  |
| \e (escape)                              | NA          | \&#27; \&#X1B;  | U+001B  |
| SPACE                                    | NA          | \&#32; \&#X20;  | U+0020  |

Unicode format can be converted in URL encoding or CSS encoding as follows

```
U+0022 #full Unicode for double quote character
%22    #URL encoded char
\22    #CSS encoded char, can be used as part of a string
```

Url encoding reserved characters

|       | URL Encoded |                              |
| ----- | ----------- | ---------------------------- |
| #     | %23         | Anchor point                 |
| ?     | %3F         | Arguments start point        |
| &     | %24         | Argument separator           |
| %     | %25         | Character encoding prefix    |
| /     | %2F         | URL separator                |
| +     | %2B         | Alternative symbol for space |
| SPACE | %20 OR +    |                              |
