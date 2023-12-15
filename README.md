# CURR-Pilot-Display
Pilot Display code for the 2023 Monaco Energy Boat Race

## Replication

### Flashing the SD card

- Use the Raspberry Pi official installer to install this version of the RasPi OS.  In this case, we actively don't want the desktop environment, so we can get away with using that version of the OS.

![](/imgs/how_to_flash_sd_card.png)

- Insert your Wifi details, and configure an SSH login for the Raspberry Pi. This is needed to run your raspberry pi remotely ("headlessly").

- Verify that you can access your Raspberry Pi over the network from your PC.

```bash
ssh krish@pizero.local # insert your login details here
```

If you've done this successfully, you'll get output that looks like this:

```
Linux pizero 6.1.0-rpi7-rpi-v8 #1 SMP PREEMPT Debian 1:6.1.63-1+rpt1 (2023-11-24) aarch64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Dec 15 16:40:03 2023 from xxxx:xxxx:xxxx:xxxx:xxxx:xxx:xxxx:xxxx
krish@pizero:~ $
```

- OPTIONAL, BUT HIGHLY RECCOMMENDED: After you can `ssh` the Raspberry Pi with your password, you may find typing your password to be a bit repetitive. Running this command will setup SSH certificates on both sides, and you won't need to enter your password again:

```bash
ssh-copy-id krish@pizero.local
```

### Custom scripts on login

- https://medium.com/@reefland/replace-the-linux-console-login-with-something-different-ad08ba192dd8


```bash
sudo useradd racedriver --no-create-home
sudo usermod --lock racedriver
sudo mkdir -p /home/racedriver/{.config/btop,.fonts,bin}
sudo chown racedriver /home/racedriver/

cat << EOF | sudo tee /home/racedriver/.profile
HOME=/home/racedriver/
PATH=/home/racedriver/bin
htop; exit
EOF

sudo ln -s /usr/bin/htop /home/racedriver/bin
```

- Check: run `sudo su - racedriver` in your console

We need to modify the `getty@tty.service` using `systemctl`. 

```bash
sudo EDITOR=nano systemctl edit getty@tty1.service
```

Insert this in the file:

```
[Service]
ExecStart=
ExecStart=-/sbin/agetty --noissue --autologin racedriver %I $TERM
Type=idle
```
