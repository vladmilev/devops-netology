# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | получена ошибка TypeError: unsupported operand type(s) for +: 'int' and 'str' |
| Как получить для переменной `c` значение 12?  | с= str(a) + b |
| Как получить для переменной `c` значение 3?  | с= a + int(b) |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
path= os.getcwd()+'/sysadm-homeworks/'
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(path + prepare_result)
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-bionic:~$ ./lesson42.sh
/home/vagrant/sysadm-homeworks/04-script-02-py/README.md
/home/vagrant/sysadm-homeworks/README.md
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3
import sys
import os

if  len(sys.argv) > 1:
        path=  sys.argv[1]
else:
        path= "~/sysadm-homeworks"
print("Path= "+path)
bash_command = ["cd "+path, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
print("Modified files:")
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(path +"/"+ prepare_result)
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-bionic:~$ ./lesson42.sh ~/sysadm-homeworks/04-script-02-py
Path= /home/vagrant/sysadm-homeworks/04-script-02-py
Modified files:
/home/vagrant/sysadm-homeworks/04-script-02-py/README.md
/home/vagrant/sysadm-homeworks/04-script-02-py/../README.md
```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3
import socket
import time
import datetime

# set variables
i = 1
wait = 2 # интервал проверок в секундах
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init=0

print('*** start script ***')
print(srv)
print('********************')

while 1==1 : #отладочное число проверок
  for host in srv:
    ip = socket.gethostbyname(host)
    if ip != srv[host]:
      if i==1 and init !=1:
        print(str(datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] '+
        str(host) +' IP mistmatch: '+srv[host]+' '+ip)
      srv[host]=ip
# счетчик итераций для отладки, закомментировать для бесконечного цикла 3 строки
  i+=1
  if i >= 50 :
    break
  time.sleep(wait)
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-bionic:~$ ./lesson424.sh
*** start script ***
{'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
********************
2021-12-18 07:08:24 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 173.194.220.194
2021-12-18 07:08:24 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 64.233.161.83
2021-12-18 07:08:24 [ERROR] google.com IP mistmatch: 0.0.0.0 216.58.210.174
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так получилось, что мы очень часто вносим правки в конфигурацию своей системы прямо на сервере. Но так как вся наша команда разработки держит файлы конфигурации в github и пользуется gitflow, то нам приходится каждый раз переносить архив с нашими изменениями с сервера на наш локальный компьютер, формировать новую ветку, коммитить в неё изменения, создавать pull request (PR) и только после выполнения Merge мы наконец можем официально подтвердить, что новая конфигурация применена. Мы хотим максимально автоматизировать всю цепочку действий. Для этого нам нужно написать скрипт, который будет в директории с локальным репозиторием обращаться по API к github, создавать PR для вливания текущей выбранной ветки в master с сообщением, которое мы вписываем в первый параметр при обращении к py-файлу (сообщение не может быть пустым). При желании, можно добавить к указанному функционалу создание новой ветки, commit и push в неё изменений конфигурации. С директорией локального репозитория можно делать всё, что угодно. Также, принимаем во внимание, что Merge Conflict у нас отсутствуют и их точно не будет при push, как в свою ветку, так и при слиянии в master. Важно получить конечный результат с созданным PR, в котором применяются наши изменения. 

### Ваш скрипт:
```python
???
```

### Вывод скрипта при запуске при тестировании:
```
???
```
