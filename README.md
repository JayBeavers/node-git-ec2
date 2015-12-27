# Hosting a NodeJS app on AWS EC2

This repository contains a set of scripts for hosting a NodeJS web app on AWS EC2
with deployment via Git.  This set of scripts is based on [Nathan Bridgewater's
blog post](http://iws.io/hosting-a-nodejs-express-application-on-amazon-web-services-ec2/),
updated to use EC2's user-data deployment scripts.  These scripts are written for
Ubuntu 14.04 LTS.

These scripts apply security settings for Ubuntu 14.04, based principally off the
advice from advice from [Matt Brock](http://blog.mattbrock.co.uk/hardening-the-security-on-ubuntu-server-14-04/)
and [Bryan Kennedy](http://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers).

The script uses Upstart to auto-start the NodeJS app upon boot and to restart the
app upon failure.  The app runs under permissions of the default login account,
ubuntu, not under root permissions.

The scripts assume that the app is started via `npm start`, which is either
specified in package.json or runs server.js from the root of the app repository
if not specified.

## Installation steps

* Add the following as user-data to the instance launch when creating a new EC2 instance
```
#include
https://raw.githubusercontent.com/JayBeavers/node-git-ec2/master/user-data
```
Note: be sure to add a security group that exposes ports 80 & 443, the default security group exposes only port 22.

* Wait for the website to finish initializing, including the post-setup scripts.
You can log in via ssh to watch the completion of the scripts via:
```
tail -f /var/log/cloud-init-output.log
```

* Upload the node app via:
```
git remote add aws ubuntu@your-server-host:app.git
git push aws master
```

* Verify the app is running on the server using ssh:
```
initctl status nodeapp
sudo cat /var/log/upstart/nodeapp.log
```
