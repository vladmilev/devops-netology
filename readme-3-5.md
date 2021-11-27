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


```
7. Соберите mdadm RAID0 на второй паре маленьких разделов.
```

```
8. Создайте 2 независимых PV на получившихся md-устройствах.
```

```
9. Создайте общую volume-group на этих двух PV.

```

```
10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.
```

```
11. Создайте mkfs.ext4 ФС на получившемся LV.
```

```
12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.
```

```
13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.
```

```
14. Прикрепите вывод lsblk.
```

```
15. Протестируйте целостность файла:  
root@vagrant:~# gzip -t /tmp/new/test.gz  
root@vagrant:~# echo $?  
0  
```

```
16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
```

```
17. Сделайте --fail на устройство в вашем RAID1 md.
```

```
18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.
```

```
19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:  
root@vagrant:~# gzip -t /tmp/new/test.gz  
root@vagrant:~# echo $?  
0  
```

```
20. Погасите тестовый хост, vagrant destroy.  
```

```