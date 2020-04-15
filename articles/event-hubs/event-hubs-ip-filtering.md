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
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: e0116c1cfe61b49f2d5aff46fab9cadc0e423ecc
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310176"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Налаживание правил IP-брандмауэра для пространства имен Azure Event Hubs
По умолчанию пространства имен Event Hubs доступны из интернета до тех пор, пока запрос поступает с достоверной проверкой подлинности и авторизацией. С помощью IP-брандмауэра его можно ограничить только набором адресов IPv4 или диапазонов адресов IPv4 в обозначении [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Эта функция полезна в сценариях, в которых концентраторы azure Event должны быть доступны только на некоторых известных сайтах. Правила брандмауэра позволяют настроить правила для приема трафика, исходиющего из определенных адресов IPv4. Например, если вы используете концентраторы событий с [Azure Express Route,][express-route]можно создать **правило брандмауэра,** чтобы разрешить трафик только с IP-адресов инфраструктуры. 

>[!WARNING]
> Включение фильтрации IP может помешать другим службам Azure взаимодействовать с концентраторами событий.
>
> Доверенные службы Майкрософт не поддерживаются, если реализованы виртуальные сети.
>
> Распространенные сценарии Azure, которые не работают с виртуальными сетями (обратите внимание, что список **НЕ** является исчерпывающим):
> - Azure Stream Analytics
> - Интеграция со службой "Сетка событий Azure".
> - Маршруты Центра Интернета вещей Azure.
> - Device Explorer Интернета вещей Azure.
>
> Следующие службы Майкрософт должны быть в виртуальной сети
> - Веб-приложения Azure
> - Функции Azure


## <a name="ip-firewall-rules"></a>Правила брандмауэра для IP-адресов
Правила IP-брандмауэра применяются на уровне пространства имен Event Hubs. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам. Любые попытки подключения с IP-адреса, который не соответствует правилу разрешения IP-адресов для пространства имен Центров событий, отклоняются. В ответе клиенту правило фильтрации IP-адресов не упоминается. Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как использовать портал Azure для создания правил IP-брандмауэра для пространства имен событий Hubs. 

1. Перейдите в **пространство имен событий концентратов** на [портале Azure.](https://portal.azure.com)
2. В левом меню выберите **опцию Networking.** При выборе опции **«Все сети»** концентратор событий принимает соединения с любого IP-адреса. Эта настройка эквивалентна правилу, которое принимает диапазон IP-адресов 0.0.0.0/0. 

    ![Брандмауэр - Выбран авоты всех сетей](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Чтобы ограничить доступ к определенным сетям и IP-адресам, выберите вариант **Избранные сети.** В разделе **Брандмауэр** выполните следующие действия:
    1. Выберите **Добавить опцию IP-адреса клиента,** чтобы предоставить вашему текущему исправному ip-адресу доступ к пространству имен. 
    2. Для **диапазона адресов**введите определенный адрес IPv4 или диапазон адреса IPv4 в обозначении CIDR. 
    3. Укажите, хотите ли вы **разрешить надежным службам Майкрософт обходить этот брандмауэр.** 

        ![Брандмауэр - Выбран авоты всех сетей](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Выберите **Сохранить** на панели инструментов, чтобы сохранить настройки. Подождите несколько минут, пока подтверждение появится в уведомлениях портала.


## <a name="use-resource-manager-template"></a>Использование шаблона Resource Manager

> [!IMPORTANT]
> Правила брандмауэра поддерживаются на **стандартном** и **выделенном** уровнях Центров событий. В "базовом" уровне не поддерживается.

Следующий шаблон Resource Manager позволяет добавить правило фильтрации IP-адресов в существующее пространство имен Центров событий.

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
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Инструкции по развертыванию шаблона см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell][lnk-deploy].

## <a name="next-steps"></a>Следующие шаги

Инструкции по ограничению доступа к Центрам событий из виртуальных сетей Azure, см. по следующей ссылке:

- [Конечные точки служб для виртуальной сети для Центров событий][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
