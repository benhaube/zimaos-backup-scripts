<p align="center">
    <img src="/assets/logo.webp" alt="Project logo. Modified version of lucide/square-terminal icon" width="400" height="400">
</p>

<h1 align="center">
    ZimaOS Backup Scripts
</h1>

The ZimaOS NAS is the main backup server for clients and other servers on the local network. To maintain the **3-2-1 Backup Strategy** the important data stored on the NAS is backed up to [Backblaze B2](https://www.backblaze.com/cloud-storage) cloud storage. To automate the backup process we utilize Bash scripts that use the `rclone` command and Systemd unit files to trigger the scripts on a set schedule.