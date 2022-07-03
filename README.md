# Spotifypulsed :speaker::musical_note: :sunglasses:
This is a fork of [docker-spotifyd](https://github.com/GnaphronG/docker-spotifyd).

It helps building a docker container of spotifyd using pulseaudio as backend.
Additionally this forks aims for giving a guideline of how to set up the
link between spotifyd running in a container and the host that is responsible
for the audio output.

I set this up with the goal of adding the possibility to use Spotify on a small
host that has docker set up and running already. The tricky part was the connection
of spotifyd and pulseaudio. To make it reproducible I will describe the setup here.

## What is Spotifyd
An open source Spotify client running as a UNIX daemon. Spotifyd streams music
just like the official client, but is more lightweight and supports more
platforms. Spotifyd also supports the Spotify Connect protocol which makes it
show up as a device that can be controlled from the official clients.

Spotifyd requires a Spotify Premium account.

## Host setup
Your host need to have a working audio setup with a running pulseaudio server.
How to do this is depending on your Linux distribution and have to be explored
on your own. You can get a good overview of the Linux audio system by reading the
Arch wiki:
- https://wiki.archlinux.org/title/Sound_system
- https://wiki.archlinux.org/title/PulseAudio

### Setup pulseaudio socket
Add socket for spotifyd to: 

`~/.config/pulse/default.pa`

```
load-module module-native-protocol-unix auth-group=audio socket=/tmp/pa_containers.socket
```

> If you do not have the `default.pa` create it and add the following at the first line:
> 
>`.include /etc/pulse/default.pa`

### Spotifyd config
The config can be modified according to your needs as nothing is mandatory. The
following is an example you can use to get started:

`~/.config/spotifyd/spotifyd.conf`
```
[global]
username = "YOURUSERNAME"
password = "YOURPASSWORD"
backend = "pulseaudio"
no_audio_cache = true
bitrate = 160
initial_volume = "80"
volume_controller = "alsa"
volume_normalisation = false
device_type = "speaker"
```
> Detailed information to the configuration:  
> https://spotifyd.github.io/spotifyd/config/File.html

### Modify path in .env file
Replace YOURUSERNAME by your current username to point to your spotifyd.conf.

## Start container using docker-compose
```bash
$ MY_UID="$(id -u)" MY_GID="$(id -g)" docker-compose up
```

> You can avoid the MY_UID and MY_GID in the command by adding the UID and GID
> directly into the docker-compose.yml

## Common Issues
* Spotifyd will not work without Spotify Premium
* The device name cannot contain spaces

## Troubleshooting 
### Problems with pulseaudio socket
Alternative to create socket
If you do not want to  or cannot set up the socket in pulseaudio config you can
use the following command to create a temporary socket:

`pactl load-module module-native-protocol-unix socket=/tmp/pa_containers.socket`

> If you cannot create the socket in /tmp try to create it in a separate folder
> of your home directory. `/home/user/tmp/pa_containers.socket` for example.
> Remember to also change the paths in the docker-compose.yml

### Problems with permissions
If `aplay -l` returns:

`aplay: device_list:123: no soundcards found...`

Give the permission to access sound:
```
sudo adduser YOURUSERNAME audio
```

### Problems running docker-compose as a daemon (up -d)
"Could not start audio: Connection refused"
"Could not write audio: Not connected to PulseAudio"

I had this on a headless Ubuntu server and it turned out that after logging
out from my ssh session the container was not able to write to pulseaudio socket
anymore. The solution was found [here](https://unix.stackexchange.com/questions/490267/prevent-logoff-from-killing-tmux-session):
Run docker-compose in a systemd-run wrapper
```
systemd-run --scope --user docker-compose up -d
```


## Credits
Thanks to the authors and contributors of https://github.com/Spotifyd/spotifyd
and https://github.com/GnaphronG/docker-spotifyd for this awesome project!

## Additional reading
- https://github.com/mviereck/x11docker/wiki/Container-sound:-ALSA-or-Pulseaudio
- https://wiki.archlinux.org/title/Sound_system
- https://wiki.archlinux.org/title/PulseAudio
- https://joonas.fi/2020/12/audio-in-docker-containers-linux-audio-subsystems-spotifyd/
