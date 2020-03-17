# The following are other major advantages to using containers:
```
Low hardware footprint
Environment isolation
Quick deployment
Multiple environment deployment
Reusability
```

# From VServer, the idea of isolated processes further evolved and became formalized around the following features of the Linux kernel:
```
Namespaces
Control groups (cgroups)
Seccomp
SELinux
```

# Kubernetes Features
```
Service discovery and load balancing
Horizontal scaling
Self-healing
Automated rollout
Secrets and configuration management
Operators
```

# OpenShift Features
```
Integrated developer workflow
Routes
Metrics and logging
Unified UI
```

# unix signal
```
Term Terminate the process.
Core Terminate the process and generate a core dump.
Ign  Signal is ignored.
Stop Stop the process.

Any Unix signal can be sent to the main process. Podman accepts either the signal name and number. The following table shows several useful signals:
Signal	   Value	  Default Action	Comment
SIGHUP	   1	        Term	        Hangup detected on controlling terminal or death of controlling process
SIGINT	   2	        Term	        Interrupt from keyboard
SIGQUIT	   3	        Core	        Quit from keyboard
SIGILL	   4	        Core	        Illegal Instruction
SIGABRT	   6	        Core	        Abort signal from abort(3)
SIGFPE	   8	        Core	        Floating point exception
SIGKILL	   9	        Term	        Kill signal
SIGSEGV	   11	        Core	        Invalid memory reference
SIGPIPE	   13	        Term	        Broken pipe: write to pipe with no readers
SIGALRM	   14	        Term	        Timer signal from alarm(2)
SIGTERM	   15	        Term	        Termination signal
SIGUSR1	   30,10,16	  Term	        User-defined signal 1
SIGUSR2	   31,12,17	  Term	        User-defined signal 2
SIGCHLD	   20,17,18	  Ign	          Child stopped or terminated
SIGCONT	   19,18,25	  Cont	        Continue if stopped
SIGSTOP	   17,19,23	  Stop	        Stop process
SIGTSTP	   18,20,24	  Stop	        Stop typed at tty
SIGTTIN	   21,21,26	  Stop	        tty input for background process
SIGTTOU	   22,22,27	  Stop	        tty output for background process
```

# podman
```
> podman search rhel
> podman pull rhel
> podman images

sets the entry point for this container to the echo "Hello world" command.
> podman run ubi7/ubi:7.7 echo "Hello world!"

To start a container image as a background process
> podman run -d rhscl/httpd-24-rhel7:2.4-36.8

retrieve the container's internal IP address from container metadata
> podman inspect -l -f "{{.NetworkSettings.IPAddress}}"

lists metadata about a running or stopped container.
> podman inspect my-httpd-container

> podman stop my-httpd-container
> podman kill my-httpd-container (SIGKILL signal default)
> podman kill -s SIGKILL my-httpd-container
> podman restart my-httpd-container
> podman rm my-httpd-container
> podman rm -a
> podman stop -a

-t is equivalent to --tty, meaning a pseudo-tty (pseudo-terminal) is to be allocated for the container.
-i is the same as --interactive. When used, standard input is kept open into the container.
-d, or its long form --detach, means the container runs in the background (detached). Podman then prints the container id.
> podman run -it ubi7/ubi:7.7 /bin/bash

inject environment variables into containers at startup by adding the -e flag
> podman run -e GREET=Hello -e NAME=RedHat rhel7:7.5 printenv GREET NAME
> podman run --name mysql-custom -e MYSQL_USER=redhat -e MYSQL_PASSWORD=r3dh4t -d rhmap47/mysql:5.5

Use the -p 8080:80 option with sudo podman run command to forward the port.
> podman run -d -p 8080:80 --name httpd-basic redhattraining/httpd-parent:2.4

podman exec command starts an additional process inside an already running container
> podman exec 7ed6e671a600 cat /etc/hostname  (container ID)
> podman exec my-httpd-container cat /etc/hostname  (container name)
> podman exec -l cat /etc/hostname  (last container used in any command -l)
```
