---
title: REST API конфигурации приложений Azure — авторизация
description: Справочные страницы по авторизации с помощью REST API конфигурации приложений Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932648"
---
# <a name="authorization"></a>Авторизация

Авторизация относится к процедуре, используемой для определения разрешений, которые вызывающий объект при выполнении запроса. Существует несколько моделей авторизации. Модель авторизации, используемая для запроса, зависит от используемого метода [проверки подлинности](./rest-api-authentication-index.md) . Ниже перечислены модели авторизации.

## <a name="hmac"></a>Код проверки подлинности сообщений с помощью хэш-функций

Модель [модели авторизации](./rest-api-authorization-hmac.md) , связанная с проверкой подлинности HMAC, разделяет разрешения на доступ только для чтения или на чтение и запись. Дополнительные сведения см. на странице [авторизации HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Модель авторизации](./rest-api-authorization-azure-ad.md) , связанная с проверкой подлинности Azure Active Directory (Azure AD), использует Azure RBAC для управления разрешениями. Дополнительные сведения см. на странице [авторизации Azure AD](./rest-api-authorization-azure-ad.md) .
