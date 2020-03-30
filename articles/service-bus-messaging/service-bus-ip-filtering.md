---
title: Настройте правила IP-брандмауэра для сервиса Azure Bus
description: Использование правил брандмауэра для разрешения подключений к Служебной шине Azure с определенных IP-адресов.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: a20882de34cb306b767959e21327180ff284e658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475949"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Настройте правила IP-брандмауэра для сервиса Azure Bus
По умолчанию пространства имен Service Bus доступны из интернета до тех пор, пока запрос поступает с проверкой подлинности и авторизации. С помощью IP-брандмауэра его можно ограничить только набором адресов IPv4 или диапазонов адресов IPv4 в обозначении [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Эта функция полезна в сценариях, в которых автобус Службы Azure должен быть доступен только на некоторых известных сайтах. Правила брандмауэра позволяют настроить правила для приема трафика, исходиющего из определенных адресов IPv4. Например, если вы используете сервисный автобус с [Azure Express Route,][express-route]можно создать **правило брандмауэра,** позволяющее трафикисироваться только с IP-адресов инфраструктуры, либо адресов корпоративного шлюза NAT. 

## <a name="ip-firewall-rules"></a>Правила брандмауэра для IP-адресов
Правила БРАНДМС применяются на уровне пространства имен Service Bus. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам. Любые попытки подключения с IP-адресов, которые не соответствуют правилу разрешенных IP-адресов для пространства имен Служебной шины, отклоняются. В ответе клиенту правило фильтрации IP-адресов не упоминается. Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как использовать портал Azure для создания правил IP-брандмауэра для пространства имен Service Bus. 

1. Перейдите к **месту вашего пространства имен Сервисного автобуса** на [портале Azure.](https://portal.azure.com)
2. В левом меню выберите **опцию Networking.** По умолчанию выбирается параметр **«Все сети».** Пространство имен S-Службы автобуса принимает соединения с любого IP-адреса. То есть, такое значение параметра по умолчанию равноценно правилу, которое принимает диапазон IP-адресов 0.0.0.0/0. 

    ![Брандмауэр - Выбран авоты всех сетей](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Выберите выбранный вариант **сетей** в верхней части страницы. В разделе **Брандмауэр** выполните следующие действия:
    1. Выберите **Добавить опцию IP-адреса клиента,** чтобы предоставить вашему текущему исправному ip-адресу доступ к пространству имен. 
    2. Для **диапазона адресов**введите определенный адрес IPv4 или диапазон адреса IPv4 в обозначении CIDR. 
    3. Укажите, хотите ли вы **разрешить надежным службам Майкрософт обходить этот брандмауэр.** 

        ![Брандмауэр - Выбран авоты всех сетей](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Выберите **Сохранить** на панели инструментов, чтобы сохранить настройки. Подождите несколько минут, пока подтверждение появится в уведомлениях портала.

## <a name="use-resource-manager-template"></a>Использование шаблона Resource Manager
В этом разделе есть пример шаблона Azure Resource Manager, который создает виртуальную сеть и правило брандмауэра.

> [!IMPORTANT]
> Брандмауэры и виртуальные сети поддерживаются только в **премиальном** уровне Сервисного автобуса.

Следующий шаблон Resource Manager позволяет добавить правило виртуальной сети в пространство имен существующей Служебной шины.

Параметры шаблона:

- Зачение **ipMask** — это один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, значение 70.37.104.0/24 в нотации CIDR представляет 256 IPv4-адресов в диапазоне от 70.37.104.0 до 70.37.104.255. Число 24 обозначает количество значимых битов префикса для адресов этого диапазона.

> [!NOTE]
> Хотя запрещающие правила отсутствуют, в шаблоне Azure Resource Manager для действия по умолчанию установлено значение **Разрешить**, которое не ограничивает подключения.
> При создании правил виртуальной сети или брандмауэров необходимо изменить значение параметра ***defaultAction***.
> 
> из
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
          "trustedServiceAccessEnabled": false,          
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

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
