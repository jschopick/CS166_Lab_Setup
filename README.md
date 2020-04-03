# CS 166 Server Access and Database Setup Guide
### Courtesy of Qizhong Mao

## Table of Contents  
- [Accessing a Lab Machine via SSH](#accessing-a-lab-machine-via-ssh)  
    - [Accessing from Linux or MacOS](#accessing-from-linux-or-macos)
    - [Accessing from Windows](#accessing-from-windows)
- [Database Setup (Courtesy of Petko Bakalov)](#database-setup-courtesy-of-petko-bakalov))  

## Accessing a Lab Machine via SSH

### Accessing from Linux or MacOS

#### 1. Creating SSH Configuration File

1. Create folder `~/.ssh` (if not exists)
    ```bash
    mkdir -m0700 ~/.ssh
    ```
2. Create (if not exists) and edit file `~/.ssh/config`
    ```bash
    vi ~/.ssh
    ```
    
    If you are not familiar with `vi`, you may use any command line text editors, 
    or text editors with GUI to edit the file.
    - In Ubuntu, you may use
        ```bash
        gedit ~/.ssh/config
        ```
    - In MacOS, you may use 
        ```bash
        open /System/Applications/TextEdit.app/Contents/MacOS/TextEdit ~/.ssh/config
        ```
        or in older versions of MacOS
        ```bash
        open /Applications/TextEdit.app/Contents/MacOS/TextEdit ~/.ssh/config
        ```

3. Add the following content to `~/.ssh/config` (Remember to save the file) 
    - If you want a separate connection to *bolt.cs.ucr.edu*:
        ```
        Host SHORTCUT_NAME_BOLT
            HostName bolt.cs.ucr.edu
            User UCR_ID
        ```
        Replace UCR_ID with your UCR ID. Replace `SHORTCUT_NAME_BOLT` with a name you prefer, for example, **bolt**.

    - To add a shortcut to a lab machine *__wch129-01__.cs.ucr.edu* (assuming you are assigned to machine 01 in lab 
      129):
        ```
        Host SHORTCUT_NAME_LAB
            HostName wch129-01.cs.ucr.edu
            User UCR_ID
            ProxyCommand ssh -W %h:%p UCR_ID@bolt.cs.ucr.edu
        ```
        or
        ```
        Host bolt
            HostName bolt.cs.ucr.edu
            User UCR_ID
        Host SHORTCUT_NAME_LAB
            HostName wch129-01.cs.ucr.edu
            User UCR_ID
            ProxyCommand ssh -W %h:%p UCR_ID@bolt
        ```
        The second one creates 2 shortcuts, the first section configures a shortcut for *bolt.cs.ucr.edu*, the second
        section configures a shortcut for the lab machine using the shortcut for *bolt*.

        Replace `SHORTCUT_NAME_LAB` with a name you prefer, for example, **lab-machine**.

4. (Optional) Set permission to `~/.ssh/config`
    ```bash
    chmod 0644 ~/.ssh/config
    ```

#### 2. SSH Connection
To execute commands on the lab machine, you need to open a SSH connection:

If the shortcut name of *bolt.cs.ucr.edu* is **bolt**.
```bash
ssh bolt
```
If the shortcut name of *__wch129-01__.cs.ucr.edu* is **lab-machine**.
```bash
ssh lab-machine
```
Via SSH config, you don't need to specify the full host name and your user name, as well as the proxy command.


#### 3. Transferring Files
To transfer files between your local machine and the server, use the following commands:

Transfer a file / directory from local machine to the server:
```bash
# Transfer a file 'somefile'
scp ~/user/somefile lab-machine:~/afolder/
# Transfer a directory 'somedir' and its contents
scp -r ~/user/somedir lab-machine:~/afolder/
```

Transfer a file / directory from the server to local machine:
```bash
# Transfer a file 'somefile'
scp lab-machine:~/afolder/somefile ~/user/
# Transfer a directory 'somedir' and its contents
scp -r lab-machine:~/afolder/somedir ~/user/
```

More details can be found at [How to Use SCP Command to Securely Transfer Files
](https://linuxize.com/post/how-to-use-scp-command-to-securely-transfer-files/) 

#### Notes
- Lab machines do not save your files in the home directory after logout.
- For the same reason, lab machines cannot save your public key for SSH keyless authentication. Meaning that you can 
  only use password for SSH authentication.
- SSH config does not have the ability to save your password. There is a way, though, not recommended as it is insecure:
  [https://serverfault.com/questions/535028/adding-password-to-ssh-config](https://serverfault.com/questions/535028/adding-password-to-ssh-config)

### Accessing from Windows

#### 1. Obtain and Install WinSCP and PuTTY
- WinSCP: [https://winscp.net/](https://winscp.net/)  
- PuTTY: [https://www.chiark.greenend.org.uk/~sgtatham/putty/](https://www.chiark.greenend.org.uk/~sgtatham/putty/)

#### 2. Set Putty in WinSCP
1. Open WinSCP, click `Tools`, select `Preferences`, this will open the Preferences dialog.

2. Select `Integration` then `Applications`, set `PuTTY/Terminal client path` to **putty.exe**. The default path should
   work.  
    ![winscp+putty](https://winscp-static-746341.c.cdn77.org/data/media/screenshots/pref_integration_app.png)  
   You may check `Automatically open new sessions in PuTTY` if you want to automatically open a PuTTY windows whenever
   you open a connection to a server.

3. Click `OK` to save the configurations.

#### 3. (Optional) Create a Site to bolt.cs.ucr.edu
Follow the following steps if you want to connect to *bolt.cs.ucr.edu*:
1. In WinSCP's login dialog, select `New Site` in the left panel, then fill the following information in the right 
   ***Session* panel:
    - `File protocol` SFTP (default)
    - `Hostname` *bolt.cs.ucr.edu*
    - `Port number` 22 (default)
    - `User name` Your UCR ID
    - `Password` Your CS password. Leave empty if you don't want to save it.

    ![winscp-login](https://winscp-static-746341.c.cdn77.org/data/media/screenshots/login.png)

2. Click `Save` to create a shortcut for bolt. You may set a `Site name` as **bolt** or any name you prefer. Check 
`Save password (not recommended)` if you prefer to save your password. Be sure to check this option **only** in a safe 
environment. The press `OK` to save the site's configuration.

#### 4. Create a Site to Lab Machine via SSH Tunnel
All lab machines are protected by gateways, meaning you can only access them via a gateway server, such as bolt. To 
access a lab machine, you need to configure bolt as [SSH Tunnel](https://www.ssh.com/ssh/tunneling/example#what-is-ssh-port-forwarding,-aka-ssh-tunneling?).
1. In WinSCP's login dialog, create a new site with the following information:
    - `File protocol` SFTP (default)
    - `Host name` *__wch129-01__.cs.ucr.edu*, assuming you are assigned to machine 01 in lab 129.
    - `Port number` 22 (default)
    - `User name` Your UCR ID
    - `Password` Your CS password. Leave empty if you don't want to save it.

2. Click `Advanced`, this will open `Advanced Site Settings`  
    ![winscp-site-advanced](https://winscp-static-746341.c.cdn77.org/data/media/screenshots/login_environment.png)

3. In the left panel, select `Connection`, then select `Tunnel`.  
    ![winscp-tunnel](https://winscp.net/eng/data/media/screenshots/login_tunnel.png)

4. Check `Connect through SSH tunnel`, and fill the following information:
    - `Host name` *bolt.cs.ucr.edu*
    - `Port number` 22 (default)
    - `User name` Your UCR ID
    - `Password` Your CS password. Leave empty if you don't want to save it.
    - `Local tunnel port` *Autoselect* (default)
    - `Private key file` Leave empty (lab machines do not support keyless authentication)  

5. Click `OK` to save the site's advanced settings.

6. Click `Save` to create a shortcut for the site. You may set a `Site name` as **lab-machine**, or **wch129-01** 
(assuming you are assigned to machine 01 in lab 129), or any name you prefer. Check `Save password (not recommended)` 
if you prefer to save your password. Be sure to check this option **only** in a safe environment. The press `OK` to save
 the site's configuration.   

More details about SSH Tunnel in WinSCP: [Tunnel Page (Advanced Site Settings dialog)](https://winscp.net/eng/docs/ui_login_tunnel)

#### 5. Use WinSCP
You can connect to a site by selecting its shortcut in WinSCP's login dialog, by clicking the `Login` button (with a 
green icon on the left and down arrow on the right).

Through WinSCP, you can transfer files from your local computer to the remote server. You can use the left and right 
file panel for file transfers. Or you can drag-and-drop files from the server to local, or from local to the server.

To open an **interactive** command line, you can use one of the following options:
- Go to `Commands`, select `Open in PuTTY`
- In the tool bar, click the icon with a flash between 2 monitors
- Use shortcut `Ctrl + P`

*Note*: In PuTTY, you can copy any text content by selecting the characters. You can paste by right clicking.

#### Documentations
- WinSCP: [https://winscp.net/eng/docs/start](https://winscp.net/eng/docs/start)
- PuTTY: [https://the.earth.li/~sgtatham/putty/0.73/htmldoc/](https://the.earth.li/~sgtatham/putty/0.73/htmldoc/)


## Database Setup (Courtesy of Petko Bakalov)
1. Log in to lab computer with your CS account.

2. Setup your PostgreSQL environment
    1. Create a folder under /tmp as the environment of your test database:  
        ```bash
        mkdir /tmp/$LOGNAME
        ```

    2. Create a data and socket folder under your environment, which will contain Postgre data files and socket 
    information:
        ```bash
        cd /tmp/$LOGNAME
        mkdir -p test/data
        mkdir sockets
        export PGDATA=/tmp/$LOGNAME/test/data
        ```
   
    3. Initialize the database environment:
        ```bash
        initdb
        ```

3. Start the PostgreSQL database server
    ```bash
    pg_ctl -o "-c unix_socket_directories=/tmp/$LOGNAME/sockets" -D $PGDATA -l /tmp/$LOGNAME/logfile start
    ```

4. Create your database (Replace `$DB_NAME` with your database name)
    ```bash
    createdb -h /tmp/$LOGNAME/sockets $DB_NAME
    ```
    If you run into the following error, first check to make sure you completed steps 1-3 correctly.
    ```
    createdb: could not connect to database template1: could not connect to server: No such file or directory
        Is the server running locally and accepting
        connections on Unix domain socket "/tmp/$LOGNAME/sockets/.s.PGSQL.5432"
    ```
   If it is still not working, try redoing step 3 with the following path instead. Then rerun the createdb command.
   ```
   pg_ctl -o "-c unix_socket_directories=/tmp/$LOGNAME/sockets" -D /extra/$LOGNAME/cs166/mydb/data -l logfile start
   ```

5. Start the interactive environment (Replace `$DB_NAME` with your database name)
    ```bash
    psql -h /tmp/$LOGNAME/sockets $DB_NAME
    ```
    The Postgre prompt like **\mydb=#** will show up to accept your SQL commands, 
    where *mydb* is the database name you specified in `$DB_NAME`. Use `\q` to quit the interactive environment.
    You can also pipeline a SQL script into the interactive environment to execute it in bulk:
    ```bash
    psql -h /tmp/$LOGNAME/sockets $DB_NAME < yourscript.sql
    ```

6. SQL Administrator Cook List Show all databases in your Postgre instance:
    ```sql
    SELECT datname FROM pg_database;
    ```
    Show all tables viewable to you:
    ```sql
    SELECT table_name
    FROM information_schema.tables
    WHERE table_schema='public';
    ```

7. Stop the database instance
    ```bash
    pg_ctl -o "-c unix_socket_directories=/tmp/$LOGNAME/sockets" -D /tmp/$LOGNAME/test/data stop
    ```
    If you run into this error,
    ```
    pg_ctl: PID file "/tmp/$LOGNAME/test/data/postmaster.pid" does not exist
    Is server running?
    ```
    try running the command with the following path instead:
    ```
    pg_ctl -o "-c unix_socket_directories=/tmp/$LOGNAME/sockets" -D /extra/$LOGNAME/cs166/mydb/data stop
    ```
