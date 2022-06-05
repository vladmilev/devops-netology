# Домашнее задание к занятию "09.04 Teamcity"

## Подготовка к выполнению
1. Поднимите инфраструктуру teamcity https://github.com/netology-code/mnt-homeworks/blob/master/09-ci-04-teamcity/teamcity/docker-compose.yml   
2. Если хочется, можете создать свою собственную инфраструктуру на основе той технологии, которая нравится. Инструкция по установке из документации https://www.jetbrains.com/help/teamcity/installing-and-configuring-the-teamcity-server.html  
3. Дождитесь запуска teamcity, выполните первоначальную настройку  
4. Авторизуйте агент  
5. Сделайте fork репозитория https://github.com/aragastmatb/example-teamcity  
```
через докер-композер не получилось 
- выдает ошибку ">>> Permission problem: TEAMCITY_DATA_PATH '/data/teamcity_server/datadir' is not a writeable directory", 
поэтому стал делать через Яндекс Облако по выданному промокоду - как в лекции у Алексея Метлякова 
- вопрос из задания "Сделайте fork репозитория" 
fork репозитория - создание копии чужого репозитория
в деталях - на гитхабе есть кнопка Fork в верхнем правом углу страницы репозитория, при нажатии на которую создается полная копия чужого репозитория в своем аккаунте
Запустил сервер и агента в облаке Яндекса, но при входе на сервер TeamCity агента нет в Unauthorized - хотя для агента указан параметр SERVER_URL= "http://<IP сервера ТС>:8111" 
Все норм - добавил новые, снес старые ВМ - надо чтобы агент был правильно настроен и запущен до инициализации ТС-сервера в браузере (как я понял)
Вход admin / admin123
```

## Основная часть   
1. Создайте новый проект в teamcity на основе fork
2. Сделайте autodetect конфигурации
3. Сохраните необходимые шаги, запустите первую сборку master'a
4. Поменяйте условия сборки: если сборка по ветке master, то должен происходит mvn clean package, иначе mvn clean test
5. Мигрируйте build configuration в репозиторий
6. Создайте отдельную ветку feature/add_reply в репозитории
7. Напишите новый метод для класса Welcomer: метод должен возвращать произвольную реплику, содержащую слово hunter
8. Дополните тест для нового метода на поиск слова hunter в новой реплике
9. Сделайте push всех изменений в новую ветку в репозиторий
10. Убедитесь что сборка самостоятельно запустилась, тесты прошли успешно
11. Внесите изменения из произвольной ветки feature/add_reply в master через Merge
12. Убедитесь, что нет собранного артефакта в сборке по ветке master
13. Настройте конфигурацию так, чтобы она собирала .jar в артефакты сборки
14. Проведите повторную сборку мастера, убедитесь, что сбора прошла успешно и артефакты собраны
15. Проверьте, что конфигурация в репозитории содержит все настройки конфигурации из teamcity
16. В ответ предоставьте ссылку на репозиторий
```
1. указать Repository URL
2. autodetect конфигурации - запускается автоматически, создается Build Step Maven
3. Run - кнопка справа вверху, Test passed: 3 - ветка только master
5. не могу выполнить миграцию в репозиторий - соединение по SSH-ключу не проходит, выдает ошибку: Connection failed!
Test connection failed in Nethology2 / Build
git -c credential.helper= ls-remote origin command failed.
exit code: 128
stderr: Warning: Permanently added 'github.com,140.82.121.3' (ECDSA) to the list of known hosts.
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```