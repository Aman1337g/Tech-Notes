# Things to do after installing Ubuntu 23.10

I go with the minimal installation of ubuntu with thirty party hardware option checked.

## Contents

1. [Removing snap completely](#1-removing-snap-completely)
2. [Getting rid of reporting](#2-uninstalling-reporting-services#)
3. [Uninstalling bloatware](#3-uninstalling-applications-preinstalled)
4. [Enabling flatpak support](#4-enabling-flatpak-support)
5. [Installing Applications](#5-installing-applications)
6. [Disabling unnecessary services](#6-disabling-unecessary-services-to-me)
7. [Firewall Setup](#7-firewall-setup)
8. [Most Imp. Theming](#8-theming-your-awesome-system)

<br>

### 1. Removing snap completely

- first list out the snap packages installed using 
```bash
snap list
```
then remove according to the **version**
```bash
sudo snap remove --purge firefox
sudo snap remove --purge snap-store
sudo snap remove --purge gnome-42-2204
sudo snap remove --purge gtk-common-themes
sudo snap remove --purge firmware-updater
sudo snap remove --purge snapd-desktop-integration
sudo snap remove --purge bare
sudo snap remove --purge core22
sudo snap remove --purge snapd
```

- the `sudo apt update` command can again brings back snap, to that
```bash
sudo -H nano /etc/apt/preferences.d/nosnap.pref
```

- paste the following lines and save the file
```
Package: snapd
Pin: release a=*
Pin-Priority: -10
```
<br>

### 2. Uninstalling reporting services

```bash
sudo ubuntu-report -f send no
```
```bash
sudo apt purge whoopsie apport ubuntu-report
```
<br>

### 3. Uninstalling preinstalled applications

```bash
sudo apt purge eog gnome-clocks gnome-logs gnome-power-manager
```
<br>

## 4. Enabling flatpak support

Setup - https://github.com/Aman1337g/Tech-Notes/blob/main/Linux/Ubuntu/Flatpak.md#installation

Applications that i use using flatpak - https://flathub.org
```
brave browser
zoom 
telegram
shotwell
libreoffice
```
<br>

## 5. Installing applications

```bash 
sudo apt install gnome-tweaks vlc build-essential ubuntu-restricted-extras
```

- enabling minimize on click (Dock)
```bash
gsettings set org.gnome.shell.extensions.dash-to-dock click-action 'minimize'
```

- installing free download manager from https://www.freedownloadmanager.org/ as .deb
- go to download folder `sudo dpkg -i ./fdm.deb` then `sudo apt install -f` then again `sudo dpkg -i ./fdm.deb`

- installing vscode - https://code.visualstudio.com/docs/setup/linux
- installing java - https://www.freecodecamp.org/news/how-to-install-java-in-ubuntu/
- installing node using nvm - https://github.com/nvm-sh/nvm
- installing nextdns using - https://github.com/nextdns/nextdns/wiki/Debian-Based-Distribution
- installing tlp - https://linrunner.de/tlp/installation/ubuntu.html
- installing git - https://git-scm.com/download/linux

For git cheatsheet go [here](https://github.com/aman1337g/git-cheatsheet) and setting up multiple accounts go [here](https://github.com/aman1337g/git-cheatsheet#configuring-git-for-a-second-github-account). For increasing productivity refer to these git [aliases](https://github.com/Aman1337g/Git-Cheatsheet#list-of-some-aliases-i-use-myself--edit-using-git-config---global---edit) 

<br>

## 6. Disabling unecessary services (to me)

To see which service takes more time to system boot

```bash
systemd-analyze blame
```

- stoping useless evolution services from running at startup (well i don't use them)
```bash
sudo chmod -x /usr/libexec/evolution-addressbook-factory
sudo chmod -x /usr/libexec/evolution-calendar-factory
sudo chmod -x /usr/libexec/evolution-data-server/evolution-alarm-notify
sudo chmod -x /usr/libexec/evolution-source-registry
```

- **Print services**
```bash 
sudo systemctl disable cups
```

- **Accounts Service**
```bash
sudo systemctl disable accounts-daemon
```

- **Logging service**
```bash
sudo systemctl disable --now rsyslog.service
```

- **Openvpn service**
```bash
sudo systemctl disable --now openvpn.service
```

### Disabling docker services
```bash
sudo systemctl disable now docker
sudo systemctl disable now docker.socket
sudo systemctl disable containerd.service
```

### Removing all docker containers and images
```bash
# Stop and remove all containers
sudo docker stop $(sudo docker ps -a -q)
sudo docker rm $(sudo docker ps -a -q)

# Remove all images
sudo docker rmi $(sudo docker images -q)
```
<br>

## 7. Firewall setup

```bash
sudo ufw limit 22/tcp  
sudo ufw allow 80/tcp  
sudo ufw allow 443/tcp  
sudo ufw default deny incoming  
sudo ufw default allow outgoing
sudo ufw enable
```
<br>

## 8. Theming your awesome system

- nord theme - https://github.com/EliverLara/Nordic
- nordzy curzors - https://github.com/alvatip/Nordzy-cursors
- papirus icons - https://github.com/PapirusDevelopmentTeam/papirus-icon-theme

For themes to be applied on flatpaks as well, refer to [here](https://itsfoss.com/flatpak-app-apply-theme/)