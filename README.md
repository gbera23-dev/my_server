# my_server

A minimal **TCP server in C** that gives each connecting client its own shell session, using the `my_terminal` shell.

## How it works

1. Creates an IPv4 TCP socket (`SO_REUSEADDR`), binds it to port **8082** and listens.
2. For each accepted connection it `fork`s a child process.
3. The child redirects stdin and stdout to the connection with `dup2` and `exec`s `utils/my_terminal` (through `stdbuf` so output is line-buffered).
4. The parent waits for the session to finish, closes the connection and goes back to accepting.

Clients are served one at a time.

## Layout

```
core/main.c          # server source
utils/my_terminal    # prebuilt shell binary executed for each client
main                 # prebuilt server binary
```

## Build and run

```bash
gcc core/main.c -o main
./main                 # run from the repo root so utils/my_terminal resolves
```

Then connect from another terminal:

```bash
nc localhost 8082
```

## Security note

The server gives connecting clients command execution on the host with no authentication or encryption. It is a learning project, so only run it on a trusted local network.

Related project: `my_terminal` (the shell source).
