<div align="center">
	<img width="256" src="media/logo.svg">
</div>

# dockerpi_revised

This is a 2026 update to Luke Childs' dockerpi for Ubuntu 24.04.3 LTS.

## Changes
* Updated the dependencies.
* Added pixman static library building from source as the standard Ubuntu library would not work.
* Disabled gio within qemu as libmount would not correctly statically link.
* Updated temporary build locations per meson requirements.
* Commented out image resizing within entrypoint.sh due to a math error.

## TODO
* Track down the math error in entrypoint.sh.
* Newer versions of QEMU require libslirp-dev.  Need to pull this from source for static library building.
* Need to test if passing in a external raspbian image works.
* Need to test newer raspbian images.
* Need to test if filesystem mounting works.

## Usage

```
01_build_no_cache.sh

```

Build the docker image with output name dockepi_image

```

05_run_me.sh

```

Instantiate the dockerpi_image.

* By default all filesystem changes will be lost on shutdown. You can persist filesystem changes between reboots by mounting the `/sdcard` volume on your host:

```
docker run -it -v $HOME/.dockerpi:/sdcard dockerpi_image
```

If you have a specific image you want to mount you can mount it at `/sdcard/filesystem.img`:

```
docker run -it -v /2019-09-26-raspbian-buster-lite.img:/sdcard/filesystem.img dockerpi_image

```

## Which machines are supported?

By default a Raspberry Pi 1 is virtualised, however experimental support has been added for Pi 2 and Pi 3 machines.

You can specify a machine by passing the name as a CLI argument:

```
docker run -it lukechilds/dockerpi pi1
docker run -it lukechilds/dockerpi pi2
docker run -it lukechilds/dockerpi pi3
```

> **Note:** In the Pi 2 and Pi 3 machines, QEMU hangs once the machines are powered down requiring you to `docker kill` the container. See [#4](https://github.com/lukechilds/dockerpi/pull/4) for details.


## Wait, what?

A full ARM environment is created by using Docker to bootstrap a QEMU virtual machine. The Docker QEMU process virtualises a machine with a single core ARM11 CPU and 256MB RAM, just like the Raspberry Pi. The official Raspbian image is mounted and booted along with a modified QEMU compatible kernel.

You'll see the entire boot process logged to your TTY until you're prompted to log in with the username/password pi/raspberry.

```
pi@raspberrypi:~$ uname -a
Linux raspberrypi 4.19.50+ #1 Tue Nov 26 01:49:16 CET 2019 armv6l GNU/Linux
pi@raspberrypi:~$ cat /etc/os-release | head -n 1
PRETTY_NAME="Raspbian GNU/Linux 10 (buster)"
pi@raspberrypi:~$ cat /proc/cpuinfo
processor       : 0
model name      : ARMv6-compatible processor rev 7 (v6l)
BogoMIPS        : 798.31
Features        : half thumb fastmult vfp edsp java tls
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x0
CPU part        : 0xb76
CPU revision    : 7

Hardware        : ARM-Versatile (Device Tree Support)
Revision        : 0000
Serial          : 0000000000000000
pi@raspberrypi:~$ free -h
              total        used        free      shared  buff/cache   available
Mem:          246Mi        20Mi       181Mi       1.0Mi        44Mi       179Mi
Swap:          99Mi          0B        99Mi
```

## Build

Build this image yourself by checking out this repo, `cd` ing into it and running:

```
docker build -t lukechilds/dockerpi .
```

Build the VM only image with:

```
docker build -t lukechilds/dockerpi:vm --target dockerpi-vm .
```

## Credit

Thanks to Luke Childs for his work!

Thanks to [@dhruvvyas90](https://github.com/dhruvvyas90) for his [dhruvvyas90/qemu-rpi-kernel](https://github.com/dhruvvyas90/qemu-rpi-kernel) repo.

## License

MIT © Luke Childs
