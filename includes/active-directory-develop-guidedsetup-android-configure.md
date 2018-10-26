---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7ff04789a4ba5e5a689b3d3815852bc0fbcdc6a7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988373"
---
## <a name="register-your-application"></a>Регистрация приложения

Приложение можно зарегистрировать одним из двух способов, которые описаны в следующих двух разделах.

### <a name="option-1-express"></a>Вариант 1. Экспресс-способ

1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2. В поле **Имя приложения** введите имя приложения.
3. Обязательно установите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.
4. Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="option-2-advanced"></a>Вариант 2. Расширенный способ

1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app).
2. В поле **Имя приложения** введите имя приложения.
3. Обязательно снимите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.
4. Выберите **Добавление платформы**, **Собственное приложение**, а затем нажмите кнопку **Сохранить**.
5. В разделе **app** > **java** > **{узел}.{пространство_имен}** откройте `MainActivity`. 
6. Замените заполнитель *[Enter application Id here]* идентификатором приложения или клиента.

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. В разделе **app** > **manifests** откройте файл *AndroidManifest.xml*.
8. В узел `manifest\application` добавьте указанное ниже действие. Действие `BrowserTabActivity` позволяет корпорации Майкрософт выполнять обратный вызов приложения после завершения проверки подлинности.

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. Замените заполнитель `[Enter the application Id here]` идентификатором приложения или клиента в `BrowserTabActivity`.
