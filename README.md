# docker-rclone-copy

### Creds to bcardiff and robinostlund for the work for this docker container (this is a forked version)

Docker image to perform a [rclone](http://rclone.org) copy based on a cron schedule.

## Usage

### Configure rclone

rclone needs a configuration file where credentials to access different storage provider are kept.

By default, this image uses a file `/config/rclone.conf` and a mounted volume may be used to keep that information persisted.

A first run of the container can help in the creation of the file, but feel free to manually create one.

```
$ mkdir config
$ docker run --rm -it -v $(pwd)/config:/config ghcr.io/rikardronnkvist/docker-rclone-copy:latest
```

### Perform copy in a daily basis

A few environment variables allow you to customize the behavior of the copy:

* `COPY_SRC` source location for `rclone copy` command
* `COPY_DEST` destination location for `rclone copy` command
* `CRON` crontab schedule `0 0 * * *` to perform copy every midnight
* `FORCE_COPY` set variable to perform a copy upon boot
* `COPY_OPTS` additional options for `rclone copy` command. Defaults to `-v`
* `TZ` set the [timezone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) to use for the cron and log `America/Argentina/Buenos_Aires`
* `CHECK_URL` [healthchecks.io](https://healthchecks.io) url or similar cron monitoring to perform a `GET` after a successful copy


```bash
$ docker run --rm -it -v $(pwd)/config:/config -v /path/to/source:/source -e COPY_SRC="/source" -e COPY_DEST="dest:path" -e TZ="America/Argentina/Buenos_Aires" -e CRON="0 0 * * *" -e  FORCE_COPY=1 ghcr.io/rikardronnkvist/docker-rclone-copy:latest
```

See [rclone copy docs](https://rclone.org/commands/rclone_copy/) for source/dest syntax and additional options.


# How do I use it?
* First I have [Nautical Backup](https://github.com/minituff/nautical-backup) running nightly between 02:00 and 03:00 on my docker hosts.
* The enivonment variable SECONDARY_DEST_DIRS is pointing towards the same location for all hosts. 
* Then I schedule [rdocker-rclone-copy](https://github.com/rikardronnkvist/docker-rclone-copy) to run at 03:00 and it copies all my new backups to [Jottacloud](http://jottacloud.com) with unlimited storage
