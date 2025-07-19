# 󰏢 Complete rclone Google Drive CLI Guide



## Initial Setup

```bash
# Install rclone
sudo pacman -S rclone

# Install FUSE (for mounting)
sudo pacman -S fuse3

# Configure Google Drive
rclone config
# Choose: n → gdrive → Google Drive → default settings → y for auto config
# When asked about Shared Drive: choose 'n' for personal Google Drive

# Verify setup
rclone listremotes
rclone lsd gdrive:
```


## Basic Operations

```bash
# List files and folders
rclone ls gdrive:                    # All files with sizes
rclone lsd gdrive:                   # Directories only
rclone ls gdrive:my-folder          # Files in specific folder

# Upload (copy)
rclone copy ~/Documents/file.txt gdrive:/
rclone copy ~/Pictures gdrive:/Photos
rclone copy ~/local-folder gdrive:/remote-folder

# Download
rclone copy gdrive:/file.txt ~/Downloads/
rclone copy gdrive:/Photos ~/Pictures

# Sync (makes destination identical to source - DELETES extra files)
rclone sync ~/Documents gdrive:/Backup      # Local → Cloud
rclone sync gdrive:/Backup ~/Documents      # Cloud → Local

# Safety: Always test sync first
rclone sync ~/Documents gdrive:/Backup --dry-run
```


## Mount (Virtual Filesystem)

```bash
# Mount entire Google Drive
mkdir ~/gdrive-mount
rclone mount gdrive: ~/gdrive-mount --daemon

# Mount specific folder
rclone mount gdrive:my-dir ~/gdrive-test --daemon

# Unmount
fusermount -u ~/gdrive-mount
# or for fuse3
fusermount3 -u ~/gdrive-mount

# Use mounted drive like normal filesystem
ls ~/gdrive-mount
cp ~/gdrive-mount/file.pdf ~/Downloads/
```


## sync vs mount Comparison

| Feature              | sync      | mount             |
|----------------------|-----------|-------------------|
| Files stored locally | Yes       | Virtual only      |
| Offline access       | Yes       | Need internet     |
| Performance          | Fast      | Network dependent |
| Real-time changes    | Manual    | Immediate         |
| Disk usage           | Full size | Minimal           |


## Automation with Cron

```bash
# Edit crontab
crontab -e

# Examples:
# Daily backup at 2 AM
0 2 * * * rclone sync ~/Documents gdrive:/Backup --log-file ~/rclone.log

# Every hour
0 * * * * rclone sync ~/Documents gdrive:/Backup --log-file ~/rclone.log

# Safety flags for automation
rclone sync ~/Documents gdrive:/Backup \
    --log-file ~/rclone.log \
    --log-level INFO \
    --max-delete 100 \
    --exclude "*.tmp"
```


## Configuration Portability

```bash
# Copy config to new system
cp ~/.config/rclone/rclone.conf ~/rclone-backup.conf

# On new system
mkdir -p ~/.config/rclone
cp ~/rclone-backup.conf ~/.config/rclone/rclone.conf

# If authentication expires
rclone config reconnect gdrive
```


## Useful Flags

```bash
--dry-run           # Preview changes without executing
--progress          # Show progress bar
--log-file FILE     # Log to file
--bwlimit 1M        # Limit bandwidth
--exclude "*.tmp"   # Exclude file patterns
--interactive       # Ask before deletions
--max-delete 10     # Safety limit on deletions
```


## Key Points to Remember

- **sync deletes files** - always use `--dry-run` first
- **mount requires internet** - files aren't stored locally
- **copy is safe** - never deletes, only adds/updates
- **Config is portable** - copy `~/.config/rclone/rclone.conf`
- **Test before automating** - especially with cron jobs

