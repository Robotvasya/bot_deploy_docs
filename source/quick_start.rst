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

Бот будет запускаться от пользователя ``tgbot`` из папки ``/opt/my_bot``, а исходник например, лежит в ``~/bot_source``:

.. code-block:: bash

  adduser --system tgbot

.. code-block:: bash

  mkdir /opt/my_bot

.. code-block:: bash

  cp ~/bot_source/* /opt/my_bot

.. code-block:: bash

  cd /opt/my_bot

.. code-block:: bash

  python3.11 -m venv venv 

.. code-block:: bash

  source venv/bin/activate

.. code-block:: bash

  pip install -r requirements.txt

.. code-block:: bash

  deactivate

.. code-block:: bash

  chown -R tgbot /opt/my_bot


Запуск бота.
------------

Создаем системный юнит:

.. code-block:: bash

  cat > /etc/systemd/system/tgbot.service

Вставляем:

.. code-block:: ini

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

Обновляем systemd и ставим в автозагрузку:

.. code-block:: bash

  systemctl daemon-reload

.. code-block:: bash

  systemctl enable tgbot

Запускаем:

.. code-block:: bash

  systemctl start tgbot
