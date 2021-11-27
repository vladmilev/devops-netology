## Домашнее задание к занятию "3.5. Файловые системы"

1. Узнайте о sparse (разряженных) файлах.
```
Использование разрежённых файлов считается одним из способов сжатия данных на уровне файловой системы;
Разрежённые файлы используются для хранения контейнеров, например образов дисков виртуальных машин;
Используются в торрентах.
```

2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
```
Нет не могут, так как hardlink это ссылка на тот же самый файл, он имеет тот же самый iNode и права будут одни и теже.
```

3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:
```
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.provider :virtualbox do |vb|
    lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
    lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
    vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
    vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
    vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
    vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
  end
end
```
Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.
```
root@vagrant:~# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                    8:0    0   64G  0 disk
├─sda1                 8:1    0  512M  0 part /boot/efi
├─sda2                 8:2    0    1K  0 part
└─sda5                 8:5    0 63.5G  0 part
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm  /
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm  [SWAP]
sdb                    8:16   0  2.5G  0 disk
sdc                    8:32   0  2.5G  0 disk
```

4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
```
$ fdisk -l /dev/sdb
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

$ fdisk /dev/sdb
Первый - первичный primary
 n   add a new partition
 p   primary (0 primary, 0 extended, 4 free)
First sector (2048-5242879, default 2048): 2048
500 Мб: 500*1048576(байт в 1 Мб)
Вычислим заключительный сектор раздела: 2048 + 500*1048576/512 = 2048 + 1024000 = 1026048
Created a new partition 1 of type 'Linux' and of size 500 MiB.

Второй раздел - логический extended
 n
 e   extended (container for logical partitions)
Created a new partition 2 of type 'Extended' and of size 2 GiB.
 p   print the partition table
 Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 1026048 1024001  500M 83 Linux
/dev/sdb2       1028096 5242879 4214784    2G  5 Extended
Установим тип ОС для второго раздела тоже на Linux (83)
 t   change a partition type
 2 
 w   write table to disk and exit
 
root@vagrant:~# lsblk /dev/sdb
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sdb      8:16   0  2.5G  0 disk
├─sdb1   8:17   0  500M  0 part
└─sdb2   8:18   0    2G  0 part
```

