# Домашнее задание к занятию "7.5. Основы golang"

С golang в рамках курса, мы будем работать не много, поэтому можно использовать любой IDE. Но рекомендуем ознакомиться с GoLand.

## Задача 1. Установите golang.  
Воспользуйтесь инструкций с официального сайта: https://golang.org/.  
Так же для тестирования кода можно использовать песочницу: https://play.golang.org/.  
```
установка через apt (ubuntu)
$ sudo apt install golang-go -y
```

## Задача 2. Знакомство с gotour.  
У Golang есть обучающая интерактивная консоль https://tour.golang.org/.   
Рекомендуется изучить максимальное количество примеров. В консоли уже написан необходимый код, осталось только с ним ознакомиться и поэкспериментировать как написано в инструкции в левой части экрана.

## Задача 3. Написание кода.
Цель этого задания закрепить знания о базовом синтаксисе языка. Можно использовать редактор кода на своем компьютере,   
либо использовать песочницу: https://play.golang.org/.  

Напишите программу для перевода метров в футы (1 фут = 0.3048 метр). Можно запросить исходные данные у пользователя, 
а можно статически задать в коде. Для взаимодействия с пользователем можно использовать функцию Scanf:  

**Решение:**
```
package main

import "fmt"

func main() {
    fmt.Print("Enter a number (in meters): ")
    var input float64
    fmt.Scanf("%f", &input)

    output := input / 0.3048
    fmt.Printf("This is in feet: %v", output)    
}
```

Напишите программу, которая найдет наименьший элемент в любом заданном списке, например:  
x := []int{48,96,86,68,57,82,63,70,37,34,83,27,19,97,9,17,}   

**Решение:**
```
package main

import "fmt"

func main() {
	var x = []int{48, 96, 86, 68, 57, 82, 63, 70, 37, 34, 83, 27, 19, 97, 9, 17}
	result := minElement(x, 16)
	fmt.Printf("Minimum element is: %v", result)
}

func minElement(x []int, size int) int {
	if size <= 0 {
		return -1
	}
	var min int = x[0]
	for i := 0; i < size; i++ {
		if x[i] < min {
			min = x[i]
		}
	}
	return min
}
```

Напишите программу, которая выводит числа от 1 до 100, которые делятся на 3. То есть (3, 6, 9, …).  

**Решение:**
```
package main

import "fmt"

func main() {
	printDividedThree(100)
}

func printDividedThree(size int) {
	for i := 1; i <= size; i++ {
		if i%3 == 0 {
			fmt.Println(i)
		}
	}
}
```
В виде решения ссылку на код или сам код.

## Задача 4. Протестировать код (не обязательно).
Создайте тесты для функций из предыдущего задания.

**Решение:**  

-тестировал 2 задачу с функцией minElement()  
```
$ go test
PASS
ok  	task2	0.001s

- для запуска еще выполнял   
$ go mod init task2

- сам файл с тестом task2_test.go
                                             
package main

import "testing"

func TestMinElement(t *testing.T) {
        var x = []int {32, 5, 12, 6}
        min := minElement(x,4)
        if (min != 5) {
                t.Error("Expected min != 5 ", min)
        }
}
```
