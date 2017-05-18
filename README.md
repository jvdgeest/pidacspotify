# Raspberry Pi + USB DAC + Spotify Connect

Instructions for setting up a Raspberry Pi with Spotify Connect and an external DAC via USB.

## WiFi credentials

Location: `/etc/wpa_supplicant/wpa_supplicant.conf`

```
country=NL
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
        ssid="SSID"
        psk="password"
}
```

## Packages

The list below is if you want to use Shairport Sync (see https://github.com/mikebrady/shairport-sync for instructions).

```sudo apt-get install avahi-utils build-essential chkconfig git libao-dev libavahi-client-dev libcrypt-openssl-rsa-perl libio-socket-inet6-perl libssl-dev libwww-perl pkg-config build-essential git xmltoman autoconf automake libtool libdaemon-dev libasound2-dev libpopt-dev libconfig-dev avahi-daemon libavahi-client-dev libssl-dev```

## Install Spotify connect

```
curl -O curl -OL https://github.com/Fornoth/spotify-connect-web/releases/download/0.0.3-alpha/spotify-connect-web.sh374
chmod u+x spotify-connect-web.sh
./spotify-connect-web.sh install
```

Hint: bug fixes in master branch. You can edit line 9 in the above shell script to download master branch instead of 0.3 release.

Add `spotify_appkey.key` to `spotify-connect-web-chroot/usr/src/app/`.

## alsa-base.conf

Location: `/etc/modprobe.d/alsa-base.conf`

```
# This sets the index value of the cards but doesn't reorder.
options snd_usb_audio index=0
options snd_bcm2835 index=1

# Does the reordering.
options snd slots=snd_usb_audio,snd_bcm2835
```

## asound.conf
Locations: `/etc/asound.conf` and  `~/spotify-connect-web-chroot/etc/asound.conf`

```
pcm.plug {
        type plug
        slave.pcm "hw:0,0"
}
```

## System service
Location: `/etc/systemd/system/scs.service`

```
[Unit]
Description=Spotify Connect
After=network-online.target
[Service]
Type=idle
User=pi
ExecStartPre=/bin/sleep 20
ExecStart=/home/pi/spotify-connect-web.sh -o plug --username username --password password --bitrate 320 --name JBL
Restart=always
RestartSec=10
StartLimitInterval=30
StartLimitBurst=20
[Install]
WantedBy=multi-user.target
```
