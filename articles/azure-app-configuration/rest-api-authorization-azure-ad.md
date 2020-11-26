---
title: REST API конфигурации приложений Azure — авторизация Azure Active Directory
description: Используйте Azure Active Directory для авторизации в конфигурации приложения Azure с помощью REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0229b1941e40345f35cb7409533e54b0c4ea7d5d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182620"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Ссылка на REST API Azure Active Directory авторизации

При использовании проверки подлинности Azure Active Directory (Azure AD) авторизация обрабатывается с помощью управления доступом на основе ролей (RBAC). RBAC требует, чтобы пользователи были назначены ролям для предоставления доступа к ресурсам. Каждая роль содержит набор действий, которые могут выполнять пользователи, назначенные роли.

## <a name="roles"></a>Роли

По умолчанию в подписках Azure доступны следующие роли:

- **Владелец данных конфигурации приложения Azure**. Эта роль обеспечивает полный доступ ко всем операциям.
- **Модуль чтения данных конфигурации приложений Azure**. Эта роль включает операции чтения.

## <a name="actions"></a>Действия

Роли содержат список действий, которые могут выполнять пользователи, назначенные этой роли. Настройка приложений Azure поддерживает следующие действия.

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Это действие разрешает доступ на чтение к ресурсам с ключом конфигурации приложения, таким как/кв и/лабелс..
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Это действие разрешает доступ на запись к ресурсам "ключ — значение" конфигурации приложения.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Это действие позволяет удалять ресурсы "ключ — значение" конфигурации приложения. Обратите внимание, что удаление ресурса Возвращает ключ-значение, которое было удалено.

## <a name="error"></a>Ошибка

```http
HTTP/1.1 403 Forbidden
```

**Причина:** Участник, выполняющий запрос, не имеет необходимых разрешений для выполнения запрошенной операции.
**Решение:** Назначьте роль, необходимую для выполнения запрошенной операции с участником, выполняющим запрос.

## <a name="managing-role-assignments"></a>Управление назначениями ролей

Вы можете управлять назначениями ролей с помощью [процедур RBAC](../role-based-access-control/overview.md) , которые являются стандартными для всех служб Azure. Это можно сделать с помощью Azure CLI, PowerShell и портал Azure. Дополнительные сведения см. [в статье Добавление и удаление назначений ролей Azure с помощью портал Azure](../role-based-access-control/role-assignments-portal.md).