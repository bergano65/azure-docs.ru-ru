---
title: Получение и удаление учетных записей из кэша маркеров с помощью MSAL для Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Узнайте, как просматривать и удалять учетные записи из кэша маркеров с помощью библиотеки проверки подлинности Майкрософт для Java.
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343abd87d3b5e8b82989b8b370cef61ec6d051df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905515"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java-msal4j"></a>Получение и удаление учетных записей из кэша маркеров с помощью MSAL для Java (MSAL4j)

MSAL4J предоставляет кэш маркеров в памяти по умолчанию. Кэш маркеров в памяти длится в течение всего времени работы экземпляра приложения.

## <a name="see-which-accounts-are-in-the-cache"></a>Узнайте, какие учетные записи находятся в кэше

Чтобы проверить, какие учетные записи находятся в кэше, вызовите `PublicClientApplication.getAccounts()`, как показано в следующем примере:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Удаление учетных записей из кэша

Чтобы удалить учетную запись из кэша, найдите учетную запись, которую необходимо удалить, а затем вызовите `PublicClientApplicatoin.removeAccount()`, как показано в следующем примере:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Подробнее

Если вы используете MSAL для Java, ознакомьтесь с [сериализацией кэша пользовательской лексемы в MSAL для Java](msal-java-token-cache-serialization.md).
