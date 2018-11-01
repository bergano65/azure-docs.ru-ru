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
ms.openlocfilehash: 0154aac14168c9d897698a15e31b3124b208db46
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142887"
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
