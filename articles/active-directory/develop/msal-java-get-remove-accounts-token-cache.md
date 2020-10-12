---
title: Получение & удаление учетных записей из кэша маркеров (MSAL4j) | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как просматривать и удалять учетные записи из кэша маркеров с помощью библиотеки проверки подлинности Майкрософт для Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 6267c2118b7bac9befb42102d1118faac639bf49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87312560"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Получение и удаление учетных записей из кэша маркеров с помощью MSAL для Java

MSAL для Java предоставляет кэш маркеров в памяти по умолчанию. Кэш маркеров в памяти длится в течение всего времени работы экземпляра приложения.

## <a name="see-which-accounts-are-in-the-cache"></a>Узнайте, какие учетные записи находятся в кэше

Вы можете проверить, какие учетные записи находятся в кэше, вызвав, `PublicClientApplication.getAccounts()` как показано в следующем примере:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Удаление учетных записей из кэша

Чтобы удалить учетную запись из кэша, найдите учетную запись, которую необходимо удалить, а затем вызовите ее, `PublicClientApplicatoin.removeAccount()` как показано в следующем примере:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Дополнительные сведения

Если вы используете MSAL для Java, ознакомьтесь с [сериализацией кэша пользовательской лексемы в MSAL для Java](msal-java-token-cache-serialization.md).
