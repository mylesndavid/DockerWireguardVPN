# Docker Wireguard Installation
By Myles David
## Docker Installation
1. Install Neccesary Tools
~~~ 
# sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
~~~
2. Add Docker Key 
~~~
# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
~~~
3. Add Docker Repo 

*We will use the 32bit/64bit OS repo*
~~~
# sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
~~~
4. Switch to the propper repo
~~~
# apt-cache policy docker-ce
~~~
5. Install Docker 
~~~
# sudo apt install docker-ce -y
~~~
> If you're not currently root, use this commmand to allow docker access without having to use `sudo` in the future.
~~~
# sudo usermod -aG docker ${USER}
~~~
## Install Docker-Compose
~~~
# sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
~~~
6. Set Permissions 
~~~
# sudo chmod +x /usr/local/bin/docker-compose
~~~
# Set up Wireguard
1. Create neccesssary directories and modify yml file.
~~~
# mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml
~~~
2. Insert the following content into the yml file and save.
~~~
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=134.122.31.185
      - SERVERPORT=80
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 80:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
~~~
> Several modifications need to be made in order to customize our wireguard profile. 

1. Change the timezone. Modify the line that currently says `TZ=Asia/Hong_Kong` To correspond to your current timezone. 
> A list of timezones can be found here: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

2. Change the server IP address on the line that says `SERVERURL=1.2.3.4` 
> this can be found on Vultr or your DigitalOcean dashboard

*I also changed the port forwarding to port 80 so that It will not be blocked on public wifi*

Start Wireguard on your server 
~~~
# cd ~/wireguard/
docker-compose up -d
~~~
## Connect your phone to Wireguard 

Open the excecution log and scan the QR code that is called phone1 on your Wireguard VPN connection settings.

**Phone before VPN Activation**
![Phone Before VPN](https://github.com/mylesndavid/DockerWireguardVPN/blob/main/20211206_174638000_iOS.png)

**Phone after VPN Activation**
![Phone After VPN](https://github.com/mylesndavid/DockerWireguardVPN/blob/main/20211206_174653000_iOS.png)

**Laptop VPN Proof**
![Laptop VPN Proof](https://github.com/mylesndavid/DockerWireguardVPN/blob/main/laptop%20vpn%20proof%20.png)

**Laptop Active Tunnel Proof**
![Laptop VPN Proof](https://github.com/mylesndavid/DockerWireguardVPN/blob/main/wireguard%20active%20tunnel.png)
