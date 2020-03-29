---
title: Получите & удалить учетные записи из кэша маркеров (MSAL4j) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как просматривать и удалять учетные записи из кэша маркеров, используя библиотеку аутентификации Майкрософт для Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696203"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Получить и удалить учетные записи из кэша маркеров с помощью MSAL для Java

MSAL для Java обеспечивает кэш маркера в памяти по умолчанию. Кэш маркера в памяти длится время экземпляра приложения.

## <a name="see-which-accounts-are-in-the-cache"></a>Посмотреть, какие учетные записи находятся в кэше

Вы можете проверить, какие учетные `PublicClientApplication.getAccounts()` записи находятся в кэше, позвонив, как показано в следующем примере:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Удаление учетных записей из кэша

Чтобы удалить учетную запись из кэша, найдите учетную запись, которую необходимо удалить, а затем вызов, `PublicClientApplicatoin.removeAccount()` как показано в следующем примере:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Дополнительные сведения

Если вы используете MSAL для Java, узнайте о [сериализации пользовательских кэша токенов в MSAL для Java.](msal-java-token-cache-serialization.md)
