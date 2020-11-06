---
title: REST API конфигурации приложений Azure — проверка подлинности
description: Справочные страницы по проверке подлинности с помощью REST API конфигурации приложений Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424528"
---
# <a name="authentication"></a>Аутентификация

Все HTTP-запросы должны пройти проверку подлинности. Поддерживаются следующие схемы проверки подлинности.

## <a name="hmac"></a>Код проверки подлинности сообщений с помощью хэш-функций

При [проверке подлинности HMAC](./rest-api-authentication-hmac.md) для подписания полезных данных запроса используется созданный случайным образом секрет. Сведения о том, как разрешены запросы, использующие этот метод проверки подлинности, можно найти в разделе [авторизации HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Проверка подлинности Azure Active Directory (Azure AD)](/azure/active-directory/authentication/overview-authentication) использует токен носителя, полученный от Azure Active Directory для проверки подлинности запросов. Сведения о том, как разрешены запросы, использующие этот метод проверки подлинности, можно найти в разделе [авторизация Azure AD](./rest-api-authorization-azure-ad.md) .
