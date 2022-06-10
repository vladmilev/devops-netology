# Домашнее задание к занятию "7.2. Облачные провайдеры и синтаксис Terraform"

## Задача 1 (Вариант с Yandex.Cloud). Регистрация в ЯО и знакомство с основами (необязательно, но крайне желательно).  
Подробная инструкция на русском языке содержится здесь. https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart    
Обратите внимание на период бесплатного использования после регистрации аккаунта.  
Используйте раздел "Подготовьте облако к работе" для регистрации аккаунта. Далее раздел "Настройте провайдер" для подготовки базового терраформ конфига.  
Воспользуйтесь инструкцией (https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs) на сайте терраформа,   
что бы не указывать авторизационный токен в коде, а терраформ провайдер брал его из переменных окружений.  
```
создал новый репозиторий под это задание - terra72  
работал через vagrant (машина may1)
Запуск terraform init долго выдавал ошибку 403 пока не выполнил в точности по инструкции переадресацию инсталляции провайдера на зеркало 
и удаление всех запомненных ранее настроек 
1. nano ~/.terraformrc
provider_installation {
  network_mirror {
    url = "https://terraform-mirror.yandexcloud.net/"
    include = ["registry.terraform.io/*/*"]
  }
  direct {
    exclude = ["registry.terraform.io/*/*"]
  }
}
2. rm -rf .terraform*
3. сам main.tf 
terraform {
  required_providers {
    yandex = {
      source = "yandex-cloud/yandex"
    }
  }
  required_version = ">= 0.13"
}

provider "yandex" {
  token     = "<OAuth>"
  cloud_id  = "<идентификатор облака>"
  folder_id = "<идентификатор каталога>"
  zone      = "<зона доступности по умолчанию>"
}
свои значения провайдера можно подставить из результатов выполнения команды
yc config list
```

## Задача 2. Создание aws ec2 или yandex_compute_instance через терраформ.  
В файле main.tf создайте ресурс yandex_compute_image.  
Если вы выполнили первый пункт, то добейтесь того, что бы команда terraform plan выполнялась без ошибок.  
В качестве результата задания предоставьте:  
Ответ на вопрос: при помощи какого инструмента (из разобранных на прошлом занятии) можно создать свой образ ami?  
Ссылку на репозиторий с исходной конфигурацией терраформа.  
```
Cвой образ ami можно создать при помощи Packer, тем более что он реализует подход IaC

Добавил ресурс в main.tf
resource "yandex_compute_image" "ubuntu-image" {
  name   = "my-ubuntu-image"
  family = "ubuntu-2004-lts"
}

Результат выполнения terraform plan

Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.
------------------------------------------------------------------------
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
Terraform will perform the following actions:
  # yandex_compute_image.ubuntu-image will be created
  + resource "yandex_compute_image" "ubuntu-image" {
      + created_at      = (known after apply)
      + family          = "ubuntu-2004-lts"
      + folder_id       = (known after apply)
      + id              = (known after apply)
      + min_disk_size   = (known after apply)
      + name            = "my-ubuntu-image"
      + os_type         = (known after apply)
      + pooled          = (known after apply)
      + product_ids     = (known after apply)
      + size            = (known after apply)
      + source_disk     = (known after apply)
      + source_family   = (known after apply)
      + source_image    = (known after apply)
      + source_snapshot = (known after apply)
      + source_url      = (known after apply)
      + status          = (known after apply)
    }
Plan: 1 to add, 0 to change, 0 to destroy.
-----------------------------------------------------------------------
Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.

Ссылка на репозиторий с конфигурацией терраформа https://github.com/vladmilev/terra72
```
