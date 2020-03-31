---
title: Параметры файла cookie прокси приложения (Azure Active Directory) | Документация Майкрософт
description: Azure Active Directory (Azure AD) имеет файлы cookie доступа и сеанса для доступа к локальным приложениям через прокси-сервер приложения. В этой статье вы узнаете, как использовать и настраивать параметры файлов cookie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481370"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Параметры файлов cookie для доступа к локальным приложениям в Azure Active Directory

Azure Active Directory (Azure AD) имеет файлы cookie доступа и сеанса для доступа к локальным приложениям через прокси-сервер приложения. Узнайте, как использовать параметры файлов cookie прокси приложения. 

## <a name="what-are-the-cookie-settings"></a>Что такое параметры файлов cookie

[Прокси приложения](application-proxy.md) использует следующие параметры файлов cookie доступа и сеанса.

| Параметр файла cookie | Значение по умолчанию | Описание | Рекомендации |
| -------------- | ------- | ----------- | --------------- |
| Использовать файл cookie, предназначенный только для HTTP | **Нет** | **Да**. Позволяет прокси приложения включить флаг HTTPOnly в заголовки ответа HTTP. Этот флаг обеспечивает дополнительные преимущества для безопасности, например, это препятствует клиентскому сценарию (CSS) копировать или изменять файлы cookie.<br></br><br></br>Прежде чем мы поддержали настройки HTTP-Only, Application Proxy шифровал и передавал файлы cookie по защищенном каналу TLS для защиты от модификации. | Выберите **Да**, чтобы получить дополнительные преимущества безопасности.<br></br><br></br>Выберите **Нет** для клиентов или агентов пользователей, которым требуется доступ к файлам cookie сеанса. Например, в клиенте для RDP или MTSC, который подключается к серверу шлюза удаленных рабочих столов через прокси приложения, выберите **Нет**.|
| Использование безопасного файла cookie | **Нет** | **Да**. Позволяет прокси приложения включить флаг безопасности в заголовки ответа HTTP. Защищенный файл cookie повышает безопасность передачи по защищенному каналу TLS, такому как HTTPS. Это предотвращает несанкционированное отслеживание файлов cookie сторонними лицами из-за передачи файла cookie в виде обычного текста. | Выберите **Да**, чтобы получить дополнительные преимущества безопасности.|
| Использование постоянных файлов cookie | **Нет** | **Да**. Позволяет прокси приложения настроить действие файлов cookie доступа после закрытия веб-браузера. Сохраняемость длится до окончания срока действия маркера доступа, или пока пользователь вручную не удалит постоянные файлы cookie. | Выберите **Нет** из-за угрозы безопасности, связанной с сохранением аутентификации пользователей.<br></br><br></br>Мы рекомендуем использовать **Да** только для более старых приложений, которые не могут совместно использовать файлы cookie между процессами. Мы советуем обновить приложение, чтобы совместно использовать файлы cookie между процессами, а не применять постоянные файлы cookie. Например, вам могут потребоваться постоянные файлы cookie, чтобы разрешить открывать документы Office в представлении проводника с сайта SharePoint. Без постоянных файлов cookie эта операция может завершиться ошибкой, если в браузере, процессе проводника и процессе Office общий доступ к файлам cookie не предоставляется. |

## <a name="samesite-cookies"></a>Печенье SameSite
Начиная с версии Chrome 80 и в конечном итоге в браузерах с использованием Chromium, печенье, в котором не указан атрибут [SameSite,](https://web.dev/samesite-cookies-explained) будет рассматриваться как если бы они были установлены на **SameSite-Lax**. Атрибут SameSite объявляет, как файлы cookie должны быть ограничены контекстом одного и того же сайта. При установке на Lax, cookie только для отправки на тот же сайт запросов или навигации верхнего уровня. Тем не менее, Application Proxy требует, чтобы эти файлы cookie были сохранены в сторонних контексте, чтобы держать пользователей в надлежащем входе во время сеанса. Из-за этого мы вносим обновления в файл доступа к приложению Proxy и файлы cookie, чтобы избежать негативного воздействия этого изменения. Обновления включают в себя:

* Настройка атрибута **SameSite** на **Нет**. Это позволяет правильно отправлять файлы cookie приложений Proxy и сеансы в сторонних контексте.
* Настройка настройки **«Безопасное cookie» для** использования **Yes** по умолчанию. Chrome также требует, чтобы файлы cookie указали флаг Secure, иначе он будет отклонен. Это изменение будет применяться ко всем существующим приложениям, опубликованным через Application Proxy. Обратите внимание, что файлы cookie доступа Application Proxy всегда были настроены на безопасную и передавались только по HTTPS. Это изменение будет применяться только к файлам cookie сеанса.

Эти изменения в файлах cookie-файлов Application Proxy будут внедряться в течение следующих нескольких недель до даты выхода Chrome 80.

Кроме того, если в вашем бэк-энд-приложении есть файлы cookie, которые должны быть доступны в стороннем контексте, необходимо явно отказаться от использования приложения SameSite-None для этих файлов cookie. Прокси-сервер приложения переводит заголовок Set-Cookie в urlS и будет уважать настройки для этих файлов cookie, установленные приложением бэк-энда.



## <a name="set-the-cookie-settings---azure-portal"></a>Установка параметров файлов cookie — портал Azure
Чтобы задать параметры файла cookie с помощью портала Azure:

1. Войдите на [портал Azure](https://portal.azure.com). 
2. Перейдите к **приложениям** > **Azure Active Directory** >Enterprise **Все приложения.**
3. Выберите приложение, для которого необходимо включить параметр файла cookie.
4. Выберите **Прокси приложения**.
5. В разделе **Дополнительные параметры** установите параметр файла cookie как **Да** или **Нет**.
6. Щелкните **Сохранить**, чтобы применить изменения. 

## <a name="view-current-cookie-settings---powershell"></a>Просмотр текущих настроек cookie - PowerShell

Чтобы просмотреть текущие настройки cookie для приложения, используйте эту команду PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Настройка настроек cookie - PowerShell

В следующих командах ```<ObjectId>``` PowerShell есть ObjectId приложения. 

**Http-только печенье** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Безопасное печенье**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Стойкие файлы cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
