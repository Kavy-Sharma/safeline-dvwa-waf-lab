# 04 — Ubuntu Server and DVWA Setup

## Goal

This stage prepares Ubuntu Server to host DVWA. The guide uses a LAMP stack: Linux, Apache, MySQL and PHP. Apache serves the web application, PHP runs its application code and MySQL stores its data.

## Server and application details

| Item | Lab value |
|---|---|
| Ubuntu Server IP on the lab network | `10.0.2.4` |
| DVWA installation directory | `/var/www/html/DVWA` |
| Web server | Apache2 |
| Backend listening port | `8080` |
| Database | `dvwa` |
| Database username | `dvwa` |
| Direct DVWA login URL | `http://dvwa.local:8080/DVWA/login.php` |

The database password is intentionally not included in this file. Keep the real value in the local configuration only.

## Packages and services

The lab setup included Apache, MySQL and the PHP components needed by DVWA. Git was used to obtain the DVWA source, and the application was placed under `/var/www/html/DVWA`.

The following are the package installation commands shown by the reference guide. They explain the components involved; they should not be read as a fresh-install instruction if the packages are already installed:

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y apache2 php php-mysql mysql-server
sudo apt-get install -y git
sudo apt-get install -y openssl
```

- `apt-get update` refreshes the local package index.
- `apt-get upgrade -y` installs available package upgrades and automatically confirms prompts.
- `apache2` provides the web server.
- `php` provides the PHP runtime.
- `php-mysql` lets PHP applications communicate with MySQL.
- `mysql-server` provides the database service.
- `git` can clone the DVWA repository.
- `openssl` is used later to create the self-signed certificate.

## DVWA files and permissions

The guide obtains DVWA from its upstream Git repository and places it in Apache's document root. The commands below are the guide's example for a fresh installation; do not run the clone command again over an existing DVWA directory.

```bash
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
```

The guide then sets ownership and permissions:

```bash
sudo chown -R www-data:www-data DVWA
sudo chmod -R 755 DVWA
```

`www-data` is the account commonly used by Apache on Ubuntu. The ownership command makes that account the owner of the DVWA files. The permission command grants the owner read/write/execute permissions and grants group/other read and execute permissions. Avoid applying broader permissions such as `777`.

## Database configuration

DVWA uses a MySQL database named `dvwa`. A database account named `dvwa` was configured for the application. The connection details are stored in:

```text
/var/www/html/DVWA/config/config.inc.php
```

The important settings identify the database engine, database name, username, password and database host. The values in this lab are:

| Setting | Value |
|---|---|
| Database engine | MySQL |
| Database name | `dvwa` |
| Database user | `dvwa` |
| Database host | `localhost` |
| Password | Kept local; not published |

A safe example of the relevant configuration is:

```php
$_DVWA[ 'db_server' ]   = 'localhost';
$_DVWA[ 'db_database' ] = 'dvwa';
$_DVWA[ 'db_user' ]     = 'dvwa';
$_DVWA[ 'db_password' ] = 'REPLACE_WITH_YOUR_LOCAL_PASSWORD';
```

The placeholder above is not the real password. Do not replace it with the actual password in a file that will be committed to GitHub.

## Apache listening port

Apache normally uses port 80 for HTTP. In this lab, its backend listener was changed to port `8080`.

The relevant Apache files are:

```text
/etc/apache2/ports.conf
/etc/apache2/sites-available/000-default.conf
```

The guide's change is:

```apache
Listen 8080
```

and the default virtual host should use:

```apache
<VirtualHost *:8080>
```

After changing Apache's configuration, the service is restarted:

```bash
sudo systemctl restart apache2
```

This command asks systemd to restart Apache so it loads the updated configuration. It can interrupt current web connections briefly.

The backend is intentionally documented on port `8080`. SafeLine's later public-facing listener and reverse-proxy configuration are separate steps and have not been completed yet.

## DVWA database initialization

The DVWA setup page was opened and its **Create/Reset Database** action was used. The DVWA login page was then confirmed to load.

The direct application URL used in the lab is:

```text
http://dvwa.local:8080/DVWA/login.php
```

The recorded successful HTTP check is documented in `05-dns-resolution.md`.

## Custom sample table from the guide

Section 4.5 of the PDF describes creating a `test_users` table with sample accounts for later SQL injection demonstrations. The current project record does not establish that this optional sample table was created and checked. It is therefore not claimed as completed here. We can add a verified record if that database change is performed and inspected later.

## Verification status

Confirmed in the lab:

- Apache and MySQL were installed/configured.
- DVWA is present under `/var/www/html/DVWA`.
- Apache's backend port is `8080`.
- The DVWA database setup/reset action was used.
- The DVWA login endpoint was reachable from Kali and returned HTTP 200.

Not claimed at this stage:

- That the WAF is already proxying DVWA traffic.
- That SQL injection protection has been tested.
- That the optional `test_users` table exists.
- That the earlier SafeLine port `8081` HTTP 502 was resolved. That test was premature and is not being changed or investigated before the guide reaches the relevant configuration stage.

## Reference

*Safeline WAF Guide.pdf*, Sections 4.1–4.5.

