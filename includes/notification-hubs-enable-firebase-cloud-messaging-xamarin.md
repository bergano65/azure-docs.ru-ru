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
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162175"
---
1. Войдите в [консоль Firebase](https://firebase.google.com/console/). Создайте проект Firebase, если его еще нет.
2. После создания проекта выберите **Add Firebase to your Android app** (Добавить Firebase в приложение Android). 

    ![Добавление Firebase в приложение Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Выполните следующие действия на странице **Add Firebase to your Android app** (Добавление Firebase в приложение Android). 
    1. В поле **Android package name** (Имя пакета Android) введите имя пакета. Например, `tutorials.tutoria1.xamarinfcmapp`. 

        ![Указание имени пакета.](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Выберите **Регистрация приложения**. 
4. Выберите **Download google-services.json** (Загрузить google-services.json), сохраните файл в папку **приложения** проекта, а затем выберите **Далее**. 

    ![Загрузка файла google-services.json.](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Выберите **Далее** на странице. 
7. Выберите **Пропустить этот шаг** на странице. 

    ![Пропуск последнего шага.](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. В консоли Firebase щелкните значок шестеренки возле имени проекта. Выберите пункт **Project Settings** (Параметры проекта).

    ![Выбор параметров проекта](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Если вы еще не скачали файл **google-services.json**, это можно сделать на этой странице. 
5. Переключитесь на вкладку **Обмен сообщениями в облаке** в верхней части. 
6. Скопируйте и сохраните **Ключ сервера** для последующего использования. Это значение будет использоваться для настройки концентратора уведомлений.
