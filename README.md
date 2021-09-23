# RPI_SRV-SETUP on Ubuntu 20.04

# First

Using Raspberry Pi Imager
Connect an SD card reader with the SD card inside. Open Raspberry Pi Imager and choose the required OS from the list presented. Choose the SD card you wish to write your image to. Review your selections and click 'WRITE' to begin writing data to the SD card. Please see the following video for more infomration: https://www.youtube.com/watch?v=J024soVgEeM and this site to download: https://www.raspberrypi.org/software/

# Next we setup the card for headless opteration

Follow these two steps

## Enabling SSH

In the /boot partition we need to create an empty text file named 

`ssh` 

(no file extension)

## Headless Wi-Fi / Ethernet (pre 20.04)

In the /boot partition we need to create create a text file called:

`wpa_supplicant.conf`

    country=US
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    
    network={
    scan_ssid=1
    ssid="your_wifi_ssid"
    psk="your_wifi_password"
    }

## Headless Wi-Fi / Ethernet (post 20.04)
Find the file named “network-config” and open it in a text editor. On Windows, you can right-click -> “open with” and select any text editor you want.

`network-config`

    version: 2
    ethernets:
       eth0:
          dhcp4: true
	  optional: true
    wifis:
      wlan0:
        dhcp4: true
	optional: true
	access-points:
	  "YOUR_WIFI_NAME":
	    password: "YOUR_WIFI_PASSWORD"



A few things to pay attention to:

1. Make sure the indentation is exactly 2 spaces. No tab, no 4 spaces.
2. Replace YOUR_WIFI_NAME with your actual Wi-Fi name. Keep the quotes “”.
3. Replace YOUR_WIFI_PASSWORD with the password for the Wi-Fi. Keep the quotes “”.

# Setting up ssh (per 20.04)
In the /boot partition we need to create create an empty text file called ssh:

`touch ssh`

# Setting up ssh (post 20.04)
For ssh, you should not need to do anything.

* Now remove the SD card and put it into your raspberry pi and power it on.  This will take anywhere from 3 to 5 minutes.

# Setting up Ubuntu remotely

You should now be able to ssh into your raspberry pi remotely using putty or command line.

Please log using the following: 
username:
`ubuntu`
password:
`ubuntu`

Once logged in you will be asked to change your password please do so.  Once this is done the connection will be be reset and you will need to log back into the system using the username: ubuntu and your new password.

Once log into please do the following:

`sudo apt update && sudo apt upgrade -y`

Then reboot the system

`sudo reboot`

Once log into please add the pi user

`sudo adduser pi`

Below is the output.  Please enter a password and then confirm it and press enter through the rest of the questions, for expedency, or answer if you like.

    Enter new UNIX password: 
    Retype new UNIX password: 
    passwd: password updated successfully
    Changing the user information for username
    Enter the new value, or press ENTER for the default
	    Full Name []: 
	    Room Number []: 
	    Work Phone []: 
	    Home Phone []: 
	    Other []: 
    Is the information correct? [Y/n] 

Inaddition please add the pi user to the sudoers so if we need to have super user privilages we can do so.

`sudo usermod -aG sudo pi`

Once this is done please logout and back in for changes to take effect.

# Installing some terminal apps

- cheat Sheet

``

    $sudo apt install rlwrap
    $curl https://cht.sh/:cht.sh | sudo tee /usr/local/bin/cht.sh
    $chmod +x /usr/local/bin/cht.sh
``
   
Usage:

`$cht.sh go reverse a list`

- bpytop

``

    $sudo apt install python3-pip
    $sudo pip3 install psutil --upgrade
    $pip3 install bpytop --upgrade   

or

    $sudo apt install bpytop
``

Usage:

`$byptop`


- Speedtest

``

    $sudo apt install speedtest-cli
``
    
Usage:
    `$speedtest`


- lolcat

``

    $sudo apt install lolcat
    or
    $sudo snap install lolcat
``

Usage:

    `$speedtest | lolcat`

- lsdir

Go to the release page at https://github.com/Peltoche/lsd/releases and very the lastest release date to download.

``

    $wget https://github.com/Peltoche/lsd/releases/download/0.20.1/lsd_0.20.1_arm64.deb
    $sudo dpkg -i lsd_0.20.1_arm64.deb
``


Use a text editor and edit ~/.bashrc

``

$nano ~/.bashrc

replace 

alias ls='ls --color=auto'

with

alias ls='lsd'

``

Usage:

 `$ls`


# Install Docker and Compose

    curl -sSL https://get.docker.com | sh
    
