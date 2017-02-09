#Install MySQL Server

Let us install MySQL server on our VPS:

```bash
# Install MySQL

apt-get install -y mysql-server
```

In the process, it will ask to set up a password for the `root` user. Set a [strong password](https://strongpasswordgenerator.com/) for `root` user. Next, let us check the version of MySQL server that is installed:

```bash
mysql --version

mysql  Ver 14.14 Distrib 5.7.17, for Linux (x86_64) using  EditLine wrapper
``` 

We can see that the version `5.7.17` of MySQL server is installed on our server. This is the most recent version at the moment that has been installed.

##Secure MySQL Server

Once we have installed the server, it is necessary to run the recommneded security procedure. We will run `mysql_secure_installation` to secure our server in the official recommended ways.

First, it will ask for the root password to start the process. Type the `root` password and press `Enter`:

```bash
mysql_secure_installation

Securing the MySQL server deployment.

Enter password for user root:
```

Next, it will ask whether we want to change the root password. If you have already set a [strong password](https://strongpasswordgenerator.com/), then there is no need to change it. I will answer `N` and continue

```bash
The subsequent steps will run with the existing configuration
of the plugin.
Using existing password for root.

Estimated strength of the password: 50
Change the password for root ? ((Press y|Y for Yes, any other key for No) : N
```

Next, it will ask to remove the anonymous user. An anonymous user can log in to the MySQL server without having to have a user account created for them. This is potentially harmful. We will answer `Y` and continue:

```bash
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : Y
```

Next, we will disallow the `root` user to log in remotely.

```bash
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : Y
```

Next, we will remove the `test` database that can be accessed by any user:

```bash
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : Y
```

Next, we need to reload the privileges table to make sure that the above changes take effect immediately:

```bash
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y
```

We are now finished with securing our MySQL server using the recommended way.

##Create a non-root User

We should create a non-root user which we can use in our application to connect to our production database:

```bash
mysql> CREATE USER 'viraj'@'139.59.62.154' IDENTIFIED BY 'Secret@123!@#';
Query OK, 0 rows affected (0.00 sec)

```

Set up the `GRANT options` and flush the privileges tables to make sure that the changes take effect:

```bash
mysql> GRANT ALL ON *.* TO 'viraj'@'139.59.62.154' IDENTIFIED BY 'Secret@123!@#' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)


mysql> GRANT ALL ON *.* TO 'viraj'@'%' IDENTIFIED BY 'Secret@123!@#' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)


mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)
```

Use this non-root user to connect to your database rather than the `root` user.