# Chat TCP Protocol

## Description
A TCP protocol I made to build a chat program.

## Notes
* The format of sending any request is the method a space and then all payloads seperated with spaces.
* Whenever sending payload, it is always going to be sent encoded with Base64 to allow all characters in payload, except for error messages, and permission levels.
* The methods and modes are uppercase to show they are commands while talking about it in chat.
* Permission level can range between 1 and 4
* The mode FROM_TELNET does not send any response back to the server as if it is in telnet mode, it is meant to be read by humans.
* For the methods CHANGE_PERM_LEVEL and CREATE_ACCOUNT, the permissions required is 1 above the permission level you want to set. For example, if you are at permission level 3, you can set people's permissions levels who are less than 3. However, you cannot set someone's permission level if they are 3+.
* GC Stands for Group Chat
* Group chats have similar qualities to accounts in that you can use SEND and RECV methods and pass the GC name as the username. GCs do not have a permission level like accounts, and you can't login as one.
* When you use the CREATE_GC method, whatever account you are logged into is automatically added to the group.

## Methods, Modes, and Errors that are passed using the protocol

### Methods
| Method and Payload                                      | Description                                                                       | For clients, servers, or both | Permissions required   |
|---------------------------------------------------------|:---------------------------------------------------------------------------------:|:------------------------------|:-----------------------|
| SEND_TO_ID                                              | Tells the client to send the ID given from the session in the TO mode.            | Client                        | 1                      |
| READY                                                   | Tells the other side they are ready to accept commands, always used on connect.   | Server                        | None                   |
| CLOSE                                                   | Close connection.                                                                 | Both                          | 1                      |
| LOGIN (username) (password)                             | Making a login request with username and password to the server.                  | Client                        | 1                      |
| CREATE_ACCOUNT (username) (password) (permission level) | Create an account                                                                 | Client                        | 2 or more (read notes) |
| CHANGE_PERM_LEVEL (username) (permission level)         | Change permission level on an account                                             | Client                        | 2 or more (read notes) |
| DELETE_ACCOUNT (username)                               | Delete an account                                                                 | Client                        | 3                      |
| CHANGE_PASSWORD (current password) (password)           | Changes the password of the account you are logged into                           | Client                        | 1                      |
| GET_ID                                                  | Get the session ID.                                                               | Client                        | 1                      |
| ECHO_FROM (anything or nothing)                         | Sends the same message back through a FROM session.                               | Both                          | 1                      |
| SEND (username) (message)                               | Sends a message to another user                                                   | Client                        | 1                      |
| RECV (username) (message)                               | Recieve a message from another user using the SEND method                         | Server                        | None                   |
| RECV_GC (gc name) (username) (message)                  | Recieve a message from a member of a group chat using the SEND method             | Server                        | None                   |
| CREATE_GC (name)                                        | Creates a group chat                                                              | Client                        | 2                      |
| DELETE_GC (name)                                        | Deletes a group chat                                                              | Client                        | 3                      |
| INVITE_TO_GC (gc name) (username)                       | Invite someone to a group chat                                                    | Client                        | 2                      |
| GC_INVITE_RECV (gc name)                                | Indication that you got an invite to a group chat                                 | Server                        | None                   |
| ACCEPT_GC_INVITE (gc name)                              | Accept an invitation to a group chat                                              | Client                        | 1
| REMOVE_FROM_GC (gc name) (username)                     | Removes someone from a group chat                                                 | Client                        | 3                      |
| CONSOLE_LOG (message)                                   | Tells the server to log a message to the server logs.                             | Client                        | 3                      |
| ERROR (error message)                                   | Sends to the other side an error message, usually in response to another request. | Both                          | 1                      |
| ACK                                                     | Acknowledging a request, usually means a request succeeded.                       | Both                          | 1                      |

### Modes
| Mode        | Description                            |
|-------------|:---------------------------------------|
| TO          | Sending to the server                  |
| TO_TELNET   | Sending to the server in telnet mode   |
| FROM        | Sending from the server                |
| FROM_TELNET | Sending from the server in telnet mode |

### Error Messages
| Error                 | Description                                                                 | Sent to clients, servers, or both |
|-----------------------|:---------------------------------------------------------------------------:|:----------------------------------|
| LoginRequired         | You need to login to run this command                                       | Clients                           |
| LoginFailed           | You sent an incorrect username or password                                  | Clients                           |
| InvalidB64Code        | You sent invalid base64 code or undecodable strings                         | Both                              |
| InvalidCommand        | You sent an invalid or non-existant command                                 | Both                              |
| PermissionDenied      | Your permission level is below the required level to issue the command      | Clients                           |
| NoFROMSession         | You sent a command that required a FROM session attached when there is none | Clients                           |
| AccountNotFound       | The server could not find the account/gc you specified in a command         | Clients                           |
| AccountExists         | You tried to make an account/gc that already existed                        | Clients                           |
| AccountAlreadyInvited | You tried to invite someone to a group chat who was already invited         | Clients                           |
| NotInvited            | You tried to accept an invite that you didn't get                           | Clients                           |
| NotAMember            | You tried to run an operation on a GC involving a user that wasnt in the GC | Clients                           |