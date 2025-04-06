# Uniform Transparent Filesystem

The Uniform Transparent Filesystem makes files available on every server used by the research group. It is based on [**rclone mount**](https://rclone.org/commands/rclone_mount/).

## Setup

### Prerequisites

* You know the server you want to access the files on (“compute server”), e.g. `bwunicluster`
* You know the server the files are stored on (“storage server”), e.g. `leia`
* You can access the storage server from the compute server via password-based SSH authentication

### Steps

1. Log in to the compute server via SSH
2. Ensure that rclone is installed and available

    ```bash
    rclone
    ```
3. Create (or edit) the file `~/.config/rclone/rclone.conf`, so it contains a section like this:

    ```
    [leia]
    type = sftp
    host = leia.ifi.uni-heidelberg.de
    user = mustermann
    port = 22
    ask_password = true
    ```

4. Create mountpoints if they don't already exist:

    ```bash
    mkdir ~/aipfs-shared
    mkdir ~/.aipfs-shared-cache
    ```

4. Start the mount:

    ```bash
    rclone mount leia:/nfs/data/shared/ ~/aipfs-shared --cache-dir ~/.aipfs-shared-cache --vfs-cache-mode full --vfs-cache-max-age 3000h
    ```

5. Enter your password. The process will stay open, so to test if everything works, open a new SSH connection and work from that connection.

## Usage

`~/aipfs-shared` contains all the files you expected to be there from `/nfs/data/shared` on leia. Use them as usually.

### Prefetching a file

If you know you need a file soon, and want to make sure it is in the cache, you can use the following command:

```bash
dd if=~/aipfs-shared/...yourfilename... of=/dev/null status=progress
```

By looking at the progress, you can also determine if the file is currently being fetched (transfer speed ≤ 70 MB/s) or if it is loaded from cache (transfer speed > 500 MB/s).
