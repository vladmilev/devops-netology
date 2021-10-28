# devops-netology

В terraform/.gitignore указаны правила для игнорирования добавления в репозиторий следующих файлов:  
- Любые вложенные (файлы и сама директория) с названием .terraform    
	**/.terraform/*  
- все файлы с названием .tfstate без расширения и с любыми расширениями  
	*.tfstate  
	*.tfstate.*  
- файлы с названием и расширением crash.log  
	crash.log  
- любые файлы с расширением *.tfvars  
	*.tfvars  
- файлы с названием и расширением: override.tf, override.tf.json  
	override.tf  
	override.tf.json  
- файлы в название и расширение заканчиваются на _override.tf, _override.tf.json
	*_override.tf
	*_override.tf.json
- файлы с названием и расширением: .terraformrc и terraform.rc  
	.terraformrc  
	terraform.rc  
