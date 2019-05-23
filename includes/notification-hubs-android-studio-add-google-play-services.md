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
ms.openlocfilehash: fb27386881e89cd9056d0efccb7d3c301867bd83
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156841"
---
1. В **Android Studio** в меню выберите **Средства**, а затем — **Диспетчер пакетов SDK**. 
2. Выберите целевую версию пакета SDK для Android, которая используется в вашем проекте, и выберите **Show Package Details** (Показать свойства пакета). 

    ![Диспетчер пакетов SDK Android: выбор целевой версии](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Выберите **Google APIs** (API-интерфейсы Google), если они еще не установлены.

    ![Диспетчер пакетов SDK для Android с выбранными API-интерфейсами Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Переключитесь на вкладку **SDK Tools**. Если сервисы Google Play еще не установлены, щелкните **Сервисы Google Play**, как показано на рисунке ниже. Нажмите кнопку **Применить**, чтобы начать установку. Запишите путь к пакету SDK. Он вам потребуется в дальнейшем.

    ![Диспетчер пакетов SDK для Android с выбранными Сервисами Google Play](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Если вы видите диалоговое окно **Подтвердить изменение**, выберите **ОК**. Установщик компонентов устанавливает запрошенные компоненты. После установки компонентов выберите **Готово**.
4. Выберите **ОК**, чтобы закрыть диалоговое окно **Settings for New Projects** (Параметры для новых проектов).  
5. Откройте файл `build.gradle` в каталоге **приложений** и добавьте эту строку в `dependencies`. 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Выберите значок **Синхронизировать сейчас** на панели инструментов.

    ![Синхронизация с Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу *application* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
