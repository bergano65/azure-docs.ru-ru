---
title: Получение & удаление учетных записей из кэша маркеров (MSAL4j) | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как просматривать и удалять учетные записи из кэша маркеров с помощью библиотеки проверки подлинности Майкрософт для Java.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2f800d8d8665667c51ee57e3fe1c556c3a2d8eb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424343"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Получение и удаление учетных записей из кэша маркеров с помощью MSAL для Java

MSAL для Java предоставляет кэш маркеров в памяти по умолчанию. Кэш маркеров в памяти длится в течение всего времени работы экземпляра приложения.

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

## <a name="learn-more"></a>Подробнее…

Если вы используете MSAL для Java, ознакомьтесь с [сериализацией кэша пользовательской лексемы в MSAL для Java](msal-java-token-cache-serialization.md).
