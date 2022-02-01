# Домашнее задание к занятию "5.4. Оркестрация группой Docker контейнеров на примере Docker Compose"


## Задача 1
Создать собственный образ операционной системы с помощью Packer.  
Для получения зачета, вам необходимо предоставить: cкриншот страницы, как на слайде из презентации (слайд 37).
```
Авторизовался на Яндекс.Облако, создал облако cloud-vladmilev

Установка Packer (https://www.packer.io/downloads)
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install packer
~$ sudo snap install packer
2022-01-31T19:14:11Z INFO Waiting for automatic snapd restart...
packer 1.0.0-2 from Nathan Handler (nhandler) installed
vagrant@ubuntu-bionic:~$ packer --version
1.0.0

Установка Terraform (terraform.io)
$ sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install terraform
$ $ terraform --version
Terraform v1.1.4
on linux_amd64

Установка утилиты yc (https://cloud.yandex.ru/docs/cli/quickstart)
$ curl https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash
$ exit
$ vagrant ssh
$ yc --version
Yandex.Cloud CLI 0.87.0 linux/amd64

$ yc init
Welcome! This command will take you through the configuration process.
Please go to https://oauth.yandex.ru/authorize?response_type=token&client_id=1a6990aa636648e9b2ef855fa7bec2fb in order to obtain OAuth token.

Please enter OAuth token: AQAAAAAAj43QAATuwaZxmI3B1ENSot2r1onn1w0
You have one cloud available: 'cloud-vladmilev' (id = b1gcjvo46oid607b3pg8). It is going to be used by default.
Please choose folder to use:
 [1] default (id = b1ggqdglh6qsi4s5off8)
 [2] Create a new folder
Please enter your numeric choice: 2
Please enter a folder name: netology
Your current folder has been set to 'netology' (id = b1g672m7v4q205bdqfc0).
Do you want to configure a default Compute zone? [Y/n] y
Which zone do you want to use as a profile default?
 [1] ru-central1-a
 [2] ru-central1-b
 [3] ru-central1-c
 [4] Don't set default zone
Please enter your numeric choice: 1
Your profile default Compute zone has been set to 'ru-central1-a'.

$ yc config list
token: AQAAAAAAj43QAATuwaZxmI3B1ENSot2r1onn1w0
cloud-id: b1gcjvo46oid607b3pg8
folder-id: b1g672m7v4q205bdqfc0
compute-default-zone: ru-central1-a

# Инициализация сети
$ yc vpc network create --name net
id: enp7nfpn17o2apus96bo
folder_id: b1g672m7v4q205bdqfc0
created_at: "2022-02-01T02:50:04Z"
name: net

# Инициализация подсети
$ yc vpc subnet create --name my-subnet-a --zone ru-central1-a --range 10.1.2.0/24 --network-name net
id: e9bsvjqagjijlprn8ild
folder_id: b1g672m7v4q205bdqfc0
created_at: "2022-02-01T02:52:49Z"
name: my-subnet-a
network_id: enp7nfpn17o2apus96bo
zone_id: ru-central1-a
v4_cidr_blocks:
- 10.1.2.0/24

# Сборка образа в Yandex.Cloud 
$ mkdir cloud
vagrant@ubuntu-bionic:~$ cd cloud
vagrant@ubuntu-bionic:~/cloud$ mkdir packer
vagrant@ubuntu-bionic:~/cloud$ nano centos-7-base.json
-----------------------
{
  "builders": [
    {
      "disk_type": "network-nvme",
      "folder_id": "b1g672m7v4q205bdqfc0",
      "image_description": "by packer",
      "image_family": "centos",
      "image_name": "centos-7-base",
      "source_image_family": "centos-7",
      "ssh_username": "centos",
      "subnet_id": "e9bsvjqagjijlprn8ild",
      "token": "",
      "type": "yandex",
      "use_ipv4_nat": true,
      "zone": "ru-central1-a"
    }
  ],
  "provisioners": [
    {
      "inline": [
        "sudo yum -y update",
        "sudo yum -y install bridge-utils bind-utils iptables curl net-tools tcpdump rsync telnet openssh-server"
      ],
      "type": "shell"
    }
  ]
}
-----------------------
$ /usr/bin/packer --version
1.7.9
vagrant@ubuntu-bionic:~/cloud$ /usr/bin/packer validate centos-7-base.json
The configuration is valid.
vagrant@ubuntu-bionic:~/cloud$ /usr/bin/packer build centos-7-base.json
...
$ yc compute image list
+----------------------+---------------+--------+----------------------+--------+
|          ID          |     NAME      | FAMILY |     PRODUCT IDS      | STATUS |
+----------------------+---------------+--------+----------------------+--------+
| fd8743i92t1hlmgpok1q | centos-7-base | centos | f2eacrudv331nbat9ehb | READY  |
+----------------------+---------------+--------+----------------------+--------+

https://prnt.sc/26mpr9e
```

