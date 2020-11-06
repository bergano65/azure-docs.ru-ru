---
title: REST API конфигурации приложений Azure — авторизация Azure Active Directory
description: Используйте Azure Active Directory для авторизации в конфигурации приложения Azure с помощью REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424410"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Ссылка на REST API Azure Active Directory авторизации

При использовании проверки подлинности Azure Active Directory (Azure AD) авторизация обрабатывается с помощью управления доступом на основе ролей (RBAC) Azure. Для предоставления доступа к ресурсам Azure RBAC необходимо назначить пользователям роли. Каждая роль содержит набор действий, которые могут выполнять пользователи, назначенные роли.

## <a name="roles"></a>Роли

Следующие роли являются встроенными ролями, которые по умолчанию доступны в подписках Azure:

- **Владелец данных конфигурации приложения Azure**. Эта роль обеспечивает полный доступ ко всем операциям.
- **Модуль чтения данных конфигурации приложений Azure**. Эта роль включает операции чтения.

## <a name="actions"></a>Действия

Роли содержат список действий, которые могут выполнять пользователи, назначенные этой роли. Настройка приложений Azure поддерживает следующие действия.

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Это действие разрешает доступ на чтение к ресурсам с ключом конфигурации приложения, таким как/кв и/лабелс..
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Это действие разрешает доступ на запись к ресурсам "ключ — значение" конфигурации приложения.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Это действие позволяет удалять ресурсы "ключ — значение" конфигурации приложения. Обратите внимание, что удаление ресурса Возвращает ключ-значение, которое было удалено.

## <a name="errors"></a>ошибки

```http
HTTP/1.1 403 Forbidden
```

**Причина:** Участник, выполняющий запрос, не имеет необходимых разрешений для выполнения запрошенной операции.
**Решение:** Назначьте роль, необходимую для выполнения запрошенной операции с участником, выполняющим запрос.

## <a name="managing-role-assignments"></a>Управление назначениями ролей

Управление назначениями ролей выполняется с помощью процедур [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) , которые являются стандартными для всех служб Azure. Это можно сделать с помощью Azure CLI, PowerShell, портал Azure и других средств. Официальная документация о том, как создавать назначения ролей, можно найти [здесь](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
