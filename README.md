# raspberry-spotifyd

This is a project to run spotifyd on raspberry pi, play the output to a speaker ( avr ) while selecting playlist and start/stop/control/volume using a spotify client on any device.
Many thanks to spotifyd project 

## Get Spotify source 
```
git clone https://github.com/Spotifyd/spotifyd.git
```

## Install rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source /home/ubuntu/.cargo/env
```

## install additional required lib as required by spotifyd
```
sudo apt-get install libasound2-dev
sudo apt-get install libssl-dev
sudo apt-get install libpulse-dev
sudo apt-get install libdbus-1-dev
```

## Build spotifyd
```
cd spotifyd
cargo build --release
```

## enable audio and select to play over HDMI or audio jack
Add the following to /boot/firmware/usercfg.txt (here is the link: https://www.raspberrypi.org/documentation/configuration/config-txt/)

```
hdmi_drive=2
dtparam=audio=on
#hdmi_force_edid_audio=1 // enable if you want to play audio over hdmi
hdmi_ignore_edid_audio=1 // enable if you want to play audio over audio jack
```


## install ALSA, MPlayer and PulseAudio
```
sudo apt-get install --reinstall alsa-base pulseaudio
```

## setup spotify config file
$HOME/.config/spotifyd/spotifyd.conf

This is most imporant part and took most of the time. The not so easy part for me was to figure out the device and control settings.  You must get list of devices by 
using commands
### aplay -l
### aplay -L
But you need to install `alsamixer`.  This is a graphic program and the key F6 help retrive the name for me.  If there is a better way please share.
Here are my contents of spotifyd.conf file
```
[global]

backend = alsa
device = hw:0
control = hw:0 
mixer = PCM
volume_controller = alsa  # use softvol for macOS
device_name = MySweetPI
bitrate = 160
cache_path = cache_directory
no_audio_cache = true
volume_normalisation = true
normalisation_pregain = -10
zeroconf_port = 4070
device_type = computer
```

## Run and test 
```
spotifyd/target/release/spotifyd --no-daemon -d MySweetPi --zeroconf-port 4070
```

