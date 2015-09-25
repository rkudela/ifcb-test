The IFCB dashboard can copy data from your instrument to your specified data directories (see [[Installation]] for details about how to set up time series and data directories). You can configure the dashboard to periodically check for new data on your IFCB and copy it to the appropriate location for your time series.

> Note: the IFCB dashboard will never delete data from your IFCB. To free up space on your IFCB's hard drive, you will need to manually delete older data.

# Set up access to your IFCB data directory

To accomplish acquisition, your IFCB's data share first needs to be mounted on your dashboard server (under Windows, this is the virtual machine you set up as described in [[Installation]]).

The procedure for mounting an IFCB data share on the virtual machine is the same as the procedure for mounting your data directory (as described in [[Installation]]). Create a mount point directory on your virtual machine and add an entry to `/etc/fstab` with the share path and any authentication information.

For example if you want to mount your instrument at `/mnt/live`, run this command:

```
sudo mkdir /mnt/live
```

Continuing this example, if your IFCB has the IP address 192.168.1.5 and the username and password to connect to it is `ifcbuser`/`phyto`, enter the following commands:

