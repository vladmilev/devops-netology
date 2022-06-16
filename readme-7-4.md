# Домашнее задание к занятию "7.4. Средства командной работы над инфраструктурой"  

## Задача 1. Настроить terraform cloud (необязательно, но крайне желательно)

В это задании предлагается познакомиться со средством командой работы над инфраструктурой предоставляемым разработчиками терраформа.  
Зарегистрируйтесь на https://app.terraform.io/. (регистрация бесплатная и не требует использования платежных инструментов).  
Создайте в своем github аккаунте (или другом хранилище репозиториев) отдельный репозиторий с конфигурационными файлами   
прошлых занятий (или воспользуйтесь любым простым конфигом).
Зарегистрируйте этот репозиторий в https://app.terraform.io/.  
Выполните plan и apply.  
В качестве результата задания приложите снимок экрана с успешным применением конфигурации.  
```
terraform недоступен
```

## Задача 2. Написать серверный конфиг для атлантиса  

Смысл задания – познакомиться с документацией о серверной конфигурации (https://www.runatlantis.io/docs/server-side-repo-config.html)
и конфигурации уровня репозитория (https://www.runatlantis.io/docs/repo-level-atlantis-yaml.html)  
Создай server.yaml который скажет атлантису:  
Укажите, что атлантис должен работать только для репозиториев в вашем github (или любом другом) аккаунте.  
На стороне клиентского конфига разрешите изменять workflow, то есть для каждого репозитория можно будет указать свои дополнительные команды.  
В workflow используемом по-умолчанию сделайте так, что бы во время планирования не происходил lock состояния.  
Создай atlantis.yaml который, если поместить в корень terraform проекта, скажет атлантису:  
Надо запускать планирование и аплай для двух воркспейсов stage и prod.
Необходимо включить автопланирование при изменении любых файлов *.tf.
В качестве результата приложите ссылку на файлы server.yaml и atlantis.yaml.
```
1. надо поднять docker-контейнер с запущенным Atlantis (работающий сервер) и настроить его
https://www.runatlantis.io/docs/deployment.html#deployment-2 (в секции Deployment есть раздел для Docker)  

2. настроить свой репозиторий на работу с ним
Видео демо-работы с Atlantis https://www.youtube.com/watch?v=ASdnj59iG2E

# sudo -s
# docker pull ghcr.io/runatlantis/atlantis
# docker run ghcr.io/runatlantis/atlantis server --gh-user=vladmilev --gh-token=XXX --repo-allowlist=*
Вывод:
{"level":"warn","ts":"2022-06-16T08:27:41.393Z","caller":"cmd/server.go:827","msg":"no GitHub webhook secret set. This could allow attackers to spoof requests from GitHub","json":{},"stacktrace":"github.com/runatlantis/atlantis/cmd.(*ServerCmd).securityWarnings\n\tgithub.com/runatlantis/atlantis/cmd/server.go:827\ngithub.com/runatlantis/atlantis/cmd.(*ServerCmd).run\n\tgithub.com/runatlantis/atlantis/cmd/server.go:615\ngithub.com/runatlantis/atlantis/cmd.(*ServerCmd).Init.func2\n\tgithub.com/runatlantis/atlantis/cmd/server.go:511\ngithub.com/runatlantis/atlantis/cmd.(*ServerCmd).withErrPrint.func1\n\tgithub.com/runatlantis/atlantis/cmd/server.go:904\ngithub.com/spf13/cobra.(*Command).execute\n\tgithub.com/spf13/cobra@v0.0.0-20170905172051-b78744579491/command.go:650\ngithub.com/spf13/cobra.(*Command).ExecuteC\n\tgithub.com/spf13/cobra@v0.0.0-20170905172051-b78744579491/command.go:729\ngithub.com/spf13/cobra.(*Command).Execute\n\tgithub.com/spf13/cobra@v0.0.0-20170905172051-b78744579491/command.go:688\ngithub.com/runatlantis/atlantis/cmd.Execute\n\tgithub.com/runatlantis/atlantis/cmd/root.go:30\nmain.main\n\tgithub.com/runatlantis/atlantis/main.go:49\nruntime.main\n\truntime/proc.go:255"}
{"level":"info","ts":"2022-06-16T08:27:41.959Z","caller":"server/server.go:837","msg":"Atlantis started - listening on port 4141","json":{}}
{"level":"info","ts":"2022-06-16T08:27:41.960Z","caller":"scheduled/executor_service.go:46","msg":"Scheduled Executor Service started","json":{}}
 
Создал новую ветку (test_atlantis) в репозитории https://github.com/vladmilev/terra72
создал Pull Request - пишу комментарии (atlantis plan) в https://github.com/vladmilev/terra72/pull/1
- никаких изменений не происходит?
- в браузере работающего сервиса по localhost - не появляется (хотя из виртуальной машины 80 порт проброшен)
# docker ps
CONTAINER ID   IMAGE                          COMMAND                  CREATED          STATUS          PORTS     NAMES
8bc2fc11b627   ghcr.io/runatlantis/atlantis   "docker-entrypoint.s…"   11 minutes ago   Up 11 minutes             elated_black
- выходит сервис не выдает открытых портов?
Что не так, помогите разобраться?
```

## Задача 3. Знакомство с каталогом модулей

В каталоге модулей (https://registry.terraform.io/browse/modules) найдите официальный модуль от aws для создания ec2 инстансов.  
Изучите как устроен модуль. Задумайтесь, будете ли в своем проекте использовать этот модуль или непосредственно ресурс aws_instance без помощи модуля?  
В рамках предпоследнего задания был создан ec2 при помощи ресурса aws_instance. Создайте аналогичный инстанс при помощи найденного модуля.  
В качестве результата задания приложите ссылку на созданный блок конфигураций.  
```
terraform недоступен
```
