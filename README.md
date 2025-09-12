# bewCloud Desktop Sync

> [!IMPORTANT]
> 
> The native desktop app is no longer built/maintained, and instead now I only provide some commands here, which use `rclone` to sync the directories (the app already used that). This is simpler and safer, to be honest.
>
> If you're looking for the last working version of the desktop app, it's at the [v0.0.5 release](https://github.com/bewcloud/bewcloud-desktop/releases/tag/v0.0.5) or [this commit](https://github.com/bewcloud/bewcloud-desktop/tree/585620e5017f43680265fd9a790e04f54b4c7e47).
>
> If you're looking for the last failed attempt to upgrade it to Tauri v2, check [this PR](https://github.com/bewcloud/bewcloud-desktop/pull/4).

If you'd like to keep a local directory in sync with a bewCloud instance, these are the recommended commands. They rely on [`rclone`](https://rclone.org), which relies on [`rsync`](https://rsync.samba.org).

Usernames, passwords, and sync are all handled by `rclone`. The connection to a bewCloud instance happens via HTTP and a remote via WebDav is created in `rclone`.

If you're looking for the mobile app, it's at [`bewcloud-mobile`](https://github.com/bewcloud/bewcloud-mobile).

## Install

1. Install `rclone` from the [official website](https://rclone.org/install/).
2. Setup your bewCloud remote: `rclone config create "bewcloud" webdav vendor=fastmail` and provide your bewCloud DAV URL, username/email, and DAV password.
3. Create your local "root directory" to sync (optional, but recommended): `mkdir -p /home/user/bewcloud`
4. Per remote directory you want to sync, run the first sync: `rclone bisync -vc "bewcloud:/<remote-directory>/" "/home/user/bewcloud/<local-directory>" --resync`

> [!NOTE]
>
> Some people have had issues with synchronizing empty directories. If you're having issues, you can try adding `--create-empty-src-dirs` to the sync command in number 4 and below.

## Sync / Cron

Assuming you want to run the sync every hour, you can use the following cron job, per directory: `0 * * * * rclone bisync -vc "bewcloud:/<remote-directory>/" "/home/user/bewcloud/<local-directory>"`

Alternatively, if you'd like to run the sync ad-hoc and keep some log to track things, you can create a file like `sync-bewcloud.sh` with all the directories you want to sync:

```sh
#!/bin/sh
rclone bisync -vc "bewcloud:/<remote-directory-1>/" "/home/user/bewcloud/<local-directory-1>"
rclone bisync -vc "bewcloud:/<remote-directory-2>/" "/home/user/bewcloud/<local-directory-2>"
```

And then your crontab can be `0 * * * * /home/user/sync-bewcloud.sh > /home/user/sync-bewcloud.log 2>&1`

And you can simply run `./sync-bewcloud.sh` to sync the directories and check the output.

## Uninstall / Remove

To uninstall/remove these sync scripts, you can delete the `rclone` config file for bewcloud (`rclone config delete bewcloud`), and remove the crons.

## Migrating from the desktop app

You should only have to follow the sync/cron section above.

You should also remove the app. If it's not deleted, you can delete the `<AppDataDir>/config.json` file, which isn't necessary or used by `rclone`.

## I _really_ want a desktop app, though

I'm sorry, but there's not enough incentive for me to keep maintaining a native desktop app. I've heard great things about [PhotoSync](https://www.photosync-app.com/home), though I haven't used it myself. Any WebDav client should work, and if it doesn't, feel free to open an issue at [`bewcloud/bewcloud`](https://github.com/bewcloud/bewcloud).
