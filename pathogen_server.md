## Pathogen server 

These are instructions for logging in to the Pathogen server, which is used for R-related analyses. However, I do not personally use this server, as I run all my analyses on HTCF, so my experience with it is limited.

### 1. Log in to the server

You will need to connect to the university network using the Cisco AnyConnect VPN first. Then open a terminal and type:

```bash
ssh <username>@pathogen.wucon.wustl.edu
```

### 2. Change password

```bash
passwd
```

### 3. Work space directory

```bash
cd /mnt/pathogen1
```

### 4. Shiny-server

The Pathogen server can also host R Shiny apps, but it is not actively used for that in the lab.

```bash
# shiny server
cd /srv/shiny-server

# error log
cd /var/log/shiny-server/
```
