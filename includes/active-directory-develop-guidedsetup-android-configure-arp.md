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
origin.date: 09/13/2018
ms.date: 11/05/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: b8f961ad3fe4550b915253746d0f4f677c593a8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300599"
---
## <a name="add-the-applications-registration-to-your-code"></a>Добавление регистрации приложения в код

На этом шаге вам нужно добавить в свой проект код приложения или идентификатор клиента.

1. Откройте `MainActivity` (выберите `app` > `java` > *`{host}.{namespace}`*).
2. Замените строку, начинающуюся с `final static String CLIENT_ID`, следующей:
    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
3. Откройте: `app` > `manifests` > `AndroidManifest.xml`.
4. Добавьте следующее действие в `manifest\application`. `BrowserTabActivity` позволяет корпорации Майкрософт выполнять обратный вызов приложения после завершения проверки подлинности.

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after Sign In-->
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

<!-- ms.date: 11/05/2018 -->
