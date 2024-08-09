### EC2 and EBS Notes

#### Checking User Data Execution
- **How to check if the userdata is executed or not?**
  - Check logs in this file:
    ```bash
    cat /var/log/cloud-init-output.log
    ```

#### Using Telnet with cURL
- **How to use telnet with curl command if telnet is not installed?**
  - Use the following curl command:
    ```bash
    curl -v telnet://fs-097876b3alfcc0d52.efs.ap-south-1.amazonaws.com:2049
    ```

#### EBS (Elastic Block Store)
- **Official Documentation:**
  - [EBS User Guide](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-using-volumes.html)

#### Listing and Managing Block Storage

- **List all block storage devices:**
  ```bash
  lsblk
  ```

- **Check the type of storage and any file system on a device (e.g., xvdf):**
  ```bash
  sudo file -s /dev/xvdf
  ```
  - Example output: `/dev/xvdf: data`

- **Create a filesystem of ext4:**
  ```bash
  sudo mkfs.ext4 /dev/xvdf
  ```
  or
  ```bash
  sudo mkfs -t ext4 /dev/xvdf
  ```

- **Mount the device to a directory (e.g., Jenkins home directory):**
  ```bash
  sudo mount /dev/xvdf /home/ec2-user/test
  ```
  or
  ```bash
  sudo mount /dev/xvdf /var/lib/jenkins/
  ```

- **Check mounted file systems:**
  ```bash
  df -kh
  ```

- **Example Output:**
  ```bash
  Filesystem Size Used Avail Use% Mounted on
  /dev/xvdf 9.8G 37M 9.3G 1% /var/lib/jenkins
  ```

- **Permanent Mount (Auto-mount after reboot):**
  - Add an entry for the device to the `/etc/fstab` file:
    ```bash
    sudo vi /etc/fstab
    ```
  - Example `fstab` entry:
    ```bash
    UUID=8763bac1-7a5f-47dc-adcb-83a3ec50dd4f /home/ec2-user/test ext4 defaults,nofail 0 2
    ```

- **Find UUID of devices:**
  ```bash
  sudo blkid
  ```

- **Unmount and remount the directory after updating `fstab`:**
  ```bash
  sudo umount /var/lib/jenkins/
  sudo mount -a
  ```

- **Start Jenkins service:**
  ```bash
  sudo service jenkins start
  ```
  - If the service fails:
    ```bash
    systemctl status jenkins.service
    journalctl -xe
    ```
  - Change ownership if needed:
    ```bash
    sudo chown jenkins:jenkins -R /var/lib/jenkins/
    ```

#### Resizing EBS Volume

- **Resize the filesystem after increasing EBS volume size:**
  ```bash
  sudo resize2fs /dev/xvdf
  ```

#### Detach and Attach Volume Created from Snapshot

- **Stop Jenkins service and unmount the volume:**
  ```bash
  sudo service jenkins stop
  sudo umount /var/lib/jenkins
  ```

- **Check the block storage devices:**
  ```bash
  df -kh
  lsblk
  ```
