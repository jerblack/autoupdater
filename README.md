# autoupdater
Autoupdater uses systemd to automatically run apt-get update, upgrade, autoremove, and autoclean to update Ubuntu/Debian-based systems on a schedule. 

The provided deb package will install 3 files on your system

- /usr/bin/autoupdater  
This is the script that the service runs to execute the apt-get commands.
This script takes the following switches, which are passed to it by the service:
```
 -a automatically reboot if needed (does not reboot by default)
 -r when to reboot if needed (now, 3:00 (3am on 24 hr clock), 18:00 (6pm on 24 hr clock), +2h)
 -m do not run autoremove (runs by default)
 -c do not run clean (runs by default)
```
- /lib/systemd/system/autoupdater.service  
This service file does not execute on it's own, but is instead executed by the timer file.
You can modify this service file to change the options that are passed to the script.
When this service is started, it runs /usr/bin/autoupdater with the specified switches and then exits on completion.

- /lib/systemd/system/autoupdater.timer  
The timer file is responsible for starting the service on a schedule. There are two options you can change here. The values shown are the current defaults.  
```
# Time to wait after booting before we run first time
OnBootSec=10min
# Time between running each consecutive time
OnUnitActiveSec=12h
```

If you make changes to any of these files, you will need to run this afterwards:

    sudo systemctl daemon-reload


### Installation
The simplest way to install is using the provided deb package:
```bash
sudo dpkg -i autoupdater.deb
```
To uninstall the service:
```bash
sudo dpkg -P autoupdater
```


### Logging
Systemd logs to the built in journal by default, and all writes to stdout are visible in the journal.  
To view the logs for this service:
```bash
journalctl -u autoupdater.service
```

You can also view the current state of the service by running:
```bash
systemctl autoupdater status
```

