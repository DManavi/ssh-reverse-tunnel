# ssh-reverse-tunnel
How to tunnel server traffic through your personal computer

## Definitions
Your PC = Your desktop computer
Server  = Server you're trying to proxy it's traffic


1. Create a local SOCKS5 server.
```sh
ssh -D 1080 -C -N $USER@localhost -v
```
> This command runs a local SOCK5 server on your PC on the PORT 1080 TCP.


2. Create a SSH reverse tunnel on the server and pass traffic received from this tunnel through your newly local SOCKS5 server.
```sh
ssh -N -R1090:localhost:1080 user@server-ip -v
```
> This command create a pipe between port 1090 on the server and 1080 on your PC.

> If you have a container (docker, lxd or ...) that you want to tunnel it's traffic. It's necessary to run step 3. Because it step 2 only creates an endpoint on your loopback address. Assume that your containars IP range is 10.0.0.0/24, the host IP address is 10.0.0.1 and the container IP address is 10.0.0.10.

3. Now create a loopback tunnel on the server to bind on non-loopback IP address(es). 
```sh
ssh -L10.0.0.1:1080:localhost:1090 user@localhost -N -v
```
 
 4. Now you can set your SOCKS5 address on your containers
 ```sh
 curl https://www.github.com --socks5 10.0.0.1:1080
 ```
