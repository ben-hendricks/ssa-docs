# MacMini Configuration

## System Settings

Edit the following ==System Settings== items: 

### Enable Automatic Restart

Using the terminal:

```sh title="Enable automatic restart (desktop)"
sudo systemsetup -setrestartpowerfailure on
```


```sh title="Enable automatic restart (laptop)"
sudo systemsetup -setrestartfreeze on 
```

or:

Energy Saver > Start up automatically after a power failure --> *ON*


### Enable Automatic Login

Users & Groups > Auto-login --> ON
Login Password > Automatically log in after a restart --> ON

### Open AnyDesk on Startup

General > Login Items --> Add AnyDesk


### Disable Sleep settings

Energy Saver > Put hard disks to sleep when possible --> OFF
Energy Saver > Wake for network access --> OFF

Lock Screen > Start Screen Saver when active --> Never
Lock Screen > Turn display off when inactive --> Never

Displays > Advanced > Prevent automatic sleeping when the display is off --> ON


### Disable software updates

General > Software Update > Automatic updates --> OFF


## Create Hydra Desktop Shortcut
First, create a script that a) activates the Python environment for Hydra, b) changes directory to the location from which Hydra is executed, and c) executes Hydra. The first line should include a shebang (`#!`) identifier with the location of bash on the Mac Mini. Save this to the Desktop named *Hydra_2023*.

??? example

	```sh title="Hydra_2023 Desktop Shortcut"
	#!/usr/bin/env bash

	source activate SSP3
	cd '/Users/janiewray/SoundSpace/SWAG/SWAG_FNIS-01_ICHF6155_2023/HYDRA'
	python Hydra_2023_v1p6.py
	```

??? note

	When executed via shortcut script, the Python environment is activated using `source activate`. If the same process is done through the Terminal directly, the required command is `conda activate`.



Give all required permissions (read, write, execute):

```sh
cd path/to/hydra_shortcut
chmod +xrw HYDRA_2023
``` 

The icon image for the Desktop shortcut can be customized by right-clicking on the icon, then selecting *Get Info*. Any desired .png or .ico graphic can now be dragged and dropped into the upper left corner to replace the default graphic.


## Install Auxiliary Programs

### Audacity

Audacity is a an audio viewing and editing application. It is free and available here:

```html
https://www.audacityteam.org/download/mac/
```

### GoogleDrive

GoogleDrive is the desktop application to the cloud storage service provided by Google. It helps transferring data seamlessly to and from machnines deployed in the field.

```html
https://www.google.com/intl/en_ca/drive/download/
```

### GoogleChrome

GoogleChrome is my preferred internet browser for field computers.

```html
https://www.google.com/chrome/
```

### AnyDesk

AnyDesk facilitates remote Desktop access to field computers. 

```htmp
https://anydesk.com/en/downloads/mac-os
```
### SublimeText

SublimeText is a free text editor that is used for code writing/editing.

```html
https://www.sublimetext.com/3
```