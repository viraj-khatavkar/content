#Fail2Ban - Block Attacking Bots

`fail2ban` is a service which identifies and blocks the potential hosts that may be attacking our server. It does so by identifying hosts which are generating continuously failed authentication attempts. The information is retrieved from the log files stored on the server. It blocks the hosts by updating the firewall rules.

##Installation

Let us first install `fail2ban`:

```bash
apt-get install -y fail2ban
```

This will fetch and install the `fail2ban` package on our server.

##Configuration

`fail2ban` has two types of configuration files:

1. `fail2ban.conf`: This file specifies the configuration to be used for log levels, target, pid, socket, dbfile, etc.
2. `jail.conf`: This file is important and specifies the configuration rules for identification of malicious hosts.


The default configuration for `fail2ban` is stored at `/etc/fail2ban/fail2ban.conf`. We can leave it to its default configuration for now. 

Instead, we are more interested in the rules which block the attackers. This can be found at `/etc/fail2ban/jail.conf` file. Let us go over some important rules one by one:

```bash
ignoreip = 127.0.0.1/8

bantime  = 600

maxretry = 5

findtime  = 600
```

1. `ignoreip`: "ignoreip" can be an IP address, a CIDR mask or a DNS host. `fail2ban` will never ban a host which matches an address in this list. We can specify multiple hosts by separating them with spaces.
2. `bantime`: "bantime" is the number of seconds for which the host is banned.
3. `maxretry`: "maxretry" is the maximum number of failures allowed before a host is banned.
4. `findtime`: A host is banned only if failed attempts cross `maxretry` value in a given duration of `findtime`. For above values, a host will be banned only if it fails for more than 5 times in the specified duration of 10 minutes (600 seconds)

Now, let us start the `fail2ban` service:

```bash
service fail2ban start
```

![service start fail2ban](https://virajkhatavkar.com/wp-content/uploads/2017/01/service-fail2ban-start.gif)

Now, if we try to log in to the server with invalid credentials, it will ban us after 5 attempts. I have failed to authenticate as `root` user for more than 5 times. Now, even if I try to log in into the server, it refuses the connection:

![fail2ban-refused](https://virajkhatavkar.com/wp-content/uploads/2017/01/fail2ban-refused.gif)

>The default configuration provided by `fail2ban` is secure enough to get us started.

##Editing jail.conf

Let us now modify some configurations of `jail.conf` to suit our needs. It is not advisable to edit the actual file. We can make a separate copy of the local file and edit the required configurations there. There are two approaches to doing so:

###Approach 1

Let us create a `jail.local` file and change the `maxretry` value to 3

```bash
# /etc/fail2ban/jail.local

[DEFAULT]
maxretry=3
```

Then, we will reload the fail2ban service to load the new configurations:

```bash
service fail2ban reload
```

![modify jail.local](https://virajkhatavkar.com/wp-content/uploads/2017/01/modify-jail.local_.gif)

###Approach 2

Alternatively, we can copy the `jail.conf` file:

```bash
sed 's/\(^[a-z tab]\)/# \1/' jail.conf | sudo tee jail.local &> /dev/null
```

The above command will copy the `jail.conf` file to `jail.local` and comment out everything in the file. Then, we can uncomment the `maxretry` configuration and change it:

```bash
#"maxretry" is the number of failures before a host get banned.

 maxretry = 3
```

Let us now reload `fail2ban` to load the modified configurations:

```bash
service fail2ban reload
```

>In a similar way, you can edit the `fail2ban.conf` by creating a `fail2ban.local` file