## Задача 2
Создать вашу первую виртуальную машину в Яндекс.Облаке.  
Для получения зачета, вам необходимо предоставить:  cкриншот страницы свойств созданной ВМ.
```
$ mkdir terraform
$ cd terraform
$ nano network.tf
-------
# Network
resource "yandex_vpc_network" "default" {
  name = "net"
}

resource "yandex_vpc_subnet" "default" {
  name = "subnet"
  zone           = "ru-central1-a"
  network_id     = "${yandex_vpc_network.default.id}"
  v4_cidr_blocks = ["192.168.101.0/24"]
}
---------
$ nano node01.tf
---------
resource "yandex_compute_instance" "node01" {
  name                      = "node01"
  zone                      = "ru-central1-a"
  hostname                  = "node01.netology.cloud"
  allow_stopping_for_update = true

  resources {
    cores  = 8
    memory = 8
  }

  boot_disk {
    initialize_params {
      image_id    = "${var.centos-7-base}"
      name        = "root-node01"
      type        = "network-nvme"
      size        = "50"
    }
  }

  network_interface {
    subnet_id = "${yandex_vpc_subnet.default.id}"
    nat       = true
  }

  metadata = {
    ssh-keys = "centos:${file("~/.ssh/id_rsa.pub")}"
  }
}
--------
$ nano output.tf
-------
output "internal_ip_address_node01_yandex_cloud" {
  value = "${yandex_compute_instance.node01.network_interface.0.ip_address}"
}

output "external_ip_address_node01_yandex_cloud" {
  value = "${yandex_compute_instance.node01.network_interface.0.nat_ip_address}"
}
-------
$ nano provider.tf
-------
# Provider
terraform {
  required_providers {
    yandex = {
      source = "yandex-cloud/yandex"
    }
  }
}

provider "yandex" {
  service_account_key_file = "key.json"
  cloud_id  = "${var.yandex_cloud_id}"
  folder_id = "${var.yandex_folder_id}"
}
-------
$ nano variables.tf
-------
# Заменить на ID своего облака
# https://console.cloud.yandex.ru/cloud?section=overview
variable "yandex_cloud_id" {
  default = "b1gcjvo46oid607b3pg8"
}

# Заменить на Folder своего облака
# https://console.cloud.yandex.ru/cloud?section=overview
variable "yandex_folder_id" {
  default = "b1g672m7v4q205bdqfc0"
}

# Заменить на ID своего образа
# ID можно узнать с помощью команды yc compute image list
variable "centos-7-base" {
  default = "fd8743i92t1hlmgpok1q"
}
-------
$ terraform init
Terraform has been successfully initialized!

```

## Задача 3
Создать ваш первый готовый к боевой эксплуатации компонент мониторинга, состоящий из стека микросервисов.  
Для получения зачета, вам необходимо предоставить: cкриншот работающего веб-интерфейса Grafana с текущими метриками.
```

```

Задача 4 (*)
Создать вторую ВМ и подключить её к мониторингу развёрнутому на первом сервере.  
Для получения зачета, вам необходимо предоставить: cкриншот из Grafana, на котором будут отображаться метрики добавленного вами сервера.
```

```
