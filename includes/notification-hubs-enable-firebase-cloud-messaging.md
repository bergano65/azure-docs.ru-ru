---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5f919a04b47aa6fdef9500f3d7e6bef4ddaa239e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140296"
---
1. Войдите в [консоль Firebase](https://firebase.google.com/console/). Создайте проект Firebase, если его еще нет.
2. После создания проекта выберите **Add Firebase to your Android app** (Добавить Firebase в приложение Android). 

    ![Добавление Firebase в приложение Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Выполните следующие действия на странице **Add Firebase to your Android app** (Добавление Firebase в приложение Android). 
    1. Для **Имя пакета Android** скопируйте свое значение **applicationId** в файл приложения **build.gradle**. В нашем примере он выглядит следующим образом: `com.fabrikam.fcmtutorial1app`. 

        ![Указание имени пакета.](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Выберите **Регистрация приложения**. 
4. Выберите **Download google-services.json** (Загрузить google-services.json), сохраните файл в папку **приложения** проекта, а затем выберите **Далее**. 

    ![Загрузка файла google-services.json.](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Внесите следующие **изменения конфигурации** в проект в Android Studio. 
    1.  В файл **build.gradle уровня проекта** (&lt;project&gt;/build.gradle) добавьте следующий текст в раздел **зависимости**. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. В файл **build.gradle уровня приложения** (&lt;project&gt;/&lt;app-module&gt;/build.gradle) добавьте следующий текст в раздел **зависимости**. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Добавьте следующую строку в конец файла **app-level build.gradle** после раздела с зависимостями. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. На панели инструментов щелкните **Синхронизировать сейчас***. 
 
        ![Изменения конфигурации build.gradle.](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Выберите **Далее** на странице. 
7. Выберите **Пропустить этот шаг** на странице. 

    ![Пропуск последнего шага.](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. В консоли Firebase щелкните значок шестеренки возле имени проекта. Выберите пункт **Project Settings** (Параметры проекта).

    ![Выбор параметров проекта](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Если вы еще не скачали файл **google-services.json** в папку **app** проекта Android Studio, это можно сделать на этой странице. 
5. Переключитесь на вкладку **Обмен сообщениями в облаке** в верхней части. 
6. Скопируйте и сохраните **Ключ сервера** для последующего использования. Это значение будет использоваться для настройки концентратора уведомлений.
