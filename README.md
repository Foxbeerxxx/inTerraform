# Домашнее задание к занятию "`Введение в Terraform`" - `Татаринцев Алексей`



---

### Задание 1


1. `Terraform уже установлен проверяю версию`

![1](https://github.com/Foxbeerxxx/inTerraform/blob/main/img/img1.png)

2. `Смотрю .gitignore`
```
Файл .gitignore исключает:
*.tfstate (state-файлы)
*.tfvars (файлы с переменными)
.terraform/ (каталог с зависимостями)
Секретную информацию (пароли, ключи) можно хранить в файлах с расширением .tfvars, так как они исключены из Git.
```
3. `Запускаю terraform init`
![2](https://github.com/Foxbeerxxx/inTerraform/blob/main/img/img2.png)

4. `Затем запускаю terraform apply  и получаю вывод`
```
alexey@dell:~/inTerraform/src$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # random_password.random_string will be created
  + resource "random_password" "random_string" {
      + bcrypt_hash = (sensitive value)
      + id          = (known after apply)
      + length      = 16
      + lower       = true
      + min_lower   = 1
      + min_numeric = 1
      + min_special = 0
      + min_upper   = 1
      + number      = true
      + numeric     = true
      + result      = (sensitive value)
      + special     = false
      + upper       = true
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

random_password.random_string: Creating...
random_password.random_string: Creation complete after 0s [id=none]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
5. `В файле terraform.tfstate  секрет находится в строчке:`
```
"result": "f4lsL0KQPCP3airz", # Это секретное значение
```
![3](https://github.com/Foxbeerxxx/inTerraform/blob/main/img/img3.png)


6. `Исправленный блок `

```
resource "docker_image" "nginx" {  # дописал название
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string.result}" # Исправлено имя песурса и атребута

  ports {
    internal = 80
    external = 9090
  }
}

После чего terraform validate показывает, что все успешно.

```
![4](https://github.com/Foxbeerxxx/inTerraform/blob/main/img/img4.png)

7. `После terraform init  и terraform apply -и проверяем , что контейнер запустился`

![5](https://github.com/Foxbeerxxx/inTerraform/blob/main/img/img5.png)

8. `Меняю в коде имя контейнера name  = "hello_world" и затем выполняю команду:`

```
terraform apply  -auto-approve
Проверяю результат
```
![6](https://github.com/Foxbeerxxx/inTerraform/blob/main/img/img6.png)

```
Опасность ключа -auto-approve автоматически подтверждает изменения без проверки, что может привести к неожиданному удалению или изменению инфраструктуры. Надо ументь пользоваться, а точнее знать когда можно применять.

```

9. `Запускаю terraform destroy смотрю, что осталось в terraform.tfstate`
```
{
  "version": 4,
  "terraform_version": "1.9.8",
  "serial": 11,
  "lineage": "f1042234-fa83-5d1b-7779-4a7e3a9691da",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```

9. `Образ не удалился , потому что в документации написано `

```
Terraform Provider Docker:
keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the Docker daemon on destroy operation.

Параметр keep_locally = true предотвращает удаление образа при terraform destroy.

```
![7](https://github.com/Foxbeerxxx/inTerraform/blob/main/img/img7.png)
