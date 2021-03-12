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

## Headless Wi-Fi / Ethernet

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


Now remove the SD card and put it into your raspberry pi and power it on.  This will take anywhere from 3 to 5 minutes.

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


# Please see the below for additional services you can run:


Fog server (PXE boot server)

`https://github.com/RPTST/fog-server`


LANCACHE server

`https://github.com/lancachenet/docker-compose`


Plex media server

`https://github.com/plexinc/pms-docker`


Samba share

`https://github.com/DeftWork/samba`


Jellyfin Media server

`https://hub.docker.com/r/linuxserver/jellyfin` or `https://github.com/jellyfin/jellyfin`


Homeassistant server

`https://hub.docker.com/r/linuxserver/homeassistant`


Google Coder

`https://github.com/hypriot/rpi-google-coder`
