# Installing Docker on MacOS with M1 (Arm64)

## Purpose
With the recent change to licensing for Docker Desktop, if you work at a organization that has more than 250 employees and earns more than $10 million in annual revenue your organization must pay a license fee to use Docker Desktop. Provide a legal, free way for users running MacOS with a M1 chip to use Docker locally.

[Docker Desktop Pricing Information](https://www.docker.com/pricing/)

_Note: If you're running MacOS with a Intel based chip, I highly recommend utilizing docker-machine with Oracle Virtual Box. This will provide a easier installation as it's much more automated. M1 users can't utilize this solution as Oracle Virtual Box doesn't supporting Arm64 based chips._

## Prerequisites
1. Homebrew Installed
    - [Link](https://brew.sh/)

## Instructions
1. Open Terminal
    - Cmd + Spacebar -> Type `Terminal` -> Enter
2. Install UTM
    - In Terminal -> Type `brew install --cask utm` -> Enter
3. Open UTM
    - Cmd + Spacebar -> Type `UTM` -> Enter
4. Download Debian 10.4 from the UTM Gallery
    - In UTM -> Click `+` -> Click `Download prebuilt from UTM Gallery...` -> Click `Debian 10.4 (Minimal)` -> Click `Download`
5. Load VM into UTM
    - Open Finder -> Downloads -> Double-click `Debian ARM.utm`
6. Configure SSH Port Forwarding
    - In UTM -> Select `Debian ARM` -> Ctrl + Click -> Click `Edit` -> Click `Network` -> Click `New` -> Enter `22022` for `Host Port` and `22` for `Guest Port` -> Click `Save`
7. Start VM
    - In UTM -> Select `Debian ARM` -> Ctrl + Click -> Click `Start`
8. Update Packages on VM
    - Signin using default credentials (user: `debian`, password: `debian`)
    - Run `sudo apt update`
        - _Note: You will be prompted about respository changes, accept them_
    - Run `sudo apt upgrade`
9. Install SSH on VM
    - Run `sudo apt install openssl-server`
    - Run `sudo systemctl status ssh` -> Ensure it's Active -> Press `q` to exit
10. Install Docker on VM
    - Run `sudo apt-get purge docker lxc-docker docker-engine docker.io`
    - Run `sudo apt-get update`
    - Run `sudo apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common`
    - Run `curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -`
    - Run `sudo add-apt-repository "deb [arch=arm64] https://download.docker.com/linux/debian buster stable"`
    - Run `sudo apt-get update`
    - Run `sudo apt-get install docker-ce docker-ce-cli containerd.io`
    - Run `sudo systemctl status docker` -> Ensure it's Active -> Press `q` to exit
11. Configure Docker for Remote Connection on VM
    - Run `sudo usermod -aG docker $USER`
12. Add docker_tunnel Alias and Configure Docker Remote on Mac
    - In Terminal -> Run `vi <your shell init file>` (I.E. `.zshrc` or `.bashrc`)
    - Press `i` -> Paste into a new line `ssh debian@127.0.0.1 -p 22022 -N -L/tmp/docker-on-debian.sock:/var/run/docker.sock ssh://debian@127.0.0.1` -> Paste into a new line `export DOCKER_HOST=unix:///tmp/docker-on-debian.sock` -> Esc -> Type `:wq` -> Enter
13. Open Docker Tunnel
    - Cmd + Spacebar -> Type `Terminal` -> Enter
    - In Terminal -> Type `docker_tunnel` -> Enter
        - _Note: If you see nothing this is expected, it means the tunnel is open_
14. Verify Docker Remote
    - In the original Terminal -> Run `docker system info | grep System` -> Verify output is `Operating System: Debian GNU/Linux 10 (buster)`

## Usage
In order to utilize Docker on your VM, ensure your VM is running in UTM (See Step 7) and your tunnel is open (See Step 13). You can now use docker as you normally would, enjoy!

## Resources
This documentation was made by aggregating existing documentation listed below:
* https://www.codeluge.com/post/setting-up-docker-on-macos-m1-arm64-to-use-debian-10.4-docker-engine/
* https://phoenixnap.com/kb/how-to-install-docker-on-debian-10