Add the pi user to the docker group so we do not have to use sudo evertime we want to launch a container.

`sudo usermod -aG docker pi`

Once this is done please logout and back in for changes to take effect.

    sudo apt-get install -y libffi-dev libssl-dev python3 python3-pip
    sudo pip3 -v install docker-compose

# Create a Docker Network

`docker network create -d macvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1  -o parent=eth0 my_net`

# Install Portainer

    docker pull portainer/portainer-ce:linux-arm
    sudo docker run --restart always -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:linux-arm
    
You should now be able to navigate to the IP address of your Raspberry Pi and port 9000 to access Portainer. When you get there, create a username and password.

`http://[RASPBERRY_PI_IP_ADDRESS]:9000`

# Install Webmin

add webmin to the repository list.

`sudo nano /etc/apt/sources.list.d/webmin.list`

    deb https://download.webmin.com/download/repository sarge contrib

Press control + o and then Control + x to exit

Get the key for the repository.

    wget https://download.webmin.com/jcameron-key.asc
    sudo apt-key add jcameron-key.asc

Next, we will need to get the package information from the Webmin Debian repository.

`sudo apt update`

Now we will install Webmin

    sudo apt install apt-show-versions libauthen-pam-perl libio-pty-perl libnet-ssleay-perl perl-openssl-defaults
    sudo apt install webmin
 
 if you get an error while installing do the following:
 
    sudo-fix-broken install
    sudo apt install webmin
    
I know it seem redundent to run the install twice just trust me when I say you need to here.

You should now be able to navigate to the IP address of your Raspberry Pi and port 10000 to access Webmin. 

`http://[RASPBERRY_PI_IP_ADDRESS]:10000`

# piKIS setup

PiKISS (Pi Keeping It Simple, Stupid!) are scripts (Bash) for Raspberry Pi boards (Raspberry OS mainly, TwisterOS and Debian derivates, all of them in 32 bits versions), which has a menu that will allow you to install some applications or configure files automatically as easy as possible.

`curl -sSL https://git.io/JfAPE | bash`

# Install Pi-Apps

Pi-Apps is a GUI menu which basically is a list of pre-made apps you can install with one click for the desktop.

`wget -qO- https://raw.githubusercontent.com/Botspot/pi-apps/master/install | bash`

# Install HP printer drivers

`https://sourceforge.net/projects/hplip/`

or

`https://developers.hp.com/hp-linux-imaging-and-printing`


# Please see the below for additional services you can run:


Plex media server

`https://github.com/plexinc/pms-docker`


Jellyfin Media server

    https://hub.docker.com/r/linuxserver/jellyfin 
    or 
    https://github.com/jellyfin/jellyfin


Fog server (A free open-source network computer cloning and management solution)

`https://github.com/RPTST/fog-server`


LANCACHE monolithic and DNS server (LAN Party game caching made easy)

    https://github.com/lancachenet/docker-compose
    https://github.com/lancachenet/lancache-dns


Samba share (File Share)

`https://github.com/DeftWork/samba`


NextCloud Server (Dropbox at home replacement)

`https://hub.docker.com/r/linuxserver/nextcloud`


Homeassistant server (Home automation)

`https://hub.docker.com/r/linuxserver/homeassistant`


LibraNMS (Network Monitoring System)

    https://github.com/librenms/docker 
    or 
    https://github.com/jarischaefer/docker-librenms


Pi-Hole (whole home network add blocker and URL Filter)

`https://hub.docker.com/r/pihole/pihole`


AdGuardHome (whole home network add blocker and URL Filter)

`https://hub.docker.com/r/adguard/adguardhome`


Google Coder (learn coding at home)

`https://github.com/hypriot/rpi-google-coder`


Installing SmokePing


    sudo apt update && sudo apt upgrade
    sudo apt install smokeping curl libauthen-radius-perl libnet-ldap-perl libnet-dns-perl libio-socket-ssl-perl libnet-telnet-perl libsocket6-perl libio-socket-inet6-perl apache2 
    sudo apt install apache2 sendmail
    a2enmod cgi
    service apache2 restart

Then edit the configuration file for smokeping:

sudo nano /etc/smokeping/config.d
	

    + Server
    menu = Mon CloudFlareDNS
    title = Mon CloudFlareDNS
    
    ++ Localhost
    menu = Localhost
    title = localhost
    host = 127.0.0.1
    ++ CloudFlareDNS
    menu = CloudFlareDNS
    title = CloudFlareDNS
    host = 1.1.1.1

Now access the webpage at `http://<your_server_ip>/smokeping/smokeping.cgi`, choose `CloudFlareDNS` from the menu.
