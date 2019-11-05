---
title: Правила брандмауэра Центров событий Azure | Документация Майкрософт
description: Использование правил брандмауэра для разрешения подключений к Центрам событий Azure с определенных IP-адресов.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: f96c25dbb85ed92141636487f10d861a8c5e5f28
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468441"
---
# <a name="use-firewall-rules"></a>Использование правил брандмауэра

Для сценариев, в которых Центры событий Azure должны быть доступны только из определенных известных сайтов, правила брандмауэра позволяют настроить правила для разрешения трафика, поступающего с определенных адресов IPv4. Например, это могут быть адреса корпоративного шлюза NAT.

## <a name="when-to-use"></a>Сценарии использования

Если нужно настроить пространство имен Центров событий так, чтобы оно получало трафик только из указанного диапазона IP-адресов и игнорировало любой другой трафик, можно воспользоваться *правилом брандмауэра*, чтобы заблокировать конечные точки Центров событий с других IP-адресов. Например, если вы используете концентраторы событий с помощью [Azure Express Route][express-route], можно создать *правило брандмауэра* , ограничивающее трафик от локальных IP-адресов инфраструктуры.

## <a name="how-filter-rules-are-applied"></a>Применение правил фильтрации

Правила фильтрации IP-адресов применяются на уровне пространства имен Центров событий. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам.

Любые попытки подключения с IP-адреса, который не соответствует правилу разрешения IP-адресов для пространства имен Центров событий, отклоняются. В ответе клиенту правило фильтрации IP-адресов не упоминается.

## <a name="default-setting"></a>Значение по умолчанию

По умолчанию раздел **Фильтрация IP-адресов** на портале для Центров событий пуст. Этот означает, что по умолчанию концентратор событий принимает подключения с любых IP-адресов. То есть, такое значение параметра по умолчанию равноценно правилу, которое принимает диапазон IP-адресов 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Оценка правила фильтрации IP-адресов

Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

>[!WARNING]
> Реализация брандмауэров может предотвратить взаимодействие с Центрами событий других служб Azure.
>
> Доверенные службы Майкрософт не поддерживаются, если реализована фильтрация IP (брандмауэры). Их поддержка будет доступна в ближайшее время.
>
> Распространенные сценарии Azure, которые не работают с фильтрацией IP (обратите внимание, что список **НЕ** является исчерпывающим):
> - Azure Monitor
> - Azure Stream Analytics
> - Интеграция со службой "Сетка событий Azure".
> - Маршруты Центра Интернета вещей Azure.
> - Device Explorer Интернета вещей Azure.
>
> В виртуальной сети должны присутствовать следующие службы Майкрософт:
> - Веб-приложения Azure.
> - Функции Azure

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Создание правила брандмауэра с использованием шаблонов Azure Resource Manager

> [!IMPORTANT]
> Правила брандмауэра поддерживаются на **стандартном** и **выделенном** уровнях Центров событий. В "базовом" уровне не поддерживается.

Следующий шаблон Resource Manager позволяет добавить правило фильтрации IP-адресов в существующее пространство имен Центров событий.

Параметры шаблона.

- Зачение **ipMask** — это один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, значение 70.37.104.0/24 в нотации CIDR представляет 256 IPv4-адресов в диапазоне от 70.37.104.0 до 70.37.104.255. Число 24 обозначает количество значимых битов префикса для адресов этого диапазона.

> [!NOTE]
> Хотя запрещающие правила отсутствуют, в шаблоне Azure Resource Manager для действия по умолчанию установлено значение **Разрешить**, которое не ограничивает подключения.
> При создании правил виртуальной сети или брандмауэров необходимо изменить значение параметра ***defaultAction***.
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> значение
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Чтобы развернуть шаблон, следуйте инструкциям по [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по ограничению доступа к Центрам событий из виртуальных сетей Azure, см. по следующей ссылке:

- [Конечные точки службы виртуальной сети для концентраторов событий][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
