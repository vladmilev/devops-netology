# Домашнее задание к занятию "7.3. Основы и принцип работы Терраформ"

## Задача 1. Создадим бэкэнд в S3 (необязательно, но крайне желательно).

Если в рамках предыдущего задания у вас уже есть аккаунт AWS, то давайте продолжим знакомство со взаимодействием терраформа и aws.  
Создайте s3 бакет, iam роль и пользователя от которого будет работать терраформ. Можно создать отдельного пользователя,   
а можно использовать созданного в рамках предыдущего задания, просто добавьте ему необходимы права, как описано здесь.  
https://www.terraform.io/docs/backends/types/s3.html  
Зарегистрируйте бэкэнд в терраформ проекте как описано по ссылке выше.  
```
(may1) - terraform (установлен из хранилища yc - см. модули 5.4, 7.1)
$ curl https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash
$ yc --version
Yandex.Cloud CLI 0.91.0 linux/amd64
```

## Задача 2. Инициализируем проект и создаем воркспейсы.

1. Выполните terraform init:  
если был создан бэкэнд в S3, то терраформ создат файл стейтов в S3 и запись в таблице dynamodb.  
иначе будет создан локальный файл со стейтами.  
2. Создайте два воркспейса stage и prod.  
3. В уже созданный aws_instance добавьте зависимость типа инстанса от вокспейса, что бы в разных ворскспейсах использовались разные instance_type.  
4. Добавим count. Для stage должен создаться один экземпляр ec2, а для prod два.  
5. Создайте рядом еще один aws_instance, но теперь определите их количество при помощи for_each, а не count.  
6. Что бы при изменении типа инстанса не возникло ситуации, когда не будет ни одного инстанса добавьте параметр жизненного цикла create_before_destroy = true в один из рессурсов aws_instance.  
7. При желании поэкспериментируйте с другими параметрами и ресурсами.

В виде результата работы пришлите:  
- Вывод команды terraform workspace list.  
```
$ terraform workspace list
  default
* prod
  stage
```
- Вывод команды terraform plan для воркспейса prod.  
```
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

yandex_vpc_network.network_terraform: Refreshing state... [id=enp395ke3jk8c3f4albm]
yandex_vpc_subnet.subnet_terraform: Refreshing state... [id=e9bo55ari8i4o3bhn8qd]
yandex_compute_instance.vm-test[1]: Refreshing state... [id=fhmhb1a74d8lfptltqqh]
yandex_compute_instance.vm-dop["vm-1"]: Refreshing state... [id=fhm2dil12a2oks5ih9m4]
yandex_compute_instance.vm-test[0]: Refreshing state... [id=fhmjpvkig8s8l8muggss]
yandex_compute_instance.vm-dop["vm-2"]: Refreshing state... [id=fhm2o9qqb03o2vimb7q3]

------------------------------------------------------------------------

No changes. Infrastructure is up-to-date.

This means that Terraform did not detect any differences between your
configuration and real physical resources that exist. As a result, no
actions need to be performed.
```

main.tf
```
  GNU nano 2.9.3                                                                                        main.tf

terraform {
  required_version = ">= 0.13"

  required_providers {
    yandex = {
      source  = "yandex-cloud/yandex"
    }
  }
}

provider "yandex" {
  token     = "AQAAAAAAj43QAATuwaZxmI3B1ENSot2r1onn1w0"
  cloud_id  = "b1g43hks12bv8as0q653"
  folder_id = "b1ga7u7bcjd3rfctvh7j"
  zone      = "ru-central1-a"
}

# 7.3 added VM
locals {
  wspace_count_map = {
    stage = 1
    prod = 2
  }
  wspace_memory_map = {
    "vm-1" = 2
    "vm-2" = 4
  }
}

resource "yandex_compute_instance" "vm-test" {
  name  = "test-${count.index}"

  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd8ic5bmgr51h6e7bh1v"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.subnet_terraform.id
    nat       = true
  }

  metadata = {
    foo      = "bar"
    ssh-keys = "ubuntu:${file("~/.ssh/id_rsa.pub")}"
  }
  
  count = local.wspace_count_map[terraform.workspace]
}

resource "yandex_compute_instance" "vm-dop" {
  for_each = local.wspace_memory_map

  name = each.key

  resources {
    cores  = 2
    memory = each.value
  }

  boot_disk {
    initialize_params {
      image_id = "fd8ic5bmgr51h6e7bh1v"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.subnet_terraform.id
    nat       = true
  }

  metadata = {
    foo      = "bar"
    ssh-keys = "ubuntu:${file("~/.ssh/id_rsa.pub")}"
  }

  lifecycle {
    create_before_destroy = true
  }
}

resource "yandex_vpc_network" "network_terraform" {
  name = "net_terraform"
}

resource "yandex_vpc_subnet" "subnet_terraform" {
  name           = "sub_terraform"
  zone           = "ru-central1-a"
  network_id     = yandex_vpc_network.network_terraform.id
  v4_cidr_blocks = ["10.128.0.0/24"]
}
```
В результате выполнения (terraform apply) создалось 4 виртуальные машины для vm-dop - изменились имена машин ("vm-1", "vm-2") и размер памяти на каждой (2 и 4)
