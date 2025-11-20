
# Лабораторная работа №2. Введение в AWS. Вычислительные сервисы


# **1. Описание лабораторной работы**

Цель работы — познакомиться с основами управления облачной инфраструктурой AWS, включая создание пользователей и политик, настройку бюджетов, запуск виртуальных машин (EC2), развёртывание веб-приложений и безопасное подключение по SSH.

---

# Постановка задачи

Необходимо выполнить следующие действия:

1. Создать IAM-группу и IAM-пользователя.
2. Настроить Zero-Spend Budget для контроля расходов.
3. Развернуть и настроить виртуальную машину EC2.
4. Запустить веб-сервер Nginx и разместить статический сайт.
5. Подключиться к виртуальной машине по SSH.
6. Развернуть сайт на PHP.
7. Остановить EC2 через AWS CLI и объяснить разницу между Stop и Terminate.

# Цель и основные этапы работы

**Цель:** получить первичные навыки работы с AWS и научиться разворачивать веб-приложение в облаке.

**Основные этапы:**

1. Работа с IAM.
2. Настройка бюджета для предотвращения расходов.
3. Развёртывание EC2.
4. Установка ПО через User Data.
5. Подключение по SSH.
6. Загрузка сайта и настройка PHP.
7. Остановка EC2.

---

# **2. Практическая часть**
## Задание 0 — Создание AWS аккаунта и выбор региона

1. Я перешла на официальный сайт aws.amazon.com и нажала кнопку “Create an AWS Account”.
2. Я выбрала Personal account, после чего заполнила контактные данные и подтвердила свою электронную почту и номер телефона. Также я указала банковскую карту для активации Free Tier — система списала небольшую сумму для проверки, и позже она была возвращена.
<img width="461" height="744" alt="Image" src="https://github.com/user-attachments/assets/e567703e-6136-4f3b-bb2d-ea9165959907" />
3. После завершения регистрации я вошла в консоль AWS под root-пользователем. Это требуется только на этапе подготовки аккаунта.
4. Далее я вручную выбрала рабочий регион AWS — EU (Frankfurt) / eu-central-1, который используется в учебных материалах и полностью поддерживает Free Tier.
<img width="597" height="1272" alt="Image" src="https://github.com/user-attachments/assets/dcf82a2e-a3d9-4c76-94c6-ecf03c054df1" />
5. После входа я убедилась, что консоль загружается корректно и регион выбран правильно. На этом подготовка среды была завершена.


---

## Задание 1 — Создание IAM-группы и пользователя

1. Открыла сервис IAM.
2. Создала группу **Admins**. <img width="1546" height="127" alt="Image" src="https://github.com/user-attachments/assets/b5961d83-9151-4b81-8971-48b4b547609a" />
3. Привязала политику **AdministratorAccess**. <img width="1537" height="301" alt="Image" src="https://github.com/user-attachments/assets/297988f3-9096-4c7c-b66c-2b439a365580" />
**Ответ: Что делает политика AdministratorAccess?**
Даёт полный доступ ко всем сервисам AWS.
4. Создала IAM-пользователя **cloudstudent**, разрешила вход в консоль. <img width="1553" height="107" alt="Image" src="https://github.com/user-attachments/assets/1db645d3-ec22-4efb-bbad-a2730c0d446c" />
5. Вышла из root-аккаунта и вошла под IAM-пользователем.
---

## **Задание 2 — Создание Zero Spend Budget**

1. Открыла Billing → Budgets.
2. Выбрала тип **Zero-spend budget**.
3. Настроила email-уведомления.
4. Создала бюджет.

<img width="1564" height="709" alt="Image" src="https://github.com/user-attachments/assets/519ea0f5-4ec6-45d9-a5a6-4a9eac715f57" />

---

## **Задание 3 — Создание и запуск EC2 инстанса**

1. В разделе **Instances → Launch instances** начато создание новой виртуальной машины.

Были выбраны параметры:

- **Name:** `webserver`
- **AMI:** Amazon Linux 2023 AMI  
- **Instance type:** `t3.micro` (Free Tier) <img width="512" height="434" alt="Image" src="https://github.com/user-attachments/assets/47709d03-626a-406a-9d84-a7f61b2c4536" />
- **Key pair:** создан новый ключ  
  - название: `marina-keypair`
  - тип: RSA  
  - формат: `.pem`
 <img width="642" height="45" alt="Image" src="https://github.com/user-attachments/assets/7d31b6af-ae7b-4da0-87e2-47cd8836977a" />

 2. Создала security group:

   * SSH: My IP
   * HTTP: 0.0.0.0/0

3. Вставила User Data:

```
#!/bin/bash
dnf -y update
dnf -y install htop
dnf -y install nginx
systemctl enable nginx
systemctl start nginx
```

**Вопрос: зачем User Data?**
Ответ : User Data позволяет выполнять команды автоматически при первом запуске виртуальной машины (применяется для автоматизации настройки).
**Вопрос: Вопрос: зачем nginx?**
Ответ : Это веб-сервер, который обслуживает HTTP-запросы.

---

## **Задание 4 — Логирование и мониторинг (кратко)**

В этом задании я изучила основные инструменты мониторинга и диагностики EC2-инстанса.

1. Status checks

Перешла во вкладку Status checks и убедилась, что обе проверки прошли успешно:

System reachability check — Passed

Instance reachability check — Passed

Это означает, что инфраструктура AWS работает корректно, и операционная система запущена без ошибок.
<img width="622" height="313" alt="Image" src="https://github.com/user-attachments/assets/4db0ec5a-6083-4f7d-bec5-ebcfe2a4c34e" />

2. Monitoring

Во вкладке Monitoring просмотрела метрики CloudWatch: загрузку CPU, сетевую активность и другие графики.
Так как сервер почти не нагружен, показатели низкие.
<img width="1556" height="492" alt="Image" src="https://github.com/user-attachments/assets/fe859b85-5e62-439b-a95b-aac8507cdac6" />

3. System Log

Через Actions → Monitor and troubleshoot → Get system log открыла системный лог.
Там увидела строки:
запуск cloud-init
установку пакетов, включая nginx
сообщения об успешной загрузке ОС
Это подтверждает, что User Data-скрипт выполнился корректно.
<img width="1866" height="760" alt="Image" src="https://github.com/user-attachments/assets/bb9a79d0-21d1-496f-b6e7-8041d0b5b768" />

4. Instance Screenshot

Через Actions → Monitor and troubleshoot → Get instance screenshot получила снимок виртуальной консоли.
На нём видно, что сервер успешно загрузился в Amazon Linux.

<img width="862" height="761" alt="Image" src="https://github.com/user-attachments/assets/305d5497-cc0b-43ed-8789-d32689d3373c" />

---

## **Задание 5 — Подключение по SSH**

Для подключения к моему EC2-инстансу я использовала приватный ключ, который создала при запуске виртуальной машины.


### **1. Подготовка ключа**

Сначала я перешла в папку, где хранится мой файл ключа `marina-keypair.pem`.
Затем я выполнила команду для подключения по SSH:

```bash
ssh -i marina-keypair.pem ec2-user@<Public-IP>
```

Где `<Public-IP>` — это публичный IP-адрес моего инстанса, который я взяла в консоли AWS EC2.

---

### **2. Подключение к инстансу**

После выполнения команды я увидела приглашение командной строки:

```
[ec2-user@ip-xx-xx-xx-xx ~]$
```

Это значит, что я успешно подключилась к серверу.
Я оказалась в домашней директории пользователя **ec2-user**, который используется в Amazon Linux по умолчанию.


---

### **3. Проверка работы Nginx**

Чтобы убедиться, что веб-сервер Nginx действительно был установлен и запущен через User Data скрипт, я выполнила команду:

```bash
systemctl status nginx
```

Сервис находился в состоянии:

```
active (running)
```

Это подтверждает корректную установку и запуск веб-сервера.

<img width="962" height="1036" alt="Image" src="https://github.com/user-attachments/assets/78b45544-5986-4b73-8f08-ac785bf123ce" />

---

### **4. Ответ на контрольный вопрос**

**Почему в AWS нельзя использовать пароль для SSH?**

