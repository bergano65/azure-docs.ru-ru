---
title: 'Сетка событий Azure: безопасность и проверка подлинности'
description: В статье описываются служба "Сетка событий Azure" и ее основные понятия.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899286"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Авторизация доступа к ресурсам сетки событий
Сетка событий Azure позволяет контролировать уровень доступа, предоставленного разным пользователям для выполнения различных операций управления, таких как создание списка подписок на события, создание новых подписок и генерирование ключей. В службе "Сетка событий" используется управление доступом на основе ролей Azure (RBAC).

## <a name="operation-types"></a>Типы операций

Сетка событий поддерживает следующие действия:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Последние три операции возвращают потенциально секретную информацию, которая отфильтровывается из обычных операций чтения. Доступ к этим операциям рекомендуется ограничить. 

## <a name="built-in-roles"></a>Встроенные роли

"Сетка событий" предоставляет две встроенные роли для управления подписками на события. Они важны при реализации [доменов событий](event-domains.md) , поскольку они предоставляют пользователям разрешения, необходимые для подписки на разделы в домене событий. Эти роли предназначены для подписки на события и не предоставляют доступа к действиям, например для создания тем.

Вы можете [назначить эти роли для пользователя или группы](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Участник EventGrid подписки**: управление операциями подписки в сетке событий

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**Читатель EventGrid подписки**: чтение подписок на сетку событий

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Пользовательские роли

Если вам нужно указать разрешения, которые отличаются от встроенных ролей, можно создать пользовательские роли.

Далее приведены примеры определений ролей Сетки событий, позволяющих пользователям выполнять различные действия. Эти пользовательские роли отличаются от встроенных, потому что они предоставляют более широкий доступ, чем просто подписка на события.

**EventGridReadOnlyRole.json**: разрешено только для операций чтения.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: разрешен ограниченный набор действий по публикации и запрещены действия удаления.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: разрешено выполнять все действия сетки событий.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Пользовательские роли можно создавать с помощью [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md) и [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Шифрование при хранении

Все события или данные, записываемые на диск службой "Сетка событий", шифруются с помощью ключа, управляемого корпорацией Майкрософт, что гарантирует их шифрование. Кроме того, максимальный период времени, в течение которого события или данные были сохранены, составляет 24 часа в соответствии с [политикой повтора сетки событий](delivery-and-retry.md). Сетка событий будет автоматически удалять все события или данные через 24 часа или срок жизни события в зависимости от того, какое значение меньше.

## <a name="next-steps"></a>Следующие шаги

* Общие сведения о сетке событий см. в статье [Сведения о сетке событий](overview.md)
