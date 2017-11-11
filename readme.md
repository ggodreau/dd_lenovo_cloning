# BACKING UP LENOVO NOTES

1. lenovo has 3 partitions: 0 is boot (100MB), 1 is os (500GB) and 2 is storage (1.5TB)
2. lenovo has two drives, the 24GB SSD is not used and not needed for the machine (win8 cache only)
3. shut down the machine and place usb ubuntu in usb slot
4. remove system hard drive (machine will refuse to boot anything else with it in)
5. press F2 to enter bios menu and move USB boot priority to top
6. set any drive settings to legacy priority in bios
7. boot into ubuntu (system hard drive still detached). shut down computer
8. put system hard drive back in. turn back on and boot into usb (use F12 for boot menu if needed). arrive in lubuntu
9. ON REMOTE/TARGET MACHINE - insert destination/target drive (onto which we back source up), boot into lubuntu usb
10 ON TARGET machine, install openssh-server and net-tools, set passwd for lubuntu user and get ip address
11. run gnome-disks on both machine as non-root user, locate the source/target drives and paritions on both machines
12. run the following to write the source partition schema to part_table file, assume source device is /dev/sda (no numbers at the end)
    
    `$ sfdisk -d /dev/sda > part_table`

13. copy the part_table drive to the target computer via scp. ON TARGET MACHINE write the part_table to the target drive (assume target is /dev/sdb)

    `$ sfdisk /dev/sdb < part_table`
 
14. mount the storage partition of the TARGET drive. this will hold the compressed partition gz files from the source machine before we unzip them and run dd to write them to the TARGET partitions in uncompressed form

## COPY BOOT PARTITION FROM SOURCE TO TARGET

15. ON SOURCE MACHINE read in partition sda1 from local machine and place on target machine storage partition as boot.gz compressed file

    `$ sudo dd if=/dev/sda1 | gzip -1 - | ssh lubuntu@192.168.8.8 dd of=/media/lubuntu/storage/boot.gz`

15. on TARGET machine unzip the boot archive and write file to the target boot partition

    ```
    $ gunzip boot.gz
    $ sudo dd if=./boot of=/dev/sdb1 bs=128K status=progress
    ```

## COPY OS PARTITION FROM SOURCE TO TARGET
 
16. on SOURCE machine read in partition sda2 from local machine and place on target machine storage partition as os.gz compressed file

    `$ sudo dd if=/dev/sdb2 | gzip -1 - | ssh lubuntu@192.168.8.8 dd of=/media/lubuntu/storage/boot.gz`

17. on TARGET machine unzip the os archive and write file to the target boot partition

    ```
    $ gunzip os.gz
    $ sudo dd if=./os of=/dev/sdb2 bs=128K status=progress
    ```
