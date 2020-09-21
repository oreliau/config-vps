# VPS CONFIG

## Setup
- Windows 10 2004
- A VPS buy in debian 10 (virtual private server)
- VSC (CTRL+SHIFT+V to preview markdown)
- WSL (windows live server) config in Debian [Awesome Repo](https://github.com/MrStanDu33/ProcessusWebServ) [Awesome Video in french](https://www.youtube.com/watch?v=ZJsMxPchNuY&t=582s)
    - [X] Basics
    - [X] ZSH
    - [X] GitHub SSH Key
    - [X] GitHub GPG Key
    - [X] Pinentry for WSL
    - [X] Ease of use
    - [X] Setup terminal colors
    - [X] autoload for Apache2
    - [X] autoload for MySQL
    - [X] Powerline Go
    - [X] Archey4

## Connection to your VPS

### Connection in SSH with WSL (Or Linux terminal)

Simply put you IPV4 VPS (or VPS name) and the name of user that your host give you by email:

```bash
$ ssh $USER_GIVEN_BY_YOUR_HOST@$YOUR_IPV4_VPS
```

And set the password send by mail.

### Connection with PuttY

1. Download [PuttY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

2. Just fill the main page "Host Name (or IP adress)" and "Port". Port is usally 22.

![Image Putty](https://www.geek17.com/sites/default/files/pictures/debian/ovh_putty_connect.jpg)

3. And validate security alert

## Security

### Update system

You need to get the last packages to get the best security.

```bash
$ apt-get update
$ apt-get upgrade
```

### Create a new user

Here you have to change $USER with a pseudo:

```bash
$ adduser $USER
```

After just put `ENTER` and `y` to validate user:

```bash
Adding user $USER ...
Adding new group $USER (1001) ...
Adding new user $USER (1001) with group $USER ...
Creating home directory '/home/$USER' ...
Copying files from '/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for rb
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] y
```

Now we need to get permission as root to this user:

```bash
$ adduser $USER sudo
```

And you have this:

```bash
Adding user 'rb' to group 'sudo' ...
Adding user rb to group sudo
Done.
```

### Change root password

Set an hard password, all the security of your vps will depend of this:

```bash
$ passwd root
```

### Secure your SSH sessions
To cut connexion SSH by root

```bash
$ nano /etc/ssh/sshd_config
```

and uncomment `PermitRootLogin` and set `no`

```bash
$ PermitRootLogin no
```

To save and close file do `Ctrl`+`X` after `Y` to validate save and `ENTER` to validate path.

Now we need to restart service, to get modications we done:

```bash
$ service sshd restart
```

### Fail2ban
Block ip if max try is failed

```bash
$ apt-get install fail2ban
$ nano /etc/fail2ban/jail.conf
```

Find:
    - enabled, uncomment it and set `on`
    - bantime = as you want
    - maxretry = as you want

### More Security

We will change port and allow ssh connection only with your new user:

```bash
$ nano /etc/ssh/sshd_config
```

Uncomment `Port 22` and modify `22` with another number.
/!\ Don't choose a port already to check it type in bash `netstat -l|grep $PORT_NUMBER`

```bash
Port 44422
```

Now restrict users wich can connect by ssh

```bash
AllowUsers $USER
```

And `restart`

```bash
$ service sshd restart
```

### Connection to VPS

Don't forget to set:
- The new `port` if you use PuttY
- For WSL (or Linux terminal) add `-p $NewPort`:

Now u can be connected to your vps with this link
```bash
$ ssh $NEW_USER@$YOUR_IPV4_VPS -p $NewPort
```

## Configuration of environnement (if you use wsl or linux)

* Conect to user root:

```bash
$ sudo su
```
And set your password

* Install the basic packages:

```bash
$ apt-get update -y && \
  apt-get upgrade -y && \
  apt-get dist-upgrade -y && \
  apt-get install -y wget && \
  wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
  echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php7.4.list && \
  apt-get update -y && \
  apt-get install -y apache2 apache2-doc apache2-utils curl mariadb-server sendmail python3-pip git unzip emacs php7.4 php7.4-cli php7.4-fpm php7.4-json php7.4-pdo php7.4-mysql php7.4-zip php7.4-gd  php7.4-mbstring php7.4-curl php7.4-xml php7.4-bcmath php7.4-json libapache2-mod-php7.4 php-cli php-mbstring nodejs npm && \
  curl -sS https://getcomposer.org/installer -o composer-setup.php && \
  php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
  a2enmod rewrite && \
  sendmailconfig && \
  service apache2 restart
```

* Install ZSH:

In bash:

```bash
$ apt-get install -y zsh
$ chsh
$ /bin/zsh
$ cd ~
$ curl -L git.io/antigen > antigen.zsh
$ emacs ~/.zshrc
```

* Setup terminal colors:

In bash:

```bash
$ emacs ~/.zshrc
```

Copy in file.

```bash
##### Start Terminal Color enabling #####
eval "`dircolors`"
force_color_prompt=yes
alias ls='ls --color=auto'
##### End Terminal Color enabling #####
```

* Setup Powerline Go:

```bash
$ emacs ~/.zshrc
```

Add in file before line `antigen apply`

```bash
antigen bundle Lokaltog/powerline powerline/bindings/zsh
```

Save it, close it and write

```bash
pip3 install powerline-status
```

* Setup Archey4

```bash
$ pip3 install archey4
$ emacs /etc/archey4/config.json
```

Set inside

```bash
{
  "allow_overriding": true,
  "suppress_warnings": true,
  "entries": {
    "User": true,
    "Hostname": true,
    "Model": false,
    "Distro": true,
    "Kernel": true,
    "Uptime": true,
    "WindowManager": false,
    "DesktopEnvironment": false,
    "Shell": true,
    "Terminal": true,
    "Packages": true,
    "Temperature": false,
    "CPU": true,
    "GPU": false,
    "RAM": true,
    "Disk": true,
    "LAN_IP": true,
    "WAN_IP": true
  },
  "colors_palette": {
    "use_unicode": true
  },
  "default_strings": {
    "no_address": "No Address",
    "not_detected": "Not detected",
    "virtual_environment": "Virtual Environment",
    "bare_metal_environment": "Bare-metal Environment"
  },
  "ip_settings": {
    "lan_ip_max_count": 2,
    "wan_ip_v6_support": false
  },
  "limits": {
    "ram": {
      "warning": 33.3,
      "danger": 66.7
    },
    "disk": {
      "warning": 50,
      "danger": 75
    }
  },
  "temperature": {
    "char_before_unit": "°",
    "use_fahrenheit": false
  },
  "timeout": {
    "ipv4_detection": 1,
    "ipv6_detection": 1
  }
}
```
```bash
$ emacs ~/.zshrc
```

Add in file after line `antigen apply`

```bash
##### Start Archey enabling #####
archey
##### End Archey enabling #####
```

### checked

Set in url of a web browser, the ip of your vps, if apache is correctly config, you will see an apache-debian page.

## Reverse your vps ip with your domain name

### Setting domain name dahsboard
To configure a subdomain to your VPS
(in ovh: web > Domain > `domain name`)

1. Add domain name with type `A`
2. set a new subdomain, here i set `server1`
3. Put your ip VPS into `target` or `cible`
4. Validate and wait a moment, take a coffe.
5. set in url of web browser, the subdomain here: `server1.monsite.fr`
    - If you see the debian page of your vps continue (apache)
    - Else wait again

### Setting IP in dashbord
(in ovh: Server > IP)

1. find your IPV4 VPS in col `reverse` put your subdomain with a dot a end.
exemple: `server1.monsite.fr.`
2. If everything is ok will get a notification on this dashbord.
3. Now you can be connected to your VPS with:
```bash
$ ssh $NEW_USER@$YOUR_SUBDOMAIN -p $NewPort
#exemple: ssh admin@server1.monsite.fr -p 52
```

### OVH setting
In ovh they might be an init config

```bash
$ sudo emacs /etc/cloud/cloud.cfg
```

So you need to desactivate it, change or add this lines:

```bash
preserve_hostname: true
manage_etc_hosts: false
```

### Configure your host
change hostname with the sub name here `server1`

```bash
$ sudo emacs /etc/hostname
server1
```

and change hosts

```bash
$ sudo emacs /etc/hosts
127.0.1.1 server1.monsite.fr    server1
127.0.0.1 localhost
```

```bash
$ sudo reboot
```

Reload your URL, if nothing is displayed wait until reboot finish.

## Configure Apache2

You can enter in directory `/var/www/html` this is your main directory of webpage you can change url or create directory.

## Contributing
- How to make Readme.md : 
    - https://guides.github.com/features/mastering-markdown/
    - https://shields.io/

- Security part : 
    - https://www.geek17.com/fr/content/debian-10-buster-prise-en-main-d-un-vps-ssd-d-ovh-103

- WSL and Linux terminal setup (debian/ubuntu) : 
    - https://github.com/MrStanDu33/ProcessusWebServ

- Security and debian part
    - https://docs.ovh.com/fr/public-cloud/modifier-le-hostname-dune-instance/#aller-plus-loin_1

    - https://www.geek17.com/fr/content/debian-9-stretch-installer-et-configurer-mariadb-65

    - https://docs.ovh.com/fr/public-cloud/modifier-le-hostname-dune-instance/