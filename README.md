# Simple FTP implementation according to RFC 913

CLI implementation of client and server application of `Simple FTP` protocol according to `RFC 913` in `C` language.
## Client
The client is very simple and tries to approximate the `Simple FTP` protocol. Control is by entering the commands described in `RFC 913` with the `enter` key, which are sent directly to the server part of the application where most of the overhead takes place. The client part, in addition to sending commands, performs checks on the commands entered and is supplemented with its own informative messages.
### startup:
>./ipk-simpleftp-client -h <ip_server_address> {-p <server_port>}
> (default port is 115)
>For example: ./ipk-simpleftp-client -p 65001 -h 147.229.176.19

The file download is done via standard output and it is up to the user to save the data. The user can redirect the standard output to the file during the download and remove the extra lines from the file. (This implementation is chosen to keep the client as simple as possible.) 
However, the client can also be served using ``scripts``, or files containing `RFC 913` line-separated commands. This file can then just be redirected to the standard application input and its contents will be executed.

## Server
From the user's point of view, the server application is very simple. After startup, messages describing what is happening on the server are printed to the console, and at the same time it is possible to enter only a single `DONE` command via the standard input, confirmed by the `enter` key, which terminates the server and disconnects all clients.

### startup:
>./ipk-simpleftp-server {-i <ethernet_interface>} {-p <server_port>} -u <user_file_path>
> (default port is 115)
> (if no specific interface is specified, all available interfaces are used)
> example: ./ipk-simpleftp-server -u ./users.txt -p 65001

When a new client is connected, a process is created to serve that client.

## Notes:
- The `ACCT` command have not been implemented.
- The `RETR` command is simplyfied.
- The `STOR` and `SIZE` commands for sending files to the server have not been implemented. 

# RFC 913 commands
- USER <user-id>
- ACCT <account>
- PASS <password>

- TYPE { A | B | C } (ASCII | BINARY | CONTINUOUS - Mapping of the stored file to a stream of bytes of the given type during file transfer.)
- LIST { F | V } <directory-path> (F - standard formatted directory listing | V - verbose directory listing)
- CDIR <new-directory> (change the current working directory on the remote host)
- KILL <file-spec> (delete the file from the remote system)
- NAME <old-file-spec> (renames the old-file-spec ...)
- TOBE <new-file-spec> (... to be new-file-spec on the remote system)

- DONE

- RETR <file-spec> (download specific file from the remote system)
- SEND (ok, waiting for file)
- STOP (abort, when you don't have enough space to store file)

- STOR { NEW | OLD | APP } <file-spec> (NEW - create a new generation of the file and not delete the existing one | OLD - write over the existing file, if any, or else create a new | APP - appended to the file or create a new file, if the file doesn't exist)
- SIZE <number-of-bytes-in-file> (is the exact number of 8-bit bytes you will be sending)
