# 520b Drive Reformatting

This repository contains a bash script that automatically reformats 520b sector-sized-formatted drives to the standard 512b sector size format on a Linux system.

## Prerequisites:

Ensure you have `sg3-utities` installed.

```bash
sudo apt-get install -y sg3-utils   # or your distro equivalent
```

## WARNING:

This is a destructive process that will wipe all data on the specified drives. Please ensure you only reformat the desired drives.

## Guide to Use:

1. Clone this repository and make it executable:
    
    ```bash
    git clone https://github.com/echumley/520b-Drive-Reformat.git
    cd 520b-Drive-Reformat && chmod +x 520b-reformat.sh
    ```

2. Verify drives & sector size:

    ```bash
    lsblk -d -o NAME,MODEL,SIZE,TYPE,TRAN,ROTA,LOG-SEC,PHY-SEC
    # or:
    for d in /dev/sd?; do echo "$d:"; cat /sys/block/${d#/dev/}/queue/{logical_block_size,physical_block_size}; done
    # optional detailed:
    sudo sg_readcap -l /dev/sdX
    ```

3. (Optional) Wipe old RAID metadata / data (destructive):

    ```bash
    # Writes zeros to each target drive until full.
    # ADJUST DEVICE LIST AS NECESSARY!

    sudo dd if=/dev/zero bs=1M status=progress | tee \
    >(sudo dd of=/dev/sdc bs=1M oflag=direct) \
    >(sudo dd of=/dev/sdd bs=1M oflag=direct) \
    >(sudo dd of=/dev/sdf bs=1M oflag=direct) \
    >(sudo dd of=/dev/sdg bs=1M oflag=direct) \
    >(sudo dd of=/dev/sdh bs=1M oflag=direct) \
    >(sudo dd of=/dev/sdi bs=1M oflag=direct) \
    | sudo dd of=/dev/sdj bs=1M oflag=direct

    # Alternatives:
    # sudo wipefs -a /dev/sdX
    # sudo sgdisk --zap-all /dev/sdX
    ```

4. Run the reformat:
    
    ```bash
    ./520b-reformat.sh
    ```

5.	Re-check sector sizes:

    ```bash
    lsblk -d -o NAME,LOG-SEC,PHY-SEC
    sudo sg_readcap -l /dev/sdX
    ```
