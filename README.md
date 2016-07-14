
# docker-seamless-osx

Make docker tools behave natively on OSX using `docker-seamless-osx`.

## How do I install it?

To install, run these commands in your terminal:

    dest=/usr/local/bin/docker-seamless-osx && curl https://raw.githubusercontent.com/mjpizz/docker-seamless-osx/master/docker-seamless-osx > $dest && chmod +x $dest
    docker-seamless-osx install

To uninstall:

    docker-seamless-osx uninstall

## Why use this tool?

| When you want to... | Usually you have to...  | `docker-seamless-osx` |
| :--- | :--- | :--- |
| Use `docker` and `docker-compose` commandline tools | Manually run `eval $(docker-machine env default)` before commandline tools will function  | **Automatic** by installing `eval` command in ~/.profile  |
| Share `SSH_AUTH_SOCK` volumes to enable private SSH access (e.g. Github repos) | Manually run an SSH agent to your `docker-machine` and symlink SSH_AUTH_SOCK in VirtualBox to match your local path  | **Automatic** by running SSH agent forwarding as an OSX LaunchAgent  |
| Access forwarded ports from containers on `localhost` | Forced to access the port on IP address given by `docker-machine ip default`, or by manually forwarding ports over SSH | **Automatic** by monitoring `docker events` inside an OSX LaunchAgent and forwarding newly exposed ports from VirtualBox |
| Resolve DNS properly, particularly when using a VPN | Manually stopping Docker Machine and modifying VirtualBox resolver using `VBoxManage modifyvm $DOCKER_MACHINE_NAME --natdnshostresolver1 on` | **Automatic** by modifying VirtualBox resolver during installation |

**What about Docker for Mac Beta?** As of June 2016, the Docker team is still improving the beta, which currently [cannot share SSH_AUTH_SOCK yet](https://forums.docker.com/t/can-we-re-use-the-osx-ssh-agent-socket-in-a-container/8152/5) and it also [doesn't handle DNS resolution in all cases yet](https://forums.docker.com/t/docker-for-mac-host-vpn-dns-dont-cooperate/8149).

## When is SSH agent forwarding useful?

Your Docker container often needs the same level of SSH access that your local machine does. A great example is modules (e.g. npm, pip) that come from private Github repositories. Without proper SSH access, your Docker container won't be able to install these dependencies.

Using SSH_AUTH_SOCK, you can grant your `docker` container proper SSH access:

    docker run --volume $SSH_AUTH_SOCK:/sock --env SSH_AUTH_SOCK=/sock ubuntu

Alternatively, you can do this in a `docker-compose.yml` as well:

    version: "2"
    services:
      example_service:
        build: .
        environment:
          - SSH_AUTH_SOCK=/sock
        volumes:
          - $SSH_AUTH_SOCK:/sock
