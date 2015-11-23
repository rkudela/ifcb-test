## Bugs, features, and updating

The IFCB dashboard is under active development and so bug fixes and new features are available.

The dashboard is beta quality code; it may have bugs that are not yet fixed. If you think you've found a bug, check the release notes to see if it's been fixed, and you may be able to resolve your issue by updating your dashboard code. If you find a bug that we don't know about, please report it using the issues tab on this repository.

When a new release is available, we will post a notice to the [IFCB users Google Group](https://groups.google.com/forum/?hl=en#!forum/ifcb-user-group).

## How to update code

Once you've decided to update your dashboard code, the process is easy. Open the Git Shell and change directories to your `ifcb-dashboard` directory:

```
cd ifcb-dashboard
```

Now run the following commands. After the last command you will be logged in to your virtual machine:

```
git pull
cd oii
git pull
vagrant ssh
```

At the vagrant command-line, run the following command to restart your web server:

```
sudo service apache2 restart
```

In your browser, make sure to shift-reload (or use whatever means your browser provides) when you first re-visit the dashboard, to make sure that there is no left-over cached code running in your browser.

## Stable releases versus latest code

In the future we will be providing specific instructions to install specific versions. In the meantime when you update you will get the very latest changes and updates, and those may contain bugs. If you are not experiencing problems with your dashboard, you should not feel that you need to update frequently. When there is a major change that requires updating, we will announce it on the [IFCB users Google Group](https://groups.google.com/forum/?hl=en#!forum/ifcb-user-group).