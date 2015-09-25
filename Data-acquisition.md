The IFCB dashboard can copy data from your instrument to your specified data directories (see [[Installation]] for details about how to set up time series and data directories). You can configure the dashboard to periodically check for new data on your IFCB and copy it to the appropriate location for your time series.

> Note: the IFCB dashboard will never delete data from your IFCB. To free up space on your IFCB's hard drive, you will need log into your IFCB and manually delete data that you have already copied off of it.

### Set up access to your IFCB's data directory

To accomplish acquisition, your IFCB's data share first needs to be mounted on your dashboard server (under Windows, this is the virtual machine you set up as described in [[Installation]]).

The procedure for mounting an IFCB data share on the virtual machine is the same as the procedure for mounting your data directory (as described in [[Installation]]). Create a mount point directory on your virtual machine and add an entry to `/etc/fstab` with the share path and any authentication information.

For example if you want to mount your instrument at `/mnt/live`, run this command:

```
sudo mkdir /mnt/live
```

Continuing this example, if your IFCB has the IP address 192.168.1.5 and the username and password to connect to it is `ifcbuser`/`phyto`, enter the following command:

```
echo "//192.168.1.5/data /mnt/live cifs username=ifcbuser,password=phyto 0 0" \
| sudo tee -a /etc/fstab
```

Now mount the data directory:

```
sudo mount /mnt/live
```

And check to make sure that it worked by running the command below, which should show you a list of data files (if your IFCB has collected any data):

```
ls /mnt/live
```

If this does not work, see [[Troubleshooting]] to debug your share and mount point.

### Configure the dashboard for your instrument

In the dashboard, open the administration interface (as described in [[Installation]]), navigate to "Instruments", and click on "Add New Instrument". Give it a short name with no spaces or unusual characters, and enter the pathname of the IFCB's data directory mount point that you set up above. Finally, select the time series that you want to add your IFCB's data to, and click "Save".

![add_instrument](https://cloud.githubusercontent.com/assets/2365298/10107610/3c9a8db2-638a-11e5-9397-5b876472c98f.png)

Now, test the acquisition process using the command line on your virtual machine. For example, if you called your instrument "myifcb", run the following command:

```
curl http://localhost:9270/workflow/api/v1/wakeup/ifcb:acq:myifcb > /dev/null
```

If you return to your dashboard, you should see data from the instrument in your time series. If you don't see it immediately, wait a few seconds and reload the dashboard in your browser. If you do this repeatedly and data does not show up, there may be a variety of problems. For example, your data share (not the instrument's data directory, but the one associated with your time series) may not allow you to write because of permissions.

If this works, you are ready to set up a schedule for automatic acquisition. Decide on how often to check for new data. It is generally not necessary to check more often than once every 20 minutes. To set up this schedule, run the following command on your virtual machine (remember, this is an example, use whatever you called your instrument in place of 'myifcb'):

```
{ crontab -l; echo '*/20 * * * * /usr/bin/curl http://localhost:9270/workflow/api/v1/wakeup/ifcb:acq:myifcb > /dev/null'; } | crontab -
```

To verify this is working, make sure your IFCB is creating new files, wait at least 20 minutes, reload your dashboard, and confirm that newly-collected data is showing up.