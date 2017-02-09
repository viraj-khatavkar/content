#Automatic Security Updates

Ubuntu releases security updates regularly. We need to be sure that our server is updated with those security patches. These updates are patches for known vulnerabilities available publicly. It is most important to make sure our server is updated. Keeping track of these updates and installing them manually is a tedious task. We will schedule our server to check for security updates, download and install them regularly.

##Installation

```bash
apt-get install -y unattended-upgrades
```

This will install the `unattended-upgrades` package which is necessary for running automatic updates. This package has the capability to update all of our server packages automatically. **It is not advisable to do so**. We will configure only security updates to be downloaded and installed automatically.

##Configuration

The configuration file is stored at `/etc/apt/apt.conf.d/50unattended-upgrades`. We will update this file to make sure that only security releases are allowed to be updated automatically. Rest of the updates are commented out:

```bash
// Automatically upgrade packages from these (origin:archive) pairs

Unattended-Upgrade::Allowed-Origins {
//      "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
//      "${distro_id}:${distro_codename}-updates";
//      "${distro_id}:${distro_codename}-proposed";
//      "${distro_id}:${distro_codename}-backports";
};
```

Next, we will configure our server to run the upgrade action once every day. The periodic file is responsible for this action. The default cron of the system reads this file daily to get the schedule. 

Let us edit the `/etc/apt/apt.conf.d/10periodic` file. We will add a line at the end of file to perform `unattended-upgrades` everyday:

```bash
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "0";
APT::Periodic::AutocleanInterval "0";
APT::Periodic::Unattended-Upgrade "1"
```

Congratulations!!! Your server is now set to download and install security updates every day. Let me know for any doubts or comments on [Twitter](https://twitter.com/virajkhatavkar).