CSE 30342 Docker
=============

The Docker container-based virtualization service lets you run a
minimal CSE 30342 environment, including Linux, on a macOS or Windows
computer, without the overhead of a full virtual machine like VMware
Workstation, VMware Fusion, or VirtualBox.

It should be possible to do *all* CSE 30342/20232 assignments in the CSE 30342
Docker container.

Advantages of Docker:

* Docker can start and stop containers incredibly quickly.
* Docker-based containers are small and occupy little space on your machine.
* With Docker, you can easily *edit* your code in your home environment, but
  *compile and run* it on a Linux host.

Disadvantages of Docker:

* Docker does not offer a graphical environment. You will need to run all CSE 30342/20232
  programs exclusively in the terminal.
* Docker technology is less user-friendly than virtual machines. You’ll have
  to type weird commands.

## Creating the CSE 30342 Docker container

1.  Download and install [Docker][].

2.  From this directory, run this command. It will take a while (up to 20
    minutes) and will use a few GB of disk space.

    ```shellsession
    $ ./cse30342-build-docker
    ```

    The command starts up a virtual Linux-based computer running inside your
    computer. It then installs a bunch of software useful for CSE 30342 on that
    environment, then takes a snapshot of the running environment. (The
    snapshot has a name, such as `cse30342:latest` or `cse30342:arm64`.) Once the
    snapshot is created, it’ll take just a second or so for Docker to restart
    it.

We may need to change the Docker image during the semester. If we do, you’ll
update your repository to get the latest Dockerfile, then re-run the
`./cse30342-build-docker` command from step 2. However, later runs should be
faster since they’ll take advantage of your previous work.

> `./cse30342-build-docker` is a wrapper around `docker build`. On x86-64 hosts, it runs
> `docker build -t cse30342:latest -f Dockerfile --platform linux/amd64`.

## Running the CSE 30342 Docker container by script

In the parent directory of this one (the cse30342-devenv repository root), you'll
find a file called `cse30342-run-docker`. This is a script that runs your CSE 30342
Docker container.

For example, here’s an example of running CSE 30342 Docker on a macOS host. At
first, `uname` (a program that prints the name of the currently running
operating system) reports `Darwin` (the name of the macOS kernel). But after
`./cse30342-run-docker` connects the terminal to a Linux container, `uname`
reports `Linux`. At the end of the example, `exit` quits the Docker
environment and returns the terminal to macOS.

```shellsession
$ uname
Darwin
$ uname -a
Darwin bashful.local 21.1.0 Darwin Kernel Version 21.1.0: Wed Oct 13 17:33:24 PDT 2021; root:xnu-8019.41.5~1/RELEASE_ARM64_T8101 arm64
$ ./cse30342-run-docker
cse30342-user@a47f05ea5085:~$ uname
Linux
cse30342-user@a47f05ea5085:~$ uname -a
Linux 4f789b721d16 5.10.47-linuxkit #1 SMP PREEMPT Sat Jul 3 21:50:16 UTC 2021 aarch64 aarch64 aarch64 GNU/Linux
cs300-user@a47f05ea5085:~$ exit
exit
$
```

A prompt like `cse30342-user@a47f05ea5085:~$` means that your terminal is
connected to the container. (The `a47f05ea5085` part is a unique identifier for this
running container.) You can execute any Linux commands you want. To escape from the
container, type Control-D or run the `exit` command.

The script assumes your Docker container is named `cse30342:latest`.


### Running CSE 30342 Docker by hand

If you don’t want to use the script, use a command like the following.

```shellsession
$ docker run -it --platform linux/amd64 -v ~/cse30342-f23-devenv/home:/home/cse30342-user cse30342:latest
```

Explanation:

* `docker run` tells Docker to start a new virtual machine.
* `-it` says Docker should run interactively (`-i`) using a terminal (`-t`).
* `--platform linux/amd64` says Docker should emulate an x86-64-based machine.
  It’s necessary to specify this if you have (for example) an Apple M1-based
  laptop and are working on assignments that require an x86-64 Intel machine.
* `-v LOCALDIR:LINUXDIR` says Docker should share a directory between your
  host and the Docker container. Here, I’ve asked for the host’s
  `~/cs300-s22-devenv/home` directory to be mapped inside the container
  onto the `/home/cs300-user` directory, which is the virtual machine
  user’s home directory.
* `cs300:latest` names the Docker image to run (namely, the one you built).

Here’s an example session:

```shellsession
$ docker run -it --platform linux/amd64 --rm -v ~/cs300-s22-devenv/home:/home/cs300-user cs300:latest
cs300-user@a15e6c4c8dbe:~$ ls
cs300-s22-projects
cs300-user@a15e6c4c8dbe:~$ echo "Hello, world"
Hello, world
cs300-user@a15e6c4c8dbe:~$ cs300-docker-version
1
cs300-user@a15e6c4c8dbe:~$ exit
exit
$
```

[Docker]: https://docker.com/

## Acknowledgements

This setup is a modified version of the setup used by
[Harvard's CS61](https://cs61.seas.harvard.edu/site/2021/) and reused
with permission.
