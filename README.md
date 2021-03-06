# Setting up LXD Containers to Connect to Eachother and the Internet
Note: All of the following should be run as root

## Installation of LXD
Use one of the following commands to install LXD.

```sudo snap install lxd```

```sudo apt-get install lxd```

## Initial creation of LXD container from scratch
Run the lxd setup service using the following command.

```lxd init```

Then start the lxd process, and check the status to ensure it is running.

```systemctl start lxd```

```systemctl status lxd```

## Add the user to the lxd groups
Use the following commands to make sure that root (and/or the user if needed) are members of the lxd group. Restart the lxd process to apply changes.

```getent group lxd```

```sudo gpasswd -a root lxd```

```systemctl restart lxd```

Then add root and lxd groups (and/or the other groups if needed) to the permissions list for subuid and subgid. To both files add the following content:

```root:1000000:65536```

```lxd:1000000:65536```

Use the following commands to edit

```vim /etc/subuid```

```vim /etc/subgid```

## Create the container
Use the following command to create a lxc container using the ubuntu:18.04 image. The name of the container, in this case, is "hw4".

```lxc launch ubuntu:18.04 hw4```

## Helpful Lxd Commands
Here are a few helpful commands when working with lxd that are worth noting.

View lxc containers: ```lxc list```
View lxc images: ```lxc image list```

## Creating a New Profile
To add network connectivity, the content of the containers profile needs to be edited from what was used in assignment 3 to add network connectivity. An existing profile can be copied and then the information from the files "limited_network_a_backup.txt" or "limited_network_b_backup.txt" (currently they are both identical) can be added to the file.

```lxc profile copy limited limited_network_a```

```lxc profile copy limited limited_network_b```

```lxc profile edit limited_network_a```

```lxc profile edit limited_network_b```

After editing the profiles, one of them (currently they are identical so it shouldn't matter) should be assigned to the container that was created.

```lxc profile assign hw4 limited_network_a```

Then, restart the container so that it is using the correct profile.

```lxc restart hw4```

## Copying Files to the new container
At this point, any files to be added to the container, such as the executables needed for demonstrations, should be added. I have added them to the "/jailed/apps/" directory inside of the container rootfs located at "/var/lib/lxd/containers/hw4/rootfs/".

```cp connect_to_server_internet* /var/lib/lxd/containers/hw4a/rootfs/jailed/apps/```
```cp server_hw4a* /var/lib/lxd/containers/hw4a/rootfs/jailed/apps/```
```cp server_hw4a* /var/lib/lxd/containers/hw4b/rootfs/jailed/apps/```
```cp client_hw4b* /var/lib/lxd/containers/hw4a/rootfs/jailed/apps/```
```cp client_hw4b* /var/lib/lxd/containers/hw4b/rootfs/jailed/apps/```

## Remove Unwanted Binaries
Remove any binaries that you do not want the container to have access to - such as chmod, dd, lsblk, among others. Binaries should be removed from "/var/lib/lxd/containers/hw4/rootfs/bin".

## Export Container Image
The container is now in the condition that we want, we can export an image of it so that we can run it elsewhere and at another time in the future. Use the following command sequence to stop the container, publish, and export. After running these three commands you will have a hw4_image.tar.gz file output to your working directory.

```lxc stop hw4```

```lxc publish hw4```

```lxc image export <shasum> hw4_image```

Then, cleanup your existing container and image. Use the ```lxc image list``` to see a list of current images.

```lxc delete hw4```

```lxc image delete <whatever images are there>```

## Importing Container Image
Use the following commands to import a conatainer image. Giving it an alias makes it easier to reference versus looking at a hash.

```lxc image import hw4_image.tar.gz --alias hw4_image```

Then, a new container using this image can be launched. In this case, the new container is named "hw4".

```lxc launch hw4_image hw4```

## Executing Bash in Container with Restricted Namespaces
To run the new container with restricted namespaces for fork, pid, mount, and mount-proc, use the following command.

```lxc exec hw4b -- unshare --mount --fork --pid --mount-proc /bin/bash```



## Resources
https://linuxcontainers.org/lxc/getting-started/
https://bobcares.com/blog/how-to-delete-lxc-container/
http://www.fernandoalmeida.net/blog/how-to-limit-cpu-and-memory-usage-with-cgroups-on-debian-ubuntu/
https://linuxconfig.org/how-to-create-systemd-service-unit-in-linux
https://www.paranoids.at/cgroup-ubuntu-18-04-howto/
https://linuxcontainers.org/lxc/manpages/man1/lxc-start.1.html
https://linuxize.com/post/how-to-list-users-in-linux/
https://www.cyberciti.biz/faq/how-to-create-unprivileged-linux-containers-on-ubuntu-linux/
https://linuxcontainers.org/lxc/getting-started/
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/resource_management_guide/sec-obtaining_information_about_control_groups
https://wiki.debian.org/LXC/CGroupV2
https://forum.mxlinux.org/viewtopic.php?t=49990
https://lxd.readthedocs.io/en/stable-3.0/containers/
https://www.youtube.com/watch?v=ZEL1BSoUhSI&t=234s&ab_channel=JustmeandOpensource
https://linuxcontainers.org/lxc/manpages/man1/lxc-execute.1.html
https://serverfault.com/questions/759170/copy-lxd-containers-between-hosts
https://manpages.ubuntu.com/manpages/artful/man1/lxc.image.1.html
https://linuxcontainers.org/lxd/docs/master/image-handling
https://www.oreilly.com/library/view/container-security/9781492056690/ch04.html
https://blog.simos.info/how-to-make-your-lxd-containers-get-ip-addresses-from-your-lan-using-a-bridge/

