# Ansible Run Analyser

ARA makes Ansible runs easier to visualize, understand and troubleshoot.
(homepage)[http://ara.readthedocs.io/]

ARA provides four things:

- An Ansible callback plugin to record playbook runs into a local or
  remote database
- The `ara_record` and `ara_read pair` of Ansible modules to record and
  read persistent data with ARA
- A CLI client to query the database
- A dynamic, database-driven web interface that can also be generated
  and served from static files

## Installation

Installing ARA from latest release on PyPi

```
yum -y install python2-pip
pip install ara
```

During the install it can happen that the installation process fails
because there is no correct `gcc` compiler available. This means the
developer tools and libraries are missing. Solve this with:

```
yum -y group install "Development Tools"
yum -y install python-devel
```

### Database

In a development and demo environment it's sufficient to use a SQLite
database, but for production purposes a _real_ database is suited better.

```
yum -y install mariadb-server
systemctl start mariadb
systemctl enable mariadb
```

And the Python database driver

```
yum -y install python-PyMySQL
```

And open the firewall

```
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload
```

## Configuration

ARA records Ansible data in a database. The callback, the CLI client and
the web application all need to know where that database is located.

ARA ensures the database exists and it's schema is created when it is
run.

ARA comes out of the box with SQLite enabled and no additional setup
required. If, for example, you would like to use MySQL instead, you will
need to create a database and it's credentials:

```
# mysql
CREATE DATABASE ara;
CREATE USER ara@localhost IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON ara.* TO ara@localhost;
FLUSH PRIVILEGES;
```

And then setup the database connection:

```
[ara]
# Start webserver with: ara-manage runserver -h 0.0.0.0 -p 8080
dir = /home/ansible/ara
database = mysql+pymysql://ara:password@localhost/ara
logfile = /var/log/ara
loglevel = 'ERROR'
logformat = %(asctime)s - %(levelname)s - %(message)s
sqldebug = False
```

## Service

Create the file `/etc/systemd/system/ara.service` containing

```
[Unit]
Description=ARA
After=network.target

[Service]
Type=simple
User=apache
Group=apache
TimeoutStartSec=0
Restart=on-failure
RestartSec=10
RemainAfterExit=yes
ExecStart=/bin/ara-manage runserver -h 0.0.0.0 -p 8080

[Install]
WantedBy=multi-user.target
```

And issue the commands:

```
systemctl daemon-reload
systemctl enable ara
systemctl start ara
```

## Browser refresh

One standing issue in the ARA code is an automatic refresh of the
displayed page. This can easily be circumvented by using a refresh
plugin in the browser.

For Firefox there is _Tab Auto Refresh_
`https://addons.mozilla.org/en-US/firefox/addon/tab-auto-refresh/` and
for Google Chrome _Refresh Monkey_
`https://chrome.google.com/webstore/detail/auto-refresh/ifooldnmmcmlbdennkpdnlnbgbmfalko`
