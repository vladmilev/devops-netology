# Домашнее задание к занятию "7.6. Написание собственных провайдеров для Terraform"  

Бывает, что  
общедоступная документация по терраформ ресурсам не всегда достоверна,  
в документации не хватает каких-нибудь правил валидации или неточно описаны параметры,  
понадобиться использовать провайдер без официальной документации,  
может возникнуть необходимость написать свой провайдер для системы используемой в ваших проектах.  

## Задача 1.
Давайте потренируемся читать исходный код AWS провайдера, который можно склонировать от сюда: https://github.com/hashicorp/terraform-provider-aws.git. Просто найдите нужные ресурсы в исходном коде и ответы на вопросы станут понятны.  
1. Найдите, где перечислены все доступные resource и data_source, приложите ссылку на эти строки в коде на гитхабе.  
```
ResourcesMap и DataSourcesMap находятся в файле terraform-provider-aws/internal/provider/provider.go (:902, :423)
https://github.com/hashicorp/terraform-provider-aws/blob/47b48e5ea3b2d7543d431e8c07787767597ce0e9/internal/provider/provider.go
```
2. Для создания очереди сообщений SQS используется ресурс aws_sqs_queue у которого есть параметр name.  
С каким другим параметром конфликтует name? Приложите строчку кода, в которой это указано.  
```
конфликтует с параметром "name_prefix"
"name": {
				Type:          schema.TypeString,
				Optional:      true,
				ForceNew:      true,
				Computed:      true,
>>>				ConflictsWith: []string{"name_prefix"},
				ValidateFunc:  validateSQSQueueName,
			},
https://github.com/hashicorp/terraform-provider-aws/blob/8e4d8a3f3f781b83f96217c2275f541c893fec5a/aws/resource_aws_sqs_queue.go#L56
```
Какая максимальная длина имени?  
```
не более 80 символов - указано в валидаторе
func validateSQSQueueName(v interface{}, k string) (ws []string, errors []error) {
	value := v.(string)
	if len(value) > 80 {
		errors = append(errors, fmt.Errorf("%q cannot be longer than 80 characters", k))
	}
	if !regexp.MustCompile(`^[0-9A-Za-z-_]+(\.fifo)?$`).MatchString(value) {
		errors = append(errors, fmt.Errorf("only alphanumeric characters and hyphens allowed in %q", k))
	}
	return
}
https://github.com/hashicorp/terraform-provider-aws/blob/8e4d8a3f3f781b83f96217c2275f541c893fec5a/aws/validators.go#L1038
```
Какому регулярному выражению должно подчиняться имя?  
```
в этом же валидаторе указано регулярное выражение:
`^[0-9A-Za-z-_]+(\.fifo)?$` 
- судя по всему, может содержать только цифры, латинские буквы, символ подчеркивания и символы +".fifo" в конце
```

## Задача 2. (Не обязательно)
В рамках вебинара и презентации мы разобрали как создать свой собственный провайдер на примере кофемашины. Также вот официальная документация о создании провайдера: https://learn.hashicorp.com/collections/terraform/providers.  

Проделайте все шаги создания провайдера.  
В виде результата приложение ссылку на исходный код.  
Попробуйте скомпилировать провайдер, если получится то приложите снимок экрана с командой и результатом компиляции.  
