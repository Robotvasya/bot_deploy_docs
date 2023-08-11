Быстрый старт.
==============

Общая информация.
-----------------

Данная короткая инструкция описывает основной порядок действий для уверенных пользователей ОС Linux. 

Устанавливаем телеграм бота на выделенный сервер под управлением Linux семейства Ubuntu. 
Телеграм бот будет запускаться от непривелегированного пользователя, и управляться systemd.

Для простоты будем считать, что вы уже подключились к серверу и скопировали на него исходный код своего бота.

Для других подробностей - смотрите соответствующие разделы настоящей документации.

Подготовка к запуску бота.
--------------------------

Бот будет запускаться от пользователя ``tgbot`` из папки ``/opt/my_bot``, а исходник например, лежит в ``~/bot_source``::

  adduser --system tgbot
  mkdir /opt/my_bot
  cp ~/bot_source/* /opt/my_bot
  cd /opt/my_bot
  python3.11 -m venv venv 
  source venv/bin/activate
  pip install -r requirements.txt
  deactivate
  chown -R tgbot /opt/my_bot



Запуск бота.
------------

Создаем системный юнит::

  cat > /etc/systemd/system/tgbot.service

Вставляем::

  [Unit]
  Description=Test echo Bot
  After=syslog.target
  After=network.target
  
  [Service]
  User=tgbot
  Type=simple
  WorkingDirectory=/opt/my_bot
  ExecStart=/opt/my_bot/venv/bin/python /opt/my_bot/cli.py
  Restart=on-failure
  RestartSec=5
  StartLimitBurst=5

  [Install]
  WantedBy=multi-user.target

Обновляем systemd и ставим в автозагрузку::

  systemctl daemon-reload
  systemctl enable tgbot

Запускаем::

  systemctl start tgbot
