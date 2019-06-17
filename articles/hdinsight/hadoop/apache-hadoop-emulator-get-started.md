---
title: Узнайте, с помощью эмулятора HDInsight Azure песочницы Apache Hadoop
description: 'Чтобы начать ознакомление с экосистемой Hadoop, настройте на виртуальной машине Azure песочницу Hadoop от Hortonworks. '
keywords: эмулятор hadoop,песочница hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 2cb99cfe765e1d3444f362e591812f5088c78c0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393149"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Начало работы с изолированной Apache Hadoop, эмулятором на виртуальной машине

Узнайте, как настроить на виртуальной машине песочницу Apache Hadoop от Hortonworks, чтобы ознакомиться с экосистемой Hadoop. Песочница представляет собой локальную среду разработки для ознакомления с Hadoop, распределенной файловой системой Hadoop (HDFS) и отправкой заданий. Если вы знакомы с Hadoop, вы можете начать использовать Hadoop в Azure, создав кластер HDInsight. Дополнительные сведения см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Технические условия
* [Oracle VirtualBox](https://www.virtualbox.org/). Скачайте и установите приложение [отсюда](https://www.virtualbox.org/wiki/Downloads).


## <a name="download-and-install-the-virtual-machine"></a>Скачивание и установка виртуальной машины
1. Перейдите к [Cloudera загружает](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

2. Нажмите кнопку **VIRTUALBOX** под **Выбор типа установки** скачать последнюю песочницу Hortonworks на виртуальную Машину. Вход или заполните форму процент продукта.

1. Нажмите кнопку **"песочницы" (последняя версия HDP)** чтобы начать загрузку.

Инструкции по настройке песочницы см. в разделе ["песочницы" развертывания и руководстве по установке](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Чтобы скачать старую песочницу версии HDP, см. по ссылкам в разделе **более старых версий**.

## <a name="start-the-virtual-machine"></a>Запуск виртуальной машины

1. Откройте Oracle VirtualBox на виртуальной машине.
2. В меню **Файл** щелкните **Import Appliance** (Импорт устройства), а затем укажите образ песочницы Hortonworks.
1. Выберете песочницу Hortonworks, щелкните **Start** (Запустить) > **Normal Start** (Обычный запуск). После завершения процесса загрузки в виртуальной машине отобразятся инструкции для входа.

    ![Normal Start](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Откройте веб-браузер и перейдите по отображаемому URL-АДРЕСУ (обычно `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Задание паролей для песочницы

1. На **начальном шаге** на странице песочницы Hortonworks выберите **View Advanced Options** (Просмотр дополнительных параметров). Используйте сведения на этой странице, чтобы войти в песочницу с помощью SSH. Введите указанные имя и пароль.

   > [!NOTE]
   > Если клиент SSH не установлен, можно использовать веб-SSH, предоставляемый виртуальной машиной по адресу **http://localhost:4200/** .

    При первом подключении с помощью SSH вам будет предложено изменить пароль для учетной записи root. Введите новый пароль, используемый при входе с помощью SSH.

2. После входа в систему введите следующую команду:

        ambari-admin-password-reset

    При появлении запроса укажите пароль для учетной записи администратора Ambari. Он используется при получении доступа к веб-интерфейсу Ambari.

## <a name="use-hive-commands"></a>Использование команд Hive

1. Используя подключение SSH к песочнице, запустите оболочку Hive с помощью следующей команды:

        hive
2. После запуска оболочки просмотрите таблицы, представленные в песочнице, используя следующую команду:

        show tables;
3. Используйте следующую команду, чтобы извлечь 10 строк из таблицы `sample_07` :

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Дальнейшие действия
* [Использование средств Azure Data Lake для Visual Studio с песочницей Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)
* [Learning the ropes of the Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop tutorial - Getting started with HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/) (Руководство по началу работы с Hadoop)

