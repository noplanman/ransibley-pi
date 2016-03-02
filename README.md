This is a simple ansible playbook to easily set up a Raspberry Pi.

**Disclaimer**: If you don't know what ansible or a Raspberry Pi is, then I'm not sure why you're here. If you'd like to learn about them, then go [here](https://ansible.com "Ansible") and [here](https://www.raspberrypi.org/ "Raspberry Pi").

If you do, then awesome! Remember, this playbook is **as-is** and can break stuff if not used properly. So use it at your own risk :wink:

### Let's go!

**Assumed base image for your Raspi is [Raspbian Jessie](https://www.raspberrypi.org/downloads/raspbian/ "Raspian Downloads") (normal or lite).**

I use this playbook for the initial setup of my Raspis, so it may not work for you.
Be sure to check [what this playbook does exactly](#so-what-does-this-playbook-do-exactly "What's happening here...") to know what you're getting yourself into when using it.

### Usage
1. Make sure you have [ansible](https://ansible.com "Ansible") installed!
1. Clone this repo: `git clone https://github.com/noplanman/ransibley-pi`
1. Change into folder: `cd ransibley-pi`
1. Copy `hosts.example` to `hosts` and set the IP of the Raspi you're connecting to (replace `your-raspi-ip` with the correct IP). The user should be `pi` using the default installation of Raspbian, else change it here.
1. Change any variables in `group_vars/all.yml` to fit your needs.
1. Run the playbook using `ansible-playbook -i hosts site.yml -k` and enter your user password. (on a default install this is `raspberry`)
1. Wait for everything to get done for you! :smiley:
1. (When running the script again, you can omit the `-k` parameter, as it would now be using public key authentication)

### So what does this playbook do exactly?
1. Config
    - Generate locale and set it to en_US.UTF-8
    - Update Timezone to Etc/UTC
    - Set hostname to the defined value (*host_name*)
    - * Expand file system to use full SD card space
    - Reboot...

1. APT setup
    - Install apt-transport-tor package to allow apt to use [Tor](https://www.torproject.org/ "The Onion Router") for package downloads ([more info](http://people.skolelinux.org/pere/blog/Always_download_Debian_packages_using_Tor___the_simple_recipe.html "Downloading packages using Tor"))
    - Set sources to download packages using Tor (by adding `tor+` prefix to source URLs)
    - Add all defined repository GPG keys (*apt_keys*)
    - Add all defined repository sources (*apt_sources*)
    - Install packages (*apt_install_packages*)
    - Remove packages (*apt_remove_packages*)
    - Full system update of all installed packages
    - Reboot... (if necessary)

1. Add / configure user
    - Ensure new user is present (*user_name*), a member of the defined group (*user_group*) and generate an SSH public key
    - Upload the local SSH public key for public key authentication (location *ssh_pub_key*)
    - Enable passwordless sudo by adding the user to the sudoers
    - Generate a new random password for the user and set it
    - Output the password to know what password has been set (**note this down**)
    - Let ansible know about the password change and let it use the new password to reconnect

1. Lock down SSH service
    - Remove root SSH configuration
    - Update sshd_config parameters (check [ssh.yml](https://github.com/noplanman/ransibley-pi/blob/master/roles/common/tasks/ssh.yml "View File") for more info)

Legend:
- * Happens only on first run
- *the name of the applicable variable*

### Contributing

Feel free to suggest improvements! If it's something I can make use of, I'll add it happily.

When making a PR, please explain in detail what the change is for and why you recommend it :+1:
