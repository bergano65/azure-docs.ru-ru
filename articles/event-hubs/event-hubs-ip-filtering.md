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
ms.openlocfilehash: 707290d7bf453ca71dd3c5cf8b39c917b3a1c479
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268280"
---
# <a name="use-firewall-rules"></a>Использование правил брандмауэра

Для сценариев, в которых Центры событий Azure должны быть доступны только из определенных известных сайтов, правила брандмауэра позволяют настроить правила для разрешения трафика, поступающего с определенных адресов IPv4. Например, это могут быть адреса корпоративного шлюза NAT.

## <a name="when-to-use"></a>Сценарии использования

Если вам нужно настроить пространство имен Центров событий так, чтобы оно принимало трафик от определенного диапазона IP-адресов и отклоняло весь остальной, то вы можете использовать *правило брандмауэра* для блокировки конечных точек Центров событий с других IP-адресов. Например, вы используете Центры событий с [Azure ExpressRoute][express-route] для создания частных подключений к локальной инфраструктуре.

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
> - Маршруты Центра Интернета вещей.
> - Device Explorer Центра Интернета вещей.
> - Обозреватель данных Azure
>
> В виртуальной сети должны присутствовать следующие службы Майкрософт:
> - Веб-приложения Azure.
> - Функции Azure

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Создание правила брандмауэра с использованием шаблонов Azure Resource Manager

> [!IMPORTANT]
> Правила брандмауэра поддерживаются на **стандартном** и **выделенном** уровнях Центров событий. В "базовом" уровне не поддерживается.

Следующий шаблон Resource Manager позволяет добавить правило фильтрации IP-адресов в существующее пространство имен Центров событий.

Параметры шаблона:

- Зачение **ipMask** — это один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, значение 70.37.104.0/24 в нотации CIDR представляет 256 IPv4-адресов в диапазоне от 70.37.104.0 до 70.37.104.255. Число 24 обозначает количество значимых битов префикса для адресов этого диапазона.

> [!NOTE]
> Хотя запрещающие правила отсутствуют, в шаблоне Azure Resource Manager для действия по умолчанию установлено значение **Allow**, которое не ограничивает подключения.
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

Инструкции по развертыванию шаблона см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell][lnk-deploy].

## <a name="next-steps"></a>Дополнительная информация

Инструкции по ограничению доступа к Центрам событий из виртуальных сетей Azure, см. по следующей ссылке:

- [Конечные точки служб виртуальной сети для Центров событий][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md