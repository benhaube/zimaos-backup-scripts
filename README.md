<p align="center">
    <img src="/assets/logo.webp" alt="Project logo. Modified version of lucide/square-terminal icon" width="400" height="400">
</p>

<h1 align="center">
    ZimaOS Backup Scripts
</h1>

The ZimaOS NAS is the main backup server for clients and other servers on the local network. To maintain the **3-2-1 Backup Strategy** the important data stored on the NAS is backed up to [Backblaze B2](https://www.backblaze.com/cloud-storage) cloud storage. To automate the backup process we utilize Bash scripts that use the `rclone` command and Systemd unit files to trigger the scripts on a set schedule. Reference the [Backblaze docs](https://www.backblaze.com/docs/cloud-storage-integrate-rclone-with-backblaze-b2) for help with `rclone` integration.

### Clone the Repo

``` bash
git clone https://github.com/benhaube/zimaos-backup-scripts.git
cd zimaos-backup-scripts/
```

### Activate Scripts

1. Put the backup scripts `b2-appdata-bkp.sh`, `b2-backup-bkp.sh`, and `b2-immich-bkp.sh`  into the `/opt/scripts` directory.

    ``` bash
    sudo mkdir -p /opt/scripts
    sudo cp b2-*-bkp.sh /opt/scripts
    ```

2. Make the scripts executable.

    ``` bash
    sudo chmod +x /opt/scripts/b2-*-bkp.sh
    ```

### Automate with Systemd

1. Move the Systemd timer unit files into the `/etc/systemd/system` directory.

    ``` bash
    sudo cp systemd/b2-*-bkp.timer /etc/systemd/system
    ```

2. Move the Systemd service unit files into the `/etc/systemd/system` directory.

    ``` bash
    sudo cp systemd/b2-*-bkp.service /etc/systemd/system
    ```

3. Tell Systemd to parse the changes.

    ``` bash
    sudo systemctl daemon-reload
    ```

4. Test the new services, and check the logs to make sure they completed successfully.

    ``` bash
    sudo systemctl start b2-<script>-bkp.service
    sudo journalctl -u b2-<script>-bkp.service -e
    ```

5. After confirming the services run successfully, enable the timers so they survive a reboot and begin their schedule.

    ``` bash 
    sudo systemctl enable --now b2-*-bkp.timer
    ```

6. Verify the timers are active with the following command.

    ``` bash
    systemctl list-timers --all
    ```

    **Example Output**

    ``` shell-session 
    NEXT                          LEFT LAST                              PASSED UNIT                          ACTIVATES                      
    Thu 2026-09-10 00:04:10 EDT     8h -                                      - b2-backup-bkp.timer           b2-backup-bkp.service
    Thu 2026-09-10 01:03:06 EDT     9h -                                      - b2-immich-bkp.timer           b2-immich-bkp.service
    Thu 2026-09-10 11:19:42 EDT    19h Wed 2026-09-09 11:19:42 EDT 4h 33min ago systemd-tmpfiles-clean.timer  systemd-tmpfiles-clean.service
    Sun 2026-09-13 00:04:23 EDT 3 days -                                      - b2-appdata-bkp.timer          b2-appdata-bkp.service
    Mon 2026-09-14 00:52:53 EDT 4 days -                                      - fstrim.timer                  fstrim.service
    -                                - Mon 2026-09-07 11:04:41 EDT   2 days ago cron-watchdog.timer           cron-watchdog.service
    -                                - Mon 2026-09-07 11:04:47 EDT   2 days ago zfw-ui-watchdog.timer         zfw-ui-watchdog.service
    -                                - Mon 2026-09-07 11:04:41 EDT   2 days ago zima-vm-extras-watchdog.timer zima-vm-extras-watchdog.service

    8 timers listed.
    ```