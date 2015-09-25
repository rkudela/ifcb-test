# Cannot mount data directories or instrument data share

If you followed the instructions to mount data directories or the instrument data share on your virtual machine, but are still not seeing the files, there could be several reasons:

1. The share is not up
1. Your `/etc/fstab` configuration is incorrect

To test if the share is up, try connecting to it from another Windows machine. If you can do that, the share is up and should be able to be mounted on the virtual machine. If not, you will need to work on bringing the share up and making sure it is accessible to your Windows host.

Assuming the share is up, the next step you can try is to try to temporarily connect to your machine (either the host with your data directories, or your instrument) and list the shares.