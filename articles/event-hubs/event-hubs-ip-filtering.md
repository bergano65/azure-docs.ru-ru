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
ms.openlocfilehash: 18212726f0ab921a05a3b640a32754c62958d047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393147"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Настройка правил брандмауэра IP-адресов для пространства имен концентраторов событий Azure
По умолчанию пространства имен концентраторов событий доступны из Интернета при условии, что запрос поступает с действительной проверкой подлинности и авторизацией. С помощью IP-брандмауэра вы можете ограничить его более ограниченным набором адресов IPv4 или диапазонами IPv4-адресов в нотации [CIDR (без класса для маршрутизации между доменами)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Эта функция полезна в сценариях, в которых концентраторы событий Azure должны быть доступны только из определенных хорошо известных сайтов. Правила брандмауэра позволяют настроить правила приема трафика, исходящего с конкретных IPv4-адресов. Например, если вы используете концентраторы событий с [Azure Express Route][express-route], можно создать **правило брандмауэра** , разрешающее трафик только с локальных IP-адресов инфраструктуры. 

>[!WARNING]
> Включение фильтрации IP-адресов может препятствовать взаимодействию других служб Azure с концентраторами событий.
>
> Доверенные службы Майкрософт не поддерживаются, если реализованы виртуальные сети.
>
> Распространенные сценарии Azure, которые не работают с виртуальными сетями (обратите внимание, что список **НЕ** является исчерпывающим):
> - Azure Monitor (параметр диагностики)
> - Azure Stream Analytics
> - Интеграция со службой "Сетка событий Azure".
> - Маршруты Центра Интернета вещей Azure.
> - Device Explorer Интернета вещей Azure.
>
> Следующие службы Майкрософт должны находиться в виртуальной сети
> - Веб-приложения Azure
> - Функции Azure


## <a name="ip-firewall-rules"></a>Правила брандмауэра для IP-адресов
Правила брандмауэра IP-адресов применяются на уровне пространства имен концентраторов событий. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам. Любые попытки подключения с IP-адреса, который не соответствует правилу разрешения IP-адресов для пространства имен Центров событий, отклоняются. В ответе клиенту правило фильтрации IP-адресов не упоминается. Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как использовать портал Azure для создания правил брандмауэра IP-адресов для пространства имен концентраторов событий. 

1. Перейдите к **пространству имен концентраторов событий** в [портал Azure](https://portal.azure.com).
2. В меню слева выберите пункт Параметры **сети** . Если выбран параметр **все сети** , концентратор событий принимает подключения с любого IP-адреса. Этот параметр эквивалентен правилу, принимающему диапазон IP-адресов 0.0.0.0/0. 

    ![Брандмауэр — выбран параметр "все сети"](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Чтобы ограничить доступ к определенным сетям и IP-адресам, выберите вариант **Выбранные сети** . В разделе **брандмауэр** выполните следующие действия.
    1. Установите флажок **Добавить IP-адрес клиента** , чтобы предоставить текущему клиенту IP доступ к пространству имен. 
    2. В качестве **диапазона адресов**введите конкретный IPv4-адрес или диапазон IPv4-адресов в нотации CIDR. 
    3. Укажите, следует ли **разрешить доверенным службам Майкрософт обходить этот брандмауэр**. 

        ![Брандмауэр — выбран параметр "все сети"](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Нажмите кнопку **сохранить** на панели инструментов, чтобы сохранить параметры. Подождите несколько минут, пока не отобразится подтверждение уведомления на портале.


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

## <a name="next-steps"></a>Дальнейшие шаги

Инструкции по ограничению доступа к Центрам событий из виртуальных сетей Azure, см. по следующей ссылке:

- [Конечные точки служб для виртуальной сети для Центров событий][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