5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.
```
sfdisk - manipulate a disk partition table
 -d, --dump <dev>                  dump partition table (usable for later input)
$ sfdisk -d /dev/sdb | sfdisk --force /dev/sdc

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 1026048 1024001  500M 83 Linux
/dev/sdc2       1028096 5242879 4214784    2G 83 Linux
```
6. Соберите mdadm RAID1 на паре разделов 2 Гб
```
mdadm is used for building, managing, and monitoring Linux md devices (aka RAID arrays)

 mdadm --help-options
 --create      -C   : Create a new array
 --verbose     -v   : Be more verbose about what is happening
 mdadm --create --help
 --level=           -l : raid level: 0,1,4,5,6,10,linear,multipath and synonyms 
 --raid-devices=    -n : number of active devices in array
 
# mdadm --create --verbose /dev/md1 -l 1 -n 2 /dev/sd{b2,c2}
mdadm: partition table exists on /dev/sdb2
mdadm: partition table exists on /dev/sdb2 but will be lost or
       meaningless after creating array
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
mdadm: size set to 2104320K
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.
```
7. Соберите mdadm RAID0 на второй паре маленьких разделов.
```
# mdadm --create --verbose /dev/md0 -l 0 -n 2 /dev/sd{b1,c1}
mdadm: chunk size defaults to 512K
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.

# lsblk
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0  500M  0 part
│ └─md0                9:0    0  996M  0 raid0
└─sdb2                 8:18   0    2G  0 part
  └─md1                9:1    0    2G  0 raid1
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0  500M  0 part
│ └─md0                9:0    0  996M  0 raid0
└─sdc2                 8:34   0    2G  0 part
  └─md1                9:1    0    2G  0 raid1
```
8. Создайте 2 независимых PV на получившихся md-устройствах.
```
pvcreate - Initialize physical volume(s) for use by LVM

pvcreate /dev/md0 /dev/md1
  Physical volume "/dev/md0" successfully created.
  Physical volume "/dev/md1" successfully created.
```
9. Создайте общую volume-group на этих двух PV.
```
 vgcreate - Create a volume group
 
 # vgcreate vg1 /dev/md0 /dev/md1
  Volume group "vg1" successfully created
 # vgdisplay
```
10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.
```
lvcreate - Create a logical volume

# lvcreate -L 100M vg1 /dev/md0
  Logical volume "lvol0" created.
# lvs
  LV     VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lvol0  vg1       -wi-a----- 100.00m
  root   vgvagrant -wi-ao---- <62.54g
  swap_1 vgvagrant -wi-ao---- 980.00m
```
11. Создайте mkfs.ext4 ФС на получившемся LV.
```
# mkfs.ext4 /dev/vg1/lvol0
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```
12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.
```
root@vagrant:~# mkdir /tmp/new
root@vagrant:~# mount /dev/vg1/lvol0 /tmp/new
```
13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.
```
root@vagrant:~# wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
--2021-11-27 05:38:58--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22608017 (22M) [application/octet-stream]
Saving to: ‘/tmp/new/test.gz’

/tmp/new/test.gz           100%[=====================================>]  21.56M  5.63MB/s    in 4.3s

2021-11-27 05:39:03 (5.01 MB/s) - ‘/tmp/new/test.gz’ saved [22608017/22608017]

root@vagrant:~# ls -l /tmp/new
total 22096
drwx------ 2 root root    16384 Nov 27 05:36 lost+found
-rw-r--r-- 1 root root 22608017 Nov 27 02:06 test.gz
```
14. Прикрепите вывод lsblk.
```
root@vagrant:~# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                    8:0    0   64G  0 disk
├─sda1                 8:1    0  512M  0 part  /boot/efi
├─sda2                 8:2    0    1K  0 part
└─sda5                 8:5    0 63.5G  0 part
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0  500M  0 part
│ └─md0                9:0    0  996M  0 raid0
│   └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new
└─sdb2                 8:18   0    2G  0 part
  └─md1                9:1    0    2G  0 raid1
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0  500M  0 part
│ └─md0                9:0    0  996M  0 raid0
│   └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new
└─sdc2                 8:34   0    2G  0 part
  └─md1                9:1    0    2G  0 raid1
```
15. Протестируйте целостность файла:  
```
root@vagrant:~# gzip -t /tmp/new/test.gz  
root@vagrant:~# echo $?  
0  
```
```
$? - это код возврата из процесса последнего запуска. 0 означает, что ошибки не произошло. Другие значения представляют собой какое-то необычное состояние.

root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```
16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
```
 pvmove - Move extents from one physical volume to another
 
 root@vagrant:~# pvmove /dev/md0 /dev/md1
  /dev/md0: Moved: 24.00%
  /dev/md0: Moved: 100.00%
root@vagrant:~# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                    8:0    0   64G  0 disk
├─sda1                 8:1    0  512M  0 part  /boot/efi
├─sda2                 8:2    0    1K  0 part
└─sda5                 8:5    0 63.5G  0 part
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0  500M  0 part
│ └─md0                9:0    0  996M  0 raid0
└─sdb2                 8:18   0    2G  0 part
  └─md1                9:1    0    2G  0 raid1
    └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0  500M  0 part
│ └─md0                9:0    0  996M  0 raid0
└─sdc2                 8:34   0    2G  0 part
  └─md1                9:1    0    2G  0 raid1
    └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new
```
17. Сделайте --fail на устройство в вашем RAID1 md.
```
mdadm is used for building, managing, and monitoring Linux md devices (aka RAID arrays)
$ mdadm --fail --help
  --fail        -f   : mark subsequent devices a faulty
~# mdadm /dev/md1 --fail /dev/sdb2
mdadm: set /dev/sdb2 faulty in /dev/md1
```
18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.
```
root@vagrant:~# dmesg | grep md1
[ 3864.659058] md/raid1:md1: not clean -- starting background reconstruction
[ 3864.659060] md/raid1:md1: active with 2 out of 2 mirrors
[ 3864.659070] md1: detected capacity change from 0 to 2154823680
[ 3864.660892] md: resync of RAID array md1
[ 3875.029729] md: md1: resync done.
[ 6409.386305] md/raid1:md1: Disk failure on sdb2, disabling device.
               md/raid1:md1: Operation continuing on 1 devices.
```
19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:  
```
root@vagrant:~# gzip -t /tmp/new/test.gz  
root@vagrant:~# echo $?  
0  
```
```
root@vagrant:~# gzip -t /tmp/new/test.gz && echo $?
0
```
20. Погасите тестовый хост, vagrant destroy.  
```
$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...
```
