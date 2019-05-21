# Infinite WP on Dokku

Sets up a basic installation for [Infinite WP](https://infinitewp.com/) on [Dokku](http://dokku.viewdocs.io/dokku/).

- PHP 7
- Apache
- Files mounted via Dokku storage

## Dokku Setup

#### Create resources

```bash
dokku apps:create wp
dokku mysql:create wp
dokku mysql:link wp wp
```

#### Create storage

```bash
mkdir ~/storage
mkdir ~/storage/wp--html
```

#### Upload Infinite WP installation

1. Visit https://infinitewp.com/cpanel-installation/
2. Click the "Download and install" tab
3. Download the .zip of Infinite WP
4. Upload the contents of the .zip to Dokku at `~/storage/wp--html`

#### Fix file permissions

```
sudo chown -R 32767:32767 ~/storage/wp--html
sudo chmod +r ~/storage/wp--html/
```

#### Mount files to the Dokku app

```bash
dokku storage:mount wp /home/stuart/storage/wp--html:/app/html
dokku ps:rebuild
```

#### Link a domain

```bash
dokku domains:add wp wp.example.com
```

#### Secure using Let's Encrypt (optional)

```bash
dokku config:set --no-restart wp DOKKU_LETSENCRYPT_EMAIL=example@example.com
dokku letsencrypt wp
```

#### Fix HTTPS Support

For HTTPS support, InfiniteWP assumes the app is hosted on port 443. However, because Heroku's PHP buildpack runs on port 5000, InfiniteWP will attempt to "redirect" to port 443, causing an infinite loop.

To fix this, override the `$_SERVER` variables to convince InfiniteWP it's running with HTTPS.

```bash
sudo vim ~/storage/wp--html/_env.php
```

Fill the contents of this file with the following:

```php
<?php

$_SERVER['HTTPS'] = 'on';
$_SERVER['SERVER_PORT'] = '443';
```

Then, verify HTTPS is enabled in `config.php` (should be `define('APP_HTTPS', 1);`)

```bash
sudo vim ~/storage/wp--html/config.php
```

Save & restart the app:

```bash
dokku ps:restart wp
```

#### Deploy app to Dokku

_Deploy a clone of this repo to Dokku_

```bash
git remote add dokku dokku@example.com:wp
git push dokku master
```

#### Setup Infinite WP

1. Retrieve MySQL credentials (`dokku config wp` on your Dokku host)
2. Visit your new domain (wp.example.com)
3. Enter the MySQL credentials from `DATABASE_URL` (`mysql://username:password@hostname:port/database`)
4. Finish installation
5. When redirected back to the admin, **do not enable https**. Dokku terminates SSL before it reaches PHP (acting as a proxy), which Infinite WP's setting doesn't support. HTTPS will stll to work fine, though.

## Notes

#### Updating composer.json and composer.lock

To update composer.lock, you must run Composer from the appropriate PHP version. If you don't have PHP 7 installed locally, you can use Docker.

```bash
$ docker-compose run web bash
# once in the bash shell...
$ composer update # or any other command
```
