---
title: Сведения об использовании эмулятора песочницы Apache Hadoop в Azure HDInsight
description: 'Чтобы начать ознакомление с экосистемой Hadoop, настройте на виртуальной машине Azure песочницу Hadoop от Hortonworks. '
keywords: эмулятор hadoop,песочница hadoop
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 074e2dd932cada5ae46ee0423dbc29fc8bc7495d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016782"
---
# <a name="get-started-with-a-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Начало работы с песочницей Apache Hadoop, эмулятором на виртуальной машине

Узнайте, как настроить на виртуальной машине песочницу Apache Hadoop от Hortonworks, чтобы ознакомиться с экосистемой Hadoop. Песочница представляет собой локальную среду разработки для ознакомления с Hadoop, распределенной файловой системой Hadoop (HDFS) и отправкой заданий. Если вы знакомы с Hadoop, вы можете начать использовать Hadoop в Azure, создав кластер HDInsight. Дополнительные сведения см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Предварительные требования
* [Oracle VirtualBox](https://www.virtualbox.org/). Скачайте и установите приложение [отсюда](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Скачивание и установка виртуальной машины
1. Перейдите на [страницу загрузок Hortonworks](https://hortonworks.com/downloads/#sandbox).

2. Щелкните **DOWNLOAD FOR VIRTUALBOX** (Файл загрузки для VirtualBox), чтобы скачать последнюю песочницу Hortonworks на виртуальную машину. Перед началом скачивания вам будет предложено зарегистрироваться в Hortonworks. Скачивание может длиться один-два часа в зависимости от скорости сети.
   
    ![Изображение ссылки для скачивания песочницы Hortonworks для VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. На этой же веб-странице щелкните ссылку **Import on Virtual Box** (Импорт в VirtualBox), чтобы скачать PDF-файл, содержащий инструкции по установке для виртуальной машины.

Чтобы скачать старую песочницу версии HDP, разверните архив:

![Архив песочницы Hortonworks](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Запуск виртуальной машины

1. Откройте Oracle VirtualBox на виртуальной машине.
2. В меню **Файл** щелкните **Import Appliance** (Импорт устройства), а затем укажите образ песочницы Hortonworks.
1. Выберете песочницу Hortonworks, щелкните **Start** (Запустить) > **Normal Start** (Обычный запуск). После завершения процесса загрузки в виртуальной машине отобразятся инструкции для входа.
   
    ![Normal Start](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Откройте веб-браузер и перейдите по отображаемому URL-адресу (как правило, это http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Задание паролей для песочницы

1. На **начальном шаге** на странице песочницы Hortonworks выберите **View Advanced Options** (Просмотр дополнительных параметров). Используйте сведения на этой странице, чтобы войти в песочницу с помощью SSH. Введите указанные имя и пароль.
   
   > [!NOTE]
   > Если клиент SSH не установлен, можно использовать веб-SSH, предоставляемый виртуальной машиной по адресу **http://localhost:4200/**.
   > 
   
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

## <a name="next-steps"></a>Дополнительная информация
* [Использование средств Azure Data Lake для Visual Studio с песочницей Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)
* [Learning the ropes of the Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop tutorial - Getting started with HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/) (Руководство по началу работы с Hadoop)

