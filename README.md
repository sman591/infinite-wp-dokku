# Infinite WP on Dokku

Sets up a basic installation for Infinite WP on Dokku.

* PHP 7
* Apache
* Files mounted via Dokku storage

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

## Notes

#### Updating composer.json and composer.lock

To update composer.lock, you must run Composer from the appropriate PHP version. If you don't have PHP 7 installed locally, you can use Docker.

```bash
$ docker-compose run web bash
# once in the bash shell...
$ cd /app
$ composer update # or any other command
```
