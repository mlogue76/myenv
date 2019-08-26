# Server
AWS, linode, [Heroku]
# Setup
## update
```
apt update && apt upgrade
```
## hostname
```
hostnamectl set-hostname <hostname>
```
`vi /etc/hosts`
```
127.0.0.1    localhost
<ip address> <hostname>
```
## Limited privileges
### app user
```
useradd <username>
usermod -aG sudo <username>

```
### SSH key based auth.
remote server
```
mkdir ~/.ssh
```
localhost
```
ssh-keygen -b 4096
scp ~/.ssh/<id_rsa.pub> <username>@<ip address>:~/.ssh/authorized_keys
```
remote server
```
sudo chmod 700 ~/.ssh/
sudo chmod 600 ~/.ssh/*
```
### SSH config
Disallow root login over SSH
Disable password auth.
`sudo vi /etc/ssh/sshd_config`
```
PermitRootLogin no
PasswordAuthentication no
```
Restart SSH daemon
```
sudo systemctl restart sshd
```
### Network
```
sudo apt-get install ufw
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow <ssh>
sudo ufw allow <5000>
sudo ufw enable
sudo ufw status
```
# Deploy app
## src
Localhost
```
pip freeze > $ProjDir/requirements.txt
scp -r $ProjDir <username>@<ip address>:~/
```
Remote
```
sudo apt-get install python3-pip python3-venv
python3 -m venv $ProjDir/venv
cd $ProjDir
source venv/bin/activate
pip install -r requirements.txt
```
## env
`sudo vi /etc/config.json`
```
{
    "SECRET_KEY": "",
    "SQLALCHEMY_DATABASE_URI: "",
    "EMAIL": "",
    ...
}
```
`vi $ProjDir/<path>/config.py`
```
with open('/etc/config.json') as config_file:
    config = json.load(config_file)
class Config:
    // use the config object
```
## run
```
export FLASK_APP=run.py
flask run --host=0.0.0.0
```
## Nginx & gunicorn
### Install
```
sudo apt install nginx
pip install gunicorn
```
### Nginx config
`sudo rm /etc/nginx/sites-enabled/default`
`sudo vi /etc/nginx/sites-enabled/<projname>`
```
server {
    listen 80;
    server_name <ip address>;

    location /static {
        alias <ProjDir>/static;
    }

    location / {
        proxy_pass http://localhost:8000;
        include /etc/nginx/proxy_params;
        proxy_redirect off;
    }
}
```
### Network
```
sudo ufw allow http/tpc
sudo ufw delete allow 5000
sudo ufw enable
sudo systemctl nginx restart
```
### Gunicorn config
```
gunicorn -w <cpu*2+1> run:<appname>
```
Supervisor
```
sudo apt install supervisor
```
`sudo vi /etc/supervisor/conf.d/<projname>.conf`
```
[program:<projname>]
directory=<$ProjDir>
command=<$ProjDir>/venv/bin/gunicorn -w <cpu*2+1> run:<appname>
user=<username>
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
stderr_logfile=/var/log/<projname>/<projname>.err.log
stdout_logfile=/var/log/<projname>/<projname>.out.log
```
Log directory
```
sudo mkdir -p /var/log/<projname>
sudo touch /var/log/<projname>/<projname>.err.log
sudo touch /var/log/<projname>/<projname>.out.log
```
Reload supervisor
```
sudo supervisorctl reload
```
## big size media
`sudo vi /etc/nginx/nginx.conf`
```
client_max_body_siz 5M;
```
Restart nginx
```
sudo systemctl nginx restart
```
