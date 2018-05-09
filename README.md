# Infinite WP - oWeb

## Dokku Setup

```bash
dokku apps:create wp
dokku mysql:create wp
dokku mysql:link wp wp
```

Permissions:

```bash
mkdir /home/stuart/storage/wp--html
sudo chown -R 32767:32767 /home/stuart/storage/wp--html
sudo chmod +r /home/stuart/storage/wp--html/
```

Mount files:

```bash
dokku storage:mount wp /home/stuart/storage/wp--html:/app/html
dokku ps:rebuild
```

## Notes

None right now...
