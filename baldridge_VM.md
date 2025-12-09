
## Baldridge_VM

These are some common uses of the Baldridge_VM server, which we use to host R Shiny apps.

### 1. Log in to the VM

You will need to connect to the university network using the Cisco AnyConnect VPN first. Then open a terminal and type:

```bash
ssh <WashU_key_id>@p_<WashU_key_id>#accounts.ad.wustl.edu@wuit-s-14467.accounts.ad.wustl.edu@securepsmp.wustl.edu 
```

The login requires your WashU password and confirmation via DUO.

### 2. Transfer files from/to the VM server

The file transfer requires the password generated from CyberArk (https://secureaccess.wustl.edu/PasswordVault/v10/logon/radius).

```bash
# when you transfer file to the VM server, you can only transfer it to your home directory or to the shiny-server directory you created
scp -r /directory/or/file/to/transfer/ p_<WashU_key_id>@wuit-s-14467.accounts.ad.wustl.edu:/home/p_<WashU_key_id> 

# transfer file from the VM server to local computer
scp -r p_<WashU_key_id>@wuit-s-14467.accounts.ad.wustl.edu:/directory/or/file/to/transfer /destination/directory/or/file/location/ 

# or use rsync
rsync -aHv --progress /directory/or/file/to/transfer/ p_<WashU_key_id>@wuit-s-14467.accounts.ad.wustl.edu:/home/p_<WashU_key_id> 
```

If you prefer, you may use software such as FileZilla instead of the command line to transfer files between your local computer and the server. See the information below for details.

```
Host: wuit-s-14467.accounts.ad.wustl.edu
Username: p_<WashU_key_id>
Password: the one generated from CyberArk
Port: 22
```

### 3. Deploy Shiny application on the VM

To deploy a Shiny app, move the directory containing your app into the `shiny-server` directory. You can then view the app by opening the link in a browser. The link is not shared here for security reason, but is available in `/srv/shiny-server/server_domain.txt`.

```bash
sudo mv <your_directory> /srv/shiny-server/
```

You can find error log in `/var/log/shiny-server/`.

### 4. `sudo` is required for every command in `/srv/shiny-server/`

Some examples are provided below.

- Remove a file in /srv/shiny-server/
```bash
sudo rm <file_name>
```

- Install R package
```bash
sudo R
install.packages('pheatmap')
q() # exit R
```