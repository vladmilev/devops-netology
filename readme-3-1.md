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

9. В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?

строка 242
       { list; }
              list  is  simply executed in the current shell environment.  list must be terminated with a newline or semicolon.  This
              is known as a group command.  The return status is the exit status of list.  Note that unlike the metacharacters (  and
              ),  {  and  } are reserved words and must occur where a reserved word is permitted to be recognized.  Since they do not
              cause a word break, they must be separated from list by whitespace or another shell metacharacter.

Это список выполняется в текущей среде оболочки. 
Погуглив, понял, что эта конструкция обозначает циклическое выполнение команды для элементов списка.

10. С учётом ответа на предыдущий вопрос, как создать однократным вызовом touch 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?

Команда touch создает пустой файл 
Команда для списка touch {000001..100000}.tmp - создаст в текущей директории 100000 файлов с расширением .tmp
mkdir 10
cd 10
touch {000001..100000}.tmp
ls -h
Попытка выполнить touch {000001..300000}.tmp 
не приводит в выполнению, получаем ошибку -bash: /usr/bin/touch: Argument list too long
Слишком длинный список аргументов.






