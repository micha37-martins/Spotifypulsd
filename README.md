# Spotifypulsd
This is a fork of [docker-spotifyd](https://github.com/GnaphronG/docker-spotifyd).

It helps building a docker container of spotifyd using pulseaudio as backend.
Additionally this forks aims for giving a guideline of how to set up the
link between spotifyd running in a container and the host providing the audio
output hardware.

I set this up with the goal of adding the possibility to use Spotify on a small
host that has docker set up and running alredy. The tricky part was the connection
of spotifyd and pulseaudio. To make it reproducable I will describe the setup here.

# What is Spotifyd
An open source Spotify client running as a UNIX daemon. Spotifyd streams music
just like the official client, but is more lightweight and supports more
platforms. Spotifyd also supports the Spotify Connect protocol which makes it
show up as a device that can be controlled from the official clients.

Spotifyd requires a Spotify Premium account.

# Common Issues

* Spotifyd will not work without Spotify Premium
* The device name cannot contain spaces

# Credits
Thanks to the authors and contributors of https://github.com/Spotifyd/spotifyd
and https://github.com/GnaphronG/docker-spotifyd
