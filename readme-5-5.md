# Домашнее задание к занятию "5.5. Оркестрация кластером Docker контейнеров на примере Docker Swarm"


## Задача 1
Дайте письменые ответы на следующие вопросы:  
- В чём отличие режимов работы сервисов в Docker Swarm кластере: replication и global?  
- Какой алгоритм выбора лидера используется в Docker Swarm кластере?  
- Что такое Overlay Network?  
```

```

## Задача 2
Создать ваш первый Docker Swarm кластер в Яндекс.Облаке  
Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:  
docker node ls  

Скриншот результата: 
<p align="center">
  <img src="./yc-3.png">
</p>

```
1. сгенерировал key.json (в папке terraform)
yc iam key create --service-account-name cloud-vladmilev --output key.json
2. $ terraform init
$ terraform validate
$ terraform plan
$ terraform apply
Ошибка
3. Указать свои значения идентификаторов
$ nano variables.tf
4.Удалил сеть и подсеть
$ yc vpc subnet delete --name my-subnet-a && yc vpc network delete --name net
5.Запуск
$ terraform apply --auto-approve
6.
```

## Задача 3
Создать ваш первый, готовый к боевой эксплуатации кластер мониторинга, состоящий из стека микросервисов.  
Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:  
docker service ls  

Скриншот результата: 
<p align="center">
  <img src="./yc-3.png">
</p>

```

```
