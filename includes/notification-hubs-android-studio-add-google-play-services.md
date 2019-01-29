---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 01/04/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f00ca7ddf44a9d5b850cd47520970a0396a0c1b5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453102"
---
1. Откройте диспетчер пакетов SDK для Android, щелкнув значок на панели инструментов Android Studio или выбрав **Сервис** > **Android** > **SDK Manager** (Диспетчер пакетов SDK) в меню. Найдите нужную версию пакета SDK для Android, которая используется в проекте. Откройте ее, щелкнув **Show Package Details** (Показать окно свойств пакета), и выберите **Google APIs** (API-интерфейсы Google), если они еще не установлены.
2. Щелкните вкладку **Пакет инструментов SDK**. Если сервисы Google Play еще не установлены, щелкните **Google Play Services** (Сервисы Google Play), как показано ниже. Нажмите кнопку **Применить**, чтобы начать установку. Запишите путь к пакету SDK. Он вам потребуется в дальнейшем.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Откройте файл `build.gradle` в каталоге приложения.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Добавьте в `dependencies` следующую строку:

    ```text
    compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. Щелкните значок **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle) на панели инструментов.
6. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу *application* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
