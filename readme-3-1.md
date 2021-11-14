## Домашнее задание к занятию «3.1. Работа в терминале, лекция 1»

5. Какие ресурсы выделены по-умолчанию?  
Оперетивная память: 1024 МБ
Процессоры: 2
Видеопамять: 4 МБ
Носители: 64 ГБ

6. Как добавить оперативной памяти или ресурсов процессора виртуальной машине?
Vagrant.configure("2") do |config|
 	config.vm.box = "bento/ubuntu-20.04"
 	config.vm.provider "virtualbox" do |v|
 	  v.memory = 2048
 	  v.cpus = 3
 	end
end

8. какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?
HISTFILESIZE (line 797) - максимальное число строк в файле истории
HISTSIZE (line 802) - число команд для сохранения

>что делает директива ignoreboth в bash?
A value of ignoreboth is shorthand for ignorespace and ignoredups. 

9.




