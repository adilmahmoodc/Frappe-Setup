# FRAPPE DOCKER SETUP

we will do step by step to install frappe in our MacOS.

## Requirements


```bash
1. python 3.10+,  node 16.4+,

2. install / download docker desktop

3. bench cli install 
   pip3 install frappe-bench

4. sudo npm install -g yarn

```

## Installation


Follow these steps and keep open the ***Docker Desktop***.
you can also see the details from [setup](https://github.com/frappe/frappe_docker/blob/main/docs/development.md).


- Clone and change directory to frappe_docker directory
```bash
git clone https://github.com/frappe/frappe_docker.git
cd frappe_docker
```

- Copy example devcontainer config from devcontainer-example to .devcontainer
```bash
cp -R devcontainer-example .devcontainer
```

- Copy example vscode config for devcontainer from development/vscode-example to development/.vscode. This will setup basic configuration for debugging.
```bash
cp -R development/vscode-example development/.vscode
```

-  ## ***Open visual studio code and install the Containers Extension:***

- Setup first bench
```bash
bench init --skip-redis-config-generation --frappe-branch version-14 frappe-bench
```

- Setup hosts
```bash
bench set-config -g db_host mariadb
bench set-config -g redis_cache redis://redis-cache:6379
bench set-config -g redis_queue redis://redis-queue:6379
bench set-config -g redis_socketio redis://redis-socketio:6379
```

- For any reason the above commands fail, set the values in common_site_config.json manually.
```bash
{
  "db_host": "mariadb",
  "redis_cache": "redis://redis-cache:6379",
  "redis_queue": "redis://redis-queue:6379",
  "redis_socketio": "redis://redis-socketio:6379"
}
```

## Create a new site with bench

- creating site with admin and mariadb passwords
```bash
bench new-site mysite.localhost --mariadb-root-password 123 --admin-password admin --no-mariadb-socket
```

## Set bench developer mode on the new site
- To develop a new app, the last step will be setting the site into developer mode.
```bash
bench --site mysite.localhost set-config developer_mode 1
bench --site mysite.localhost clear-cache
```

## Install an app
```bash
# (default app)
bench --site mysite.localhost install-app frappe 

# github repo
bench get-app --branch version-14 https://github.com/myusername/myapp
bench --site mysite.localhost install-app myapp 

```

## Setup completed

```bash 
bench start
```
- The the results in the [mysite.localhost:8000](http://mysite.localhost:8000).


## Other Commands:

```bash

# restart server
bench restart

# migrate bench apps
bench --site <sitename> migrate

# uninstall app with site
bench --site <sitename> uninstall-app <appname>

# Drop site
bench drop-site <sitename>

# bench setups
bench setup config
bench setup socketio
bench setup redis


```

## CONFIGURATIONS FOR STREAMWORK PROJECT:
#
```bash
# after installing frappe-bench successfully
# now we need to add application in it.
bench get-app --branch version-14 https://github.com/StreamWork-io/streamwork-backend.git

# after that we need to connect this application with our custom created site
bench --site <sitename> install-app streamwork

# then change the branch for backend to get latest developments
git checkout dev  | git checkout production # for this you need to go into the streamwork application folder

# migrate the streamwork app into our custom site
bench --site <sitename> migrate
```

## Important things which couldn't be forgetten

#
Set Default Email Address :  
-
- open frappe administrator panel => goto settings from sidebar => click "Email Account" link.
- select gmail service and set password of your google "App Passwords"

Set Social SignIn Keys :
- 
- get [client_id]() and [secret_id]() from [Google Console](https://console.cloud.google.com/apis/credentials).
- open frappe administrator panel
- search for ```social login keys```
- set CLIENT_ID and SECRET_ID there. 
- select service ```Google```.

Allow Cross Site Requests for Custom Site :
-
- add ```"allow_cors": "*"``` in the ```site_config.json``` file.
