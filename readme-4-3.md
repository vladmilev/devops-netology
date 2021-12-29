# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3
import socket
import time
import datetime
import yaml
import json

# set variables
i = 1
wait = 2 # интервал проверок в секундах
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init=0

print('*** start script ***')
print(srv)
print('********************')

while True:
  for host in srv:
    is_error = False
    ip = socket.gethostbyname(host)
    if ip != srv[host]:
      is_error = True
      if i==1 and init!=1:
        print(str(datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] '+
        str(host) +' IP mistmatch: '+srv[host]+' '+ip)
      srv[host]=ip

# запись всех хостов с их ip в файлы json и jaml
    if is_error:
      data = []
      for host in srv:
        data.append({host:ip})
      with open('task_4_3_2.json','w') as file_json:
        json_data= json.dumps(data)
        file_json.write(json_data)
      with open('task_4_3_2.yml','w') as file_yml:
        yaml_data= yaml.dump(data)
        file_yml.write(yaml_data)

# счетчик итераций для отладки, закомментировать для бесконечного цикла 3 строки
  i+=1
  if i >= 50 :
    break
  time.sleep(wait)
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-bionic:~$ ./lesson-4-3-2.sh
*** start script ***
{'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
********************
2021-12-28 19:53:07 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 74.125.205.194
2021-12-28 19:53:07 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 74.125.131.83
2021-12-28 19:53:07 [ERROR] google.com IP mistmatch: 0.0.0.0 142.251.1.139
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
[{"drive.google.com": "142.251.1.102"}, {"mail.google.com": "142.251.1.102"}, {"google.com": "142.251.1.102"}]
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
- {drive.google.com: 142.251.1.102}
- {mail.google.com: 142.251.1.102}
- {google.com: 142.251.1.102}
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:
   * Принимать на вход имя файла
   * Проверять формат исходного файла. Если файл не json или yml - скрипт должен остановить свою работу
   * Распознавать какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны
   * Перекодировать данные из исходного формата во второй доступный (из JSON в YAML, из YAML в JSON)
   * При обнаружении ошибки в исходном файле - указать в стандартном выводе строку с ошибкой синтаксиса и её номер
   * Полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов

### Ваш скрипт:
```python
???
```

### Пример работы скрипта:
???
