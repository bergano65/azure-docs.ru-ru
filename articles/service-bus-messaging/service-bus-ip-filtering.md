---
title: Правила брандмауэра Служебной шины Azure | Документация Майкрософт
description: Использование правил брандмауэра для разрешения подключений к Служебной шине Azure с определенных IP-адресов.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 540435e3e018ae77477030ae8b9f727d71782121
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704587"
---
# <a name="use-firewall-rules"></a>Использование правил брандмауэра

Для сценариев, в которых Служебная шина Azure доступна только из определенных известных расположений, правила брандмауэра позволяют настроить правила для разрешения трафика, поступающего с определенных адресов IPv4. Например, это могут быть адреса корпоративного шлюза NAT.

## <a name="when-to-use"></a>Сценарии использования

Если нужно настроить Служебную шину так, чтобы она получала трафик только из указанного диапазона IP-адресов и игнорировала любой другой трафик, можно воспользоваться *брандмауэром*, чтобы заблокировать конечные точки Служебной шины с других IP-адресов. Допустим, Служебная шина используется вместе с [Azure Express Route][express-route] для создания частных подключений к локальной инфраструктуре. 

## <a name="how-filter-rules-are-applied"></a>Применение правил фильтрации

Правила фильтрации IP-адресов применяются на уровне пространства имен Служебной шины. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам.

Любые попытки подключения с IP-адресов, которые не соответствуют правилу разрешенных IP-адресов для пространства имен Служебной шины, отклоняются. В ответе клиенту правило фильтрации IP-адресов не упоминается.

## <a name="default-setting"></a>Значение по умолчанию

По умолчанию раздел **Фильтрация IP-адресов** на портале для Служебной шины пуст. Этот означает, что по умолчанию пространство имен принимает подключения с любых IP-адресов. То есть, такое значение параметра по умолчанию равноценно правилу, которое принимает диапазон IP-адресов 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Оценка правила фильтрации IP-адресов

Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

>[!WARNING]
> Реализация правил брандмауэра может предотвратить взаимодействие других служб Azure со Служебной шиной.
>
> Доверенные службы Майкрософт не поддерживаются, если реализована фильтрация IP (правила брандмауэра). Их поддержка будет доступна в ближайшее время.
>
> Распространенные сценарии Azure, которые не работают с фильтрацией IP (обратите внимание, что список **НЕ** является исчерпывающим):
> - Azure Monitor
> - Azure Stream Analytics
> - Интеграция со службой "Сетка событий Azure".
> - Маршруты Центра Интернета вещей Azure.
> - Device Explorer Интернета вещей Azure.
> - Обозреватель данных Azure
>
> В виртуальной сети должны присутствовать следующие службы Майкрософт:
> - Служба приложений Azure
> - Функции Azure

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Создание правил виртуальной сети и брандмауэра с использованием шаблонов Azure Resource Manager

> [!IMPORTANT]
> Брандмауэры и виртуальные сети, поддерживаются только в **уровня "премиум"** уровня служебной шины.

Следующий шаблон Resource Manager позволяет добавить правило виртуальной сети в пространство имен существующей Служебной шины.

Параметры шаблона:

- Зачение **ipMask** — это один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, значение 70.37.104.0/24 в нотации CIDR представляет 256 IPv4-адресов в диапазоне от 70.37.104.0 до 70.37.104.255. Число 24 обозначает количество значимых битов префикса для адресов этого диапазона.

> [!NOTE]
> Хотя запрещающие правила отсутствуют, в шаблоне Azure Resource Manager для действия по умолчанию установлено значение **Allow**, которое не ограничивает подключения.
> При создании правил виртуальной сети или брандмауэров необходимо изменить значение параметра ***defaultAction***.
> 
> От
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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по ограничению доступа к Служебной шине из виртуальных сетей Azure, см. по следующей ссылке:

- [Use Virtual Network service endpoints with Azure Service Bus][lnk-vnet] (Использование конечных точек служб для виртуальной сети для Служебной шины Azure)

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
