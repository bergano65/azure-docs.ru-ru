---
title: REST API конфигурации приложений Azure — проверка подлинности
description: Справочные страницы по проверке подлинности с помощью REST API конфигурации приложений Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183470"
---
# <a name="authentication"></a>Проверка подлинности

Все HTTP-запросы должны пройти проверку подлинности. Поддерживаются следующие схемы проверки подлинности.

## <a name="hmac"></a>Код проверки подлинности сообщений с помощью хэш-функций

При [проверке подлинности HMAC](./rest-api-authentication-hmac.md) для подписания полезных данных запроса используется созданный случайным образом секрет. Сведения о том, как разрешены запросы, использующие этот метод проверки подлинности, можно найти в разделе [авторизации HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Проверка подлинности Azure Active Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) использует токен носителя, полученный от Azure Active Directory для проверки подлинности запросов. Сведения о том, как разрешены запросы, использующие этот метод проверки подлинности, можно найти в разделе [авторизация Azure AD](./rest-api-authorization-azure-ad.md) .