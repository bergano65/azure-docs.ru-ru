---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e047ba57a61d2f327544ec795f640f5066962f6
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509908"
---
1. Войдите в [консоль Firebase](https://firebase.google.com/console/). Создайте проект Firebase, если его еще нет.
2. После создания проекта выберите **Add Firebase to your Android app** (Добавить Firebase в приложение Android). 

    ![Добавление Firebase в приложение Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Выполните следующие действия на странице **Add Firebase to your Android app** (Добавление Firebase в приложение Android): 
1. 
    1. В поле **Android package name** (Имя пакета Android) введите имя пакета. Например, `tutorials.tutoria1.xamarinfcmapp`. 

        ![Указание имени пакета.](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)

2. Выберите **Регистрация приложения**. 
1. 
1. Выберите **Download google-services.json** (Скачать Download google-services.json). Затем сохраните файл в папку **приложений** проекта и выберите **Далее**. 

    ![Загрузка файла google-services.json.](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)

6. Щелкните **Далее**. 
7. Выберите **Пропустить этот шаг**. 

    ![Пропуск последнего шага.](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)

8. В консоли Firebase щелкните значок шестеренки возле имени проекта. Выберите пункт **Project Settings** (Параметры проекта).

    ![Выбор параметров проекта](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Если вы еще не скачали файл **google-services.json**, вы можете скачать его на этой странице. 

1. Переключитесь на вкладку **Обмен сообщениями в облаке** в верхней части. 

1. Скопируйте и сохраните **Legacy Server key** (Ключ устаревшего сервера) для последующего использования. Это значение будет использоваться для настройки концентратора уведомлений.
