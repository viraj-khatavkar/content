#How to Setup Firewall for Our Server

I always used to wonder what firewall did which assisted to improve the security our server.

**The firewall allows us to set access rules. These rules allow or deny or reject traffic according to their sources.**

`deny` and `reject` are two different rules for firewall and work differently. We will see about that in some time.

##Getting Started

All the firewall rules are configured using the `iptables` command. We can see currently configured rules using the following command:

```bash
iptables -L -v
```

For a bare VPS, there will be no firewall rules configured:

![iptables rules](https://virajkhatavkar.com/wp-content/uploads/2017/01/iptables-L-v.gif)

##UFW

As we can see, there are no rules currently configured for our server. We can add rules to our server using `iptables` command. But, it involves a lot of complexity to get started. Instead, we will use `ufw` to configure our firewall rules.

`ufw` stands for **U**ncomplicated **F**ire**W**all. It provides a user-friendly GUI on the command line to setup firewall rules.

UFW comes pre-installed in Ubuntu distros. If your server doesn't have UFW installed, you can install it using:

```bash
apt-get install ufw
```

UFW will be inactive by default in our server:

```bash
ufw status
```

![UFW status](https://virajkhatavkar.com/wp-content/uploads/2017/01/ufw-status.gif)

##Setup Basic Rules

Let us get started by setting up the basic rules:

```bash
ufw allow 80
ufw allow 443
ufw allow 22
```

![ufw allow](https://virajkhatavkar.com/wp-content/uploads/2017/01/ufw-allow.gif)

The above commands specify:

1. Allow traffic over `port 80` i.e. allow `http` traffic
2. Allow traffic over `port 443` i.e. allow `https` traffic
3. Allow traffic over `port 22` i.e. allow `ssh` traffic

Next, we will enable ufw to activate these rules and check the updated status:

```bash
ufw enable
```

![ufw enable](https://virajkhatavkar.com/wp-content/uploads/2017/01/ufw-enable.gif)

>We have configured the basic rules for our firewall. Any incoming traffic on other than `80`, `443`, `22` will be denied. This is enough for getting started

##Deny vs Reject

As we discussed above, there is a difference between `reject` and `deny` rules. Let us understand the basic difference between them.

`reject` rule will directly reject the connection and give the rejected response. 

`deny` rule will deny the connection but will not give any response.

###Deny Rule

We will configure the `deny` rule for `port 80`. This will deny the traffic on port 80 but wouldn't give any response.

```bash
ufw deny 80
```

In the below demonstration, we configure our firewall to deny any traffic on `port 80`. After enabling the rule, if we try to load the server, we don't receive any response. Our request tries loading the page waiting for the response.

![ufw deny 80](https://virajkhatavkar.com/wp-content/uploads/2017/01/ufw-deny-80.gif)

###Reject Rule

Now, we will configure the `reject` rule for `port 80`. This will reject the traffic on port 80 right away and give the response.

```bash
ufw reject 80
```

In the below demonstration, we configure our firewall to reject any traffic on `port 80`. After enabling the rule, if we try to load the server, we receive the rejected response right away.

![ufw deny 80](https://virajkhatavkar.com/wp-content/uploads/2017/01/ufw-reject-80.gif)

##Deny All Traffic From an Ip Address

We can deny traffic from a specific IP address with following rule:

```bash
ufw deny from 115.97.246.98
```

This will deny any traffic from ip `115.97.246.98` to any port.

##Deleting Rules

If we configure some rule by mistake or want to delete some rule, we can do so easily using **UFW**. UFW interprets and applies according to their order. We can view the current order of rules:

```bash
ufw status numbered
```

![ufw status numbered](https://virajkhatavkar.com/wp-content/uploads/2017/01/ufw-status-numbered.gif)

Then we can delete a specific rule:

```bash
ufw delete 1
```

Optionally, we can delete a rule by their name:

```bash
ufw delete allow 80
```

>Do not forget to `ufw enable` after deleting a rule

##Disable UFW

If we decide to not use UFW for some reason, we can disable it:

```bash
ufw disable
```

##Conclusion

We have now configured our firewall to handle traffic. We can set up new rules according to your specific needs. We have covered only a few of the methods that are available in UFW. You can refer to their [official documentation](https://help.ubuntu.com/community/UFW) for other methods that are available.