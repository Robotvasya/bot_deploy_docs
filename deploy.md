# Деплой бота на сервер.

## 1. Соединение с сервером по протоколу SSH.

### Данные для аутентификации:

Для подключения к серверу вам необходимо  иметь (получить)  следующие данные:

* логин,
* пароль,
* IP адрес сервера,
* порт подключения по протоколу SSH.

Например, логин: root, пароль: my_password,  ip: 127.92.0.131, port: 22111.

### Инструменты:

Для соединения с сервером и удаленной работы с ним нам потребуются следующие инструменты, в зависимости от вашей операционной системы:

**Для Linux, MacOS:**

* Обычный встроенный  в систему терминал.

**Для Windows любой удобный вариант:**

* `WinSCP` – клиент для работы по протоколу SSH и для передачи файлов с графическим интерфейсом. Ссылка для скачивания с официального сайта:  https://winscp.net/eng/download.php  
* `PuTTY` – клиент для работы по протоколу SSH и, опционально, консольная утилита `pSCP` для передачи файлов. Ссылка для скачивания с официального сайта: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html Выбираем раздел Alternative binary – это файлы приложений, не требующих установки в систему. Скачиваем нужной разрядности x32 или x64 putty.exe – это сам клиент, и pscp.exe для передачи файлов на сервер.
* `PowerShell` встроенный SSH-клиент c помошью команды `ssh` и команды `scp` для передачи файлов. Запускается с помощью `Win+R` команда `powershell`,  или в Проводнике `shift + контекстное меню` (правая клавиша мыши) «Открыть окно Powershell здесь»

### Подключение:

Открываем терминал Linux, MacOS или Windows PowerShell и в командной строке выполняем команду 

`ssh имя_пользователя@IP_адрес -p номер_порта`:

``` 
ssh root@127.92.0.131 -p 22111
```

При первом подключении у вас запросят подтвердить обмен ключами

```
The authenticity of host '127.92.0.131 (127.92.0.131)' can't be established.
ECDSA key fingerprint is SHA256:ABCDE9vncSmuYvHi8hq+j0U8NzvjcwIvfX84tvv6S3bvvY.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Необходимо ввести `yes`. 

Затем будет предложено ввести пароль. 

```
root@127.92.0.131's password:
```

Обратите внимание, что при вводе пароля знаки и их количество не отображаются в целях безопасности. Нужно ввести / вставить пароль, а затем нажать ввод.

Появится приглашение ввода команд:

```
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.4.0-146-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 * Introducing Expanded Security Maintenance for Applications.
   Receive updates to over 25,000 software packages with your
   Ubuntu Pro subscription. Free for personal use.

     https://ubuntu.com/pro
Last login: Tue Jun 20 12:02:39 2023 from 192.168.8.7
root@bot-111:~#
```

При использовании `PuTTY` в разделе Session поле Host Name (or IP adress) вводится IP адрес и также указываем порт в поле Port.  И нажать снизу кнопку Open, для установления соединения. 
Логин и пароль нужно будет ввести после соединения с сервером.

При использовании `WinSCP` нужно нажать на кнопку  Новое соединение.. (New connection..) и так же ввести все имеющиеся учетные данные. В целях безопасности, пароль желательно не сохранять в приложении, а вводить через менеджер паролей или вручную.

Итак, если вы видите такую строку 

```
root@bot-111:~#
```
это означает `имя_пользователя@имя_сервера:путь_к_папке#`, и вы успешно вошли в систему.

## Копирование файлов проекта.

Знак `#` в конце приглашения командной строки означает, что вы суперпользователь root, а тильда `~` перед ним – что вы в домашней папке пользователя, в нашем случае root.  

По соображениям безопасности бот не должен работать от суперпользователя, поэтому потребуется дополнительная настройка, о которой речь пойдет в следующем разделе. 

Пока же, нам нужно подготовить и разложить по папкам файлы нашего бота.

Для удобства установим файловый менеджер, на пример `Midnight commander`


```
apt install mc

```

И запустим его 


```
mc
```
Находясь в этом консольном файловом менеджере мы можем переключаться между окнами интерфейса и командной строкой клавишами `Ctrl + O`.


В ubuntu сторонние приложения по соглашению находятся в папке `/opt`. Поэтому файлы бота будут скопированы в эту папку. 

Для копирования нам потребуется утилита `scp`, которая есть в Linux, MacOS и Windows PowerShell, или `pscp.exe` из проекта Putty. В случае с Putty вам необходимо открыть приложение командной строки Windows `cmd.exe` через команду выполнить `Win + R`. В WinSCP, в свою очередь, копирование файлов доступно после подключения к серверу через графический интерфейс.

Откроем новое окно терминала на локальном компьютере и перейдем в вышестоящую папку нашего проекта. Предположим, файлы нашего бота находятся на локальном компьютере в папке `c:\my_bot` или `/home/user/my_bot` (путь специально написал такой, чтоб не заморачиваться с длинной путей). Тогда нам нужно открыть терминал, и перейти  командой `cd c:\` или `cd ~`. 

Копируем папку с локального компьютера на удаленный командой `scp` `-r`(рекурсивно, то есть все файлы) `-P` (SSH порт) `путь_к_папке_my_bot` `имя_пользователя@IP_адрес_сервера` `:` `путь_к_папке_/opt`

Чтобы не было проблем с пробелами в локальном пути, его можно заключить в кавычки `"`.

```
scp -r -P 22111 "c:\my_bot" root@127.92.0.131:/opt
```
Произойдет подключение к серверу. Нужно ввести пароль и файлы скопируются.

Если пути поменять местами, то скопируется наоборот на локальный компьютер.

Возвращаемся в терминал, где открыт Midnight Commander, и проверяем что все прошло ок.

## Настройка окружения Python.

Для того, чтобы приложение бота заработало и не было конфликта с библиотеками, которые уже установлены в системе, создадим новое чистое виртуальное окружение.

Перейдем в папку /opt/my_bot и создадим чистое окружение.

```
cd /opt/my_bot
python3 -m venv venv
```

Создастся папка venv, в которой будут находиться интерпретатор, pip и библиотеки нашего проекта.  

Для установки библиотек нам необходимо активировать виртуальное окружение, обновить менеджер пакетов pip и установить все из файла зависимостей.


```
source venv/bin/activate
pip install --upgrade pip 
pip install -r requirements.txt
```

Когда все работы по настройке и проверке скрипта завершены, виртуальное окружение нужно деактивировать:


```
deactivate
```

## Настройка бота как сервиса с автозапуском

После того, как мы установили все библиотеки и проверили работоспособность бота, нам необходимо сделать автоматический запуск бота и перезапуск в случае ошибок, внезапных аварий и пр.

В целях безопасности, бот будет работать от пользователя с минимальными привилегиями. Создадим пользователя tgbot без возможности sudo и без прав логиниться в систему. 

```
adduser --system tgbot 
 
```

Отвечать за запуск и перезапуск бота, а также ротацию логов будет systemd.

Для этого нам необходимо создать новый юнит — службу для управления нашим ботом.

Создадим в нашем проекте папку systemd и файл tgbot.service в ней.

```
cat >  tgbot.service
```
Вставляем текст файла, приведенный ниже. 


```
mkdir systemd
cd systemd/
touch tgbot.service
```

Добавим в файл tgbot.service следующее содержимое:

```
[Unit]
Description=Test echo Bot
After=network.target

[Service]
User=tgbot
Group=tgbot
Type=simple
WorkingDirectory=/opt/my_bot
ExecStart=/opt/my_bot/venv/bin/python /opt/my_bot/cli.py
Restart=on-failure
RestartSec=5
StartLimitBurst=5

[Install]
WantedBy=multi-user.target

```
Не забываем в конце добавить пустую строку и нажимаем `Ctrl + D`. 

В Midnight Commander редактирование файла `F4` (при первом вызове выберите редактор mcedit). 

Обратим внимание на параметры в файле tgbot.service:

Description=Test echo Bot – это описание нашего бота.

