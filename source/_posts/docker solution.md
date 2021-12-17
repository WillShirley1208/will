---
title: docker solution
date: 2019-08-16 14:34:30
tags: snippet
categories: docker
---



## Manage Docker as a non-root user

1. Create the `docker` group.

   ```
   $ sudo groupadd docker
   ```

2. Add your user to the `docker` group.

   ```
   $ sudo usermod -aG docker $USER
   ```

3. Log out and log back in so that your group membership is re-evaluated.

   If testing on a virtual machine, it may be necessary to restart the virtual machine for changes to take effect.

   On a desktop Linux environment such as X Windows, log out of your session completely and then log back in.

   On Linux, you can also run the following command to activate the changes to groups:

   ```
   $ newgrp docker 
   ```

4. Verify that you can run `docker` commands without `sudo`.

   ```
   $ docker run hello-world
   ```



#### Failed to load listeners: no sockets found via socket activation: make sure the service was started by systemd

```
edit the docker’s systemd unit file and change the ExecStart:
sudo systemctl edit docker.service

edite The value ExecStart, change fd:// to unix://:
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H unix://
```

#### Error creating default "bridge" network: cannot create network (docker0): conflicts with network (docker0): networks have same bridge name

```shell
sudo rm -rf /var/lib/docker/network
sudo systemctl start docker
```

