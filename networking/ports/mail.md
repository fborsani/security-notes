---
description: SMTP and POP3
---

# Mail

## POP3

```
USER <username>
PASS <password>
STAT
LIST
RETR <idx>
DELE <idx>
TOP <idx> <num of lines>
RSET
QUIT
```

### Login

```
#wait for +OK response before executing another command
USER <user>
PASS <pass>
```

## IMAP

```
APPEND                Appends the literal argument as a new message to the end of the specified destination mailbox. 	
AUTHENTICATE 	        Indicates a Simple Authentication and Security Layer (SASL) authentication mechanism to the server. 	
CAPABILITY 	          Requests a listing of capabilities that the server supports. 	
CHECK                 Requests a checkpoint of the currently selected mailbox. 	
CLOSE                 Permanently removes all messages that have the \Deleted flag set from the currently selected mailbox, and returns to the authenticated state from the selected state. 	
COPY/UID COPY         Copies the specified message(s) to the end of the specified destination mailbox. 	
CREATE                Creates a mailbox with the given name.
DELETE                Permanently removes the mailbox with the given name.
EXAMINE               Identical to SELECT and returns the same output; however, the selected mailbox is identified as read-only. 	
EXPUNGE               Permanently removes all messages that have the \Deleted flag set from the currently selected mailbox. 	
FETCH/UID FETCH       Retrieves data associated with a message in the mailbox. 
UID                   Unique identifier.
LIST                  Returns a subset of names from the complete set of all names available to the client. 	
LOGIN                 Identifies the client to the server and carries the plaintext password authenticating this user. 	
LOGOUT                Informs the server that the client is done with the connection. 
LSUB                  Returns a subset of names from the set of names that the user has declared as being “active” or “subscribed”. 	
NOOP                  Does nothing. It always succeeds.
RENAME                Changes the name of a mailbox.
SEARCH                Searches the mailbox for messages that match the given searching criteria. 	
SELECT                Selects a mailbox so that messages in the mailbox can be accessed. 
STORE                 Alters data associated with a message in the mailbox.
SUBSCRIBE             Adds the specified mailbox name to the server’s set of “active” or “subscribed” mailboxes as returned by the LSUB command. 	
UNSUBSCRIBE           Removes the specified mailbox name from the server’s set of “active” or “subscribed” mailboxes as returned by the LSUB command. 	
```

## SMTP

```
HELO <domain>
MAIL FROM <sender email>
RCPT TO <dest email>
DATA
RSET
VRFY <email>
NOOP
QUIT
```

### Extended SMTP

```
EHLO
AUTH <method> <user and pass>
STARTTLS
SIZE
HELP
```

### Extended login

The AUTH command is used to authenticate the client to the server. The AUTH command sends the clients username and password to the e-mail server. AUTH can be combined with some other keywords as PLAIN, LOGIN and CRAM-MD5 (e.g. AUTH LOGIN) to use different login methods and different levels of security. After that the AUTH LOGIN command has been sent to the server, the server asks for username and password by sending BASE64 encoded text (questions) to the client. The client sends username and password also using BASE64 encoding.

### Verify users

```
smtp-user-enum -M VRFY -D <mail domain> -u <user> -t <ip>
smtp-user-enum -M VRFY -D <mail domain> -U <file>.txt -t <ip>
```
