# Hassio-on-Pi4
This is just a how-to on installing Hassos on Pi4 using docker on Raspberry Pi OS - lite

##Setup Pi for Boot-From-SSD
1. Download Raspberry Pi OS - lite from https://www.raspberrypi.org/downloads/raspberry-pi-os/
2. Flash This image to an SD card using BalenaEtcher or RaspberryPi Imager
3. Add a blank file called ssh(no extension) to the root directory on boot partition
4. If you are planning on connecting via Wifi, add a file to root directory on boot partion called wpa_supplicant,with your wifi info as follows:
  ``` 
  country=US
      ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
      update_config=1

  network={
	      ssid="Your Network Name"
	      psk="Your Network Password"
	      key_mgmt=WPA-PSK
	  }
  ```
5. Unmount drive
6. Insert the SD Card into your RPi
7. Boot Pi
8. SSH into pi(you can find the ip by logging into your router and checking connected devices)
9. Run: 
   ```
   $ sudo apt update && sudo apt full-upgrade
   ```
10. Once update is complete, reboot pi
11. SSH back into your Pi
12. Edit your Eeprom update file from critical to stable:
    ```
    $ sudo nano /etc/default/rpi-eeprom-update
    ```
13. Check what the latest bootloader version is:
    ```
    $ ls /lib/firmware/raspberrypi/bootloader/stable/
    ```
14. Update your Pi bootloader to the latest file available in above location:
    ```
    $ sudo rpi-eeprom-update -d -f /lib/firmware/raspberrypi/bootloader/stable/pieeprom-2020-06-15.bin
    ```
15. Reboot Pi
    ```
    $ sudo reboot now
    ```
16. SSH back into your pi
17. Install rpi-clone(https://github.com/billw2/rpi-clone) using the following commands:
    ```
    $ git clone https://github.com/billw2/rpi-clone.git 
    $ cd rpi-clone
    $ sudo cp rpi-clone rpi-clone-setup /usr/local/sbin
    ```
18. Run the following command and take careful note of any devices named SDX
    ``` 
    $ ls /dev
    ```
19. Connect your SSD, then run the above command again.
20. Note the SDX device added
21. Clone your SD card using the following command, where SDX should be the device noted in #15
    ```
    $ rpi-clone SDX -e
    ```
22. Once completed, shutdown your pi:
    ```
    $ sudo shutdown now
    ```
23. Remove the SD Card
24. Power up your Pi
25: You should now be Booting from SD

##Install Docker

1. Run the following Command to install Docker:
   ```
   $ curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh
   ```
2. Set non-root docker user:
   ```
   $ sudo usermod -aG docker Pi
   ```
2. Test Docker
   ```
   $ sudo docker run hello-world
   ```
3. Set Docker to boot on startup
   ```
   $ sudo systemctl enable docker
   ```
4.Install Default Home assistant:
  ```
  $ sudo docker run --restart=always --init -d --name="home-assistant" -e "TZ=America/New_York" -v /home/pi/homeassistant:/config --net=host homeassistant/raspberrypi3-homeassistant:stable
  ```
**_OR._** Install HomeAssistant with Supervisor(This is the suggested method if you are coming from a HassOS setup and need supervisor to reinstate a snapsho, also Supervisor has the Add-on installation section.):
   
   https://community.home-assistant.io/t/installing-home-assistant-supervised-on-raspberry-pi-os/201836