After=network.target – это указание, что бот должен быть запущен только после того, как стартует сервис сети. Можно указывать еще, что после старта базы данных: 

```
After=mysql.service
Requires=mysql.service 
```

User=tgbot – указываем от имени какого пользователя запускать сервис

WorkingDirectory=/opt/my_bot/ – рабочая директория проекта.

ExecStart=/opt/my_bot/venv/bin/python /opt/my_bot/cli.py — здесь указываем путь к интерпретатору в нашем виртуальном окружении и через пробел путь к основному файлу бота. У меня это cli.py.

Эти параметры определяют как будет происходить перезапуск:

Restart=always – Перезапускать всегда. Может быть значение on-failure, как в моем случае.

RestartSec=5 – Запустить через 5 секунд. 

StartLimitBurst=5 –  Запустить 5 попыток.

Получившийся файл нам необходимо скопировать в папку /etc/systemd/system/

```
cp /opt/my_bot/systemd/tgbot.service /etc/systemd/system/
```

Обновляем конфигурацию systemd, чтобы он увидел новый юнит нашего сервиса. Эта команда будет нужна после каждой правки файла tgbot.service.

```
systemctl daemon-reload
```

Запускаем сервис нашего бота:

```
systemctl start tgbot
```

Проверяем, что сервис запущен и нет ошибок.

```
systemctl status tgbot 
```

Если  все прошло удачно, то выведется, что статус активен, бот запущен:  

```
root@bot-111:/opt/my_bot# systemctl status tgbot
● tgbot.service - Test echo Bot
     Loaded: loaded (/etc/systemd/system/tgbot.service; enabled; vendor preset: enabled)
    Drop-In: /run/systemd/system/service.d
             └─zzz-lxc-service.conf
     Active: active (running) since Wed 2023-06-21 11:13:16 UTC; 2h 21min ago
   Main PID: 2955 (python)
      Tasks: 2 (limit: 309168)
     Memory: 35.0M
     CGroup: /system.slice/tgbot.service
             └─2955 /opt/my_bot/venv/bin/python /opt/my_bot/cli.py

Jun 21 11:13:16 bot-103 systemd[1]: Started Test echo Bot.
```

Если ошибки есть, то они будут отображены примерно так:

```
systemctl status tgbot
● tgbot.service - Test echo Bot
     Loaded: loaded (/etc/systemd/system/tgbot.service; enabled; vendor preset: enabled)
    Drop-In: /run/systemd/system/service.d
             └─zzz-lxc-service.conf
     Active: activating (auto-restart) (Result: exit-code) since Wed 2023-06-21 10:47:29 UTC; 1s ago
    Process: 2730 ExecStart=/opt/my_bot/venv/bin/python /opt/my_bot/cli.py (code=exited, status=200/CHDIR)
   Main PID: 2730 (code=exited, status=200/CHDIR)

Jun 21 10:47:29 bot-103 systemd[2730]: tgbot.service: Failed at step CHDIR spawning /opt/bbt/venv/bin/python: No such file or directory
Jun 21 10:47:29 bot-103 systemd[1]: tgbot.service: Main process exited, code=exited, status=200/CHDIR
Jun 21 10:47:29 bot-103 systemd[1]: tgbot.service: Failed with result 'exit-code'.
```

Ошибка произошла, поскольку я ошибся в строках `WorkingDirectory=/opt/my_bot/` и `ExecStart=/opt/my_bot/venv/bin/python /opt/my_bot/cli.py`. 

Проверьте пути!

После того, как вы все настроили, бот работает и ошибок нет — сохраните рабочую версию конфигурации tgbot.service. Не забывайте после правок настроек сервиса делать релоад systemd: `systemctl daemon-reload`.

Устанавливаем бота в автозапуск:

```
systemctl enable tgbot
```

При необходимости останавливаем бота.

```
systemctl stop tgbot
```

Команда для удаления из автозагрузки:

```
systemctl disable tgbot
```

## todo:

* Добавить работу с пременными окружения
* Добавить работу с логами

