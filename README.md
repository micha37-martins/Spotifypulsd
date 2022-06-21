# Spotifypulsd :speaker::musical_note: :sunglasses:
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
### Setup pulseaudio socket
Add sock for spotifyd to: 

`~/.config/pulse/default.pa`

load-module module-native-protocol-unix auth-group=audio socket=/tmp/pa_containers.socket

##### Example `spotifyd.conf`
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

### Modify path in .env file

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
Alternative to create socket
If you do not want to  or cannot set up the socket in pulseaudio config you can
use the following command to create a temporary socket:

`$ pactl load-module module-native-protocol-unix socket=/tmp/pa_containers.socket`

## Credits
Thanks to the authors and contributors of https://github.com/Spotifyd/spotifyd
and https://github.com/GnaphronG/docker-spotifyd

## Additional reading
https://github.com/mviereck/x11docker/wiki/Container-sound:-ALSA-or-Pulseaudio