В AWS вход по паролю отключён специально — из соображений безопасности.
Пароль можно подобрать, но SSH-ключ состоит из длинной криптографической последовательности, которую невозможно взломать перебором.

Поэтому AWS использует **только вход по SSH-ключам**, так как это намного безопаснее.

---

## ** Задание 6a — Развёртывание статического сайта**

1. Создала файлы:

* index.html
* about.html
* contact.html
<img width="655" height="212" alt="Image" src="https://github.com/user-attachments/assets/88a6a5f0-4a01-4ef4-aa33-94c598818533" />
2. Скопировала на сервер:

```
scp -i marina-keypair.pem index.html ec2-user@IP:/tmp
scp -i marina-keypair.pem about.html ec2-user@IP:/tmp
scp -i marina-keypair.pem contact.html ec2-user@IP:/tmp
```
<img width="962" height="251" alt="Image" src="https://github.com/user-attachments/assets/2e9cd652-f4ae-4f21-9823-83cfc44828a3" />

3. На сервере переместила:

```
sudo cp /tmp/*.html /usr/share/nginx/html/
```

4. Проверила в браузере:

```
http://IP
```

<img width="962" height="554" alt="Image" src="https://github.com/user-attachments/assets/34cf5ea0-83fe-494a-96df-fd7a6a5c67d1" />

---

## **Задание 6b — Развёртывание сайта на PHP**

1. Создала структуру php-site с файлами:

* index.php
* about.php
* contact.php

2. Скопировала:

```
scp -i marina-keypair.pem -r php-site/* ec2-user@IP:/tmp
```

3. На сервере:

```
sudo cp /tmp/*.php /usr/share/nginx/html/
```
<img width="962" height="302" alt="Image" src="https://github.com/user-attachments/assets/6e515685-83bb-42c9-a3cc-d276812fb086" />

4. Установила PHP:

```
sudo dnf -y install php php-fpm
sudo systemctl enable php-fpm
sudo systemctl start php-fpm
```

5. Создала конфиг mywebsite.conf и загрузила в:

```
/etc/nginx/conf.d/
```

6. Проверила:

```
sudo nginx -t
sudo systemctl restart nginx
```
<img width="962" height="183" alt="Image" src="https://github.com/user-attachments/assets/600e7acb-3caf-46a3-b451-4078de6cf9b2" />
7. Открыла сайт:

```
http://IP/index.php
```

<img width="962" height="373" alt="Image" src="https://github.com/user-attachments/assets/ee1092a3-c1b8-416c-a010-030d1fb55559" />

---

# **Задание 7 — Остановка EC2 через CLI**

### Подключилась в AWS CloudShell

Выполнила команду:

```
aws ec2 stop-instances --instance-ids i-03ee2abb6e102d247
```

### Ответ: чем Stop отличается от Terminate?

| Stop                             | Terminate                     |
| -------------------------------- | ----------------------------- |
| Инстанс останавливается          | Инстанс удаляется навсегда    |
| Можно запустить снова            | Запустить снова невозможно    |
| Диск EBS сохраняется             | Диск EBS удаляется            |
| Деньги за compute не списываются | Ресурс полностью уничтожается |

<img width="962" height="571" alt="Image" src="https://github.com/user-attachments/assets/69364214-d99d-4ade-bfb2-10e9a98c253f" />

---

# **5. Список использованных источников**

1. AWS Documentation — [https://docs.aws.amazon.com](https://docs.aws.amazon.com)
2. Руководство Amazon EC2 — [https://docs.aws.amazon.com/ec2/](https://docs.aws.amazon.com/ec2/)
3. Учебные материалы по AWS (предоставленные преподавателем)

---

# **6. Вывод**

В ходе лабораторной работы я:

* Создала IAM-пользователя и группу.
* Настроила бюджет для защиты от расходов.
* Запустила виртуальную машину EC2 и автоматически настроила её через User Data.
* Установила и проверила работу веб-сервера Nginx.
* Подключилась по SSH с использованием ключей.
* Развернула статический сайт и сайт на PHP.
* Освоила работу с CloudWatch и логами.
* Остановила EC2 через командную строку.

Получены ключевые навыки по работе с AWS, достаточные для развёртывания базового веб-приложения в облаке.


