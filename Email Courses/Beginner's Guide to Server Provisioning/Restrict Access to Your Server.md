Security is the basic aspect anyone can think of when it comes to servers. The most fundamental part of security that we ignore is how our server can be accessed.

Potentially, it is one of the most neglected points in server security. Whenever we spin up a new Droplet or a Linode or a custom VPS, we get access to a bare server with a `root` login. Allowing access as a `root` login to our server is one of the most common vulnerable ways in which a server can be compromised.

Today, we are going to go through a series of steps to restrict access to our server.

I have created a new droplet on Digital Ocean. Let us now restrict access to our server:

##Create a non-root User

The first thing to do is create a **non-root** user. `root` user is a **GOD** user. It can do anything in the server without any authentication mechanism. I will create an user `viraj` and assign a strong password to it.

Login to our VPS as a `root` user and add a new user: `adduser [username]`

It will ask for few personal details. We will enter those details, assign a [secure password](https://strongpasswordgenerator.com/) and confirm.

Below is the gif demonstrating this simple process:

![Add new user](https://virajkhatavkar.com/wp-content/uploads/2017/01/Add-new-user.gif)

###Assign sudo Group to the non-root User

`sudo` is short-form for **superuser do** Any `sudo` user will be able to perform all actions that a `root` user can, but requires a password authentication. This becomes a potential barrier if any hacker by chance gets access to our server.

We will assign the group `sudo` to user `viraj`

```bash
usermod -a -G sudo user
```
The above command says: Assign a secondary group `sudo` to user `viraj`

`-a`: append groups to the user

`-G`: assign a secondary group without removing the existing groups

`sudo`: group name to append

`viraj`: username to whom group is to be assigned


![Assign sudo group to root user](https://virajkhatavkar.com/wp-content/uploads/2017/01/Assign-sudo-group-to-user-viraj.gif)

###Check if you can login as the new user

Next, we should check if we can log in as `viraj`. Open a new tab in your terminal and log in to your server as `viraj`.

![Login as a new user](https://virajkhatavkar.com/wp-content/uploads/2017/01/Login-as-the-new-user.gif)

As you can see in the above gif, we can login as `viraj` successfully.

##Configure SSH key based authentication for new user

Now, logout from the server as `viraj`. **Do not logout as the root user. Keep the tab of terminal open where root user is already logged in**

As `viraj`, we have to log in to the server using a *plain text password*. This is another security flaw which most of us ignore. Allowing password based login to server is very risky. A strong password of 8 characters can be easily guessed with a brute force attack.

An SSH key based authentication mechanism allows us to have more rigid security. An SSH-key is more secure as it is very long. This makes it nearly impossible to brute force. Plus, hacker needs to have access to your private key as well as public key to get access to our server.

###Create a Local SSH Key

First we will create a local SSH key in our terminal:

```bash
ssh-keygen -t rsa -b 4096
```

`-t`: The algorithm to use for generating the new key

`-b`: How long the key should be (in bits)

###Copy SSH Key to Your Server

Now, we need to copy this SSH key to `/home/viraj/.ssh/authorized_keys` on our server.

**Mac Users**

```bash
ssh-copy-id viraj@139.59.39.163
```

The SSH key will be automatically added to the `authorized_keys` file in our home directory on the server.

![ssh-copy-id](https://virajkhatavkar.com/wp-content/uploads/2017/01/ssh-copy-id.gif)

**PC Users**

On a Windows machine, we need to manually copy the SSH key from our local PC to user's `authorized_keys` file.

###Log in with SSH Key

Now, if we try to log in to our server as `viraj`, we will not be asked for our password. Instead, our SSH key will be automatically detected.


![SSH Key Login](https://virajkhatavkar.com/wp-content/uploads/2017/01/ssh-key-based-login.gif)

> It is difficult or I can say impossible to brute force an SSH key of 4096 bits.

###Disable Password Authentication for Our Server

Now, if we try to log in to our server from another PC or Laptop, we can still login using our password. This indicates that our server is still vulnerable to password based attacks.

The next step should be to disable **Password Authentication** for our server. This will ensure that no one can attempt to log in to our server using a plain text password. Even if someone guesses our password, it will be not possible for them to get access to our server without the private key.

Once, we disable *Password Authentication*, we will always require a SSH key to log in to our server.

>Allowing *Password Authentication* is a major potential threat for our server. Make sure that none of your severs is accessible using a plain text password.

Go to the terminal tab of `root` user. We will edit the `/etc/ssh/sshd_config` file to disable *Password Authentication*.

Update the following line:

```bash
PasswordAuthentication no
```
Then restart the `ssh` service to effect the changes we have made:

```bash
service ssh restart
```

![Password Authentication no](https://virajkhatavkar.com/wp-content/uploads/2017/01/PasswordAuthentication-no.gif)

##Disable Root Login

Next, we will disable `root` login permanently. `root` being the god user, it is not wise to keep it active. In case we need to do anything, we can always do it as the `sudo` user.

We will edit the `/etc/ssh/sshd_config` file to disable the `root` login:

```bash
PermitRootLogin no
```
Then restart the `ssh` service to effect the changes we have made:

```bash
service ssh restart
```

![PermitRootLogin no](https://virajkhatavkar.com/wp-content/uploads/2017/01/PermitRootLogin-no.gif)

##Conclusion

We covered some basics of *Restricting Access to Our Server* . Do you feel anything else is missing in the above guide? What else do you take care to restrict access to your server? If you think of anything shoot an email to [viraj@virajkhatavkar.com](mailto:viraj@virajkhatavkar.com)