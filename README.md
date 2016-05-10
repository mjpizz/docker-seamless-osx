
# docker-seamless-osx

Make docker tools behave natively on OSX using `docker-seamless-osx`.

| When you want to... | Usually you have to...  | `docker-seamless-osx` |
| :--- | :--- | :--- |
| Use `docker` and `docker-compose` commandline tools | Manually run `eval $(docker-machine env default)` before commandline tools will function  | **Automatic** by installing `eval` command in ~/.profile  |
| Share `SSH_AUTH_SOCK` volumes to enable private SSH access (e.g. Github repos) | Manually run an SSH agent to your `docker-machine` and symlink SSH_AUTH_SOCK in VirtualBox to match your local path  | **Automatic** by running SSH agent forwarding as an OSX LaunchAgent  |
| Access forwarded ports from containers on `localhost` | Forced to access the port on IP address given by `docker-machine ip default`, or by manually forwarding ports over SSH | **Automatic** by monitoring `docker events` inside an OSX LaunchAgent and forwarding newly exposed ports from VirtualBox |

## Installation

To install:

    curl https://raw.githubusercontent.com/mjpizz/docker-seamless-osx/master/docker-seamless-osx > /usr/local/bin/docker-seamless-osx
    docker-seamless-osx install

To uninstall:

    docker-seamless-osx uninstall

## When is SSH agent forwarding useful?

Your Docker container often needs the same level of SSH access that your local machine does. A great example is modules (e.g. npm, pip) that come from private Github repositories. Without proper SSH access, your Docker container won't be able to install these dependencies.

Using SSH_AUTH_SOCK, you can grant your `docker` container proper SSH access:

    docker run --volume $SSH_AUTH_SOCK:/port --env SSH_AUTH_SOCK=/port ubuntu

And similarly in a `docker-compose.yml` like this:

    version: "2"
    services:
      example_service:
        build: .
        environment:
          - SSH_AUTH_SOCK=/port
        volumes:
          - $SSH_AUTH_SOCK:/port
          - .:/code
