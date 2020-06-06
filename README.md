# BitwardenRS Backup

Docker containers for [bitwarden_rs](https://github.com/dani-garcia/bitwarden_rs) backup.

Find this docker image at [Docker Hub](https://hub.docker.com/repository/docker/ttionya/bitwardenrs-backup).

## Usage

We upload the backup files to the storage system by [Rclone](https://rclone.org/).

Visit [GitHub](https://github.com/rclone/rclone) for more storage system tutorials. Different systems get tokens differently.

You can get the token by the following command.

```shell
docker run --rm -it --mount source=bitwardenrs-rclone-data,target=/config/ ttionya/bitwardenrs-backup:latest rclone config
```

After setting, check the configuration content by the following command.

```shell
docker run --rm -it --mount source=bitwardenrs-rclone-data,target=/config/ ttionya/bitwardenrs-backup:latest rclone config show

# Microsoft Onedrive Example
# [YouRemoteName]
# type = onedrive
# token = {"access_token":"access token","token_type":"token type","refresh_token":"refresh token","expiry":"expiry time"}
# drive_id = driveid
# drive_type = personal
```

Note that you need to set the environment variable `RCLONE_REMOTE_NAME` to a remote name like `YouRemoteName`.

### Automatic Backups

Make sure that your bitwarden_rs container is named `bitwardenrs` otherwise you have to replace the container name in the `--volumes-from` section of the docker run call.

Start backup container with default settings (automatic backup at 5 minute every hour)

```shell
docker run -d \
  --restart=always \
  --name bitwardenrs_backup \
  --volumes-from=bitwardenrs \
  --mount source=bitwardenrs-rclone-data,target=/config/ \
  -e RCLONE_REMOTE_NAME="YouRemoteName"
  ttionya/bitwardenrs-backup:latest
```

### Use Docker Compose

Download `docker-compose.yml` to you machine, edit environment variables and start it. You need to go to the directory where the `docker-compose.yml` file is saved.

```shell
# Start
docker-compose up -d

# Stop
docker-compose stop

# Restart
docker-compose restart

# Remove
docker-compose down
```

## Environment Variables

#### RCLONE_REMOTE_NAME

Rclone remote name, you can name it yourself.

Default: `BitwardenBackup`

#### RCLONE_REMOTE_DIR

Folder for storing backup files in the storage system.

Default: `/BitwardenBackup/`

#### CRON

Schedule run backup script, based on Linux `crond`. You can test the rules [here](https://crontab.guru/#5_*_*_*_*).

Default: `5 * * * *` (run the script at 5 minute every hour)

#### ZIP_ENABLE

Compress the backup file as Zip archive. When set to `'FALSE'`, only upload `.sqlite3` files without compression.

Default: `TRUE`

#### ZIP_PASSWORD

Set your password to encrypt Zip archive. Note that the password will always be used when compressing the backup file.

Default: `WHEREISMYPASSWORD?`

#### BACKUP_KEEP_DAYS

Only keep last a few days backup files in the storage system. Set to `0` to keep all backup files.

Default: `0`

## License

MIT