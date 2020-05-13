---
title: Брандмауэр конгигуре IP для пространства имен Azure Relay
description: В этой статье описывается, как использовать правила брандмауэра, чтобы разрешить подключения с конкретных IP-адресов к Azure Relay пространствам имен.
services: service-bus-relay
documentationcenter: ''
author: spelluru
ms.service: service-bus-relay
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: e2a9e89ca8c3b77b09a58a45959f1acda1457b56
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211058"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Настройка брандмауэра IP для пространства имен Azure Relay
По умолчанию пространства имен ретранслятора доступны через Интернет, если запрос сопровождается действительной проверкой подлинности и авторизацией. С помощью IP-брандмауэра вы можете ограничить его более ограниченным набором адресов IPv4 или диапазонами IPv4-адресов в нотации [CIDR (без класса для маршрутизации между доменами)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Эта функция полезна в сценариях, в которых Azure Relay должны быть доступны только из определенных хорошо известных сайтов. Правила брандмауэра позволяют настроить правила приема трафика, исходящего с конкретных IPv4-адресов. Например, если вы используете ретранслятор с [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), можно создать **правило брандмауэра** , разрешающее трафик только с локальных IP-адресов инфраструктуры. 


> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. 


## <a name="enable-ip-firewall-rules"></a>Включить правила IP-брандмауэра
Правила брандмауэра IP-адресов применяются на уровне пространства имен. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам. Любая попытка подключения с IP-адреса, который не соответствует разрешенному правилу IP-адресов в пространстве имен, отклоняется как несанкционированный. В ответе клиенту правило фильтрации IP-адресов не упоминается. Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

### <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как использовать портал Azure для создания правил брандмауэра IP-адресов для пространства имен. 

1. Перейдите к **пространству имен ретранслятора** в [портал Azure](https://portal.azure.com).
2. В меню слева выберите пункт Параметры **сети** . Если в разделе **Разрешить доступ из** выбран параметр **все сети** , пространство имен ретранслятора принимает подключения с любого IP-адреса. Этот параметр эквивалентен правилу, принимающему диапазон IP-адресов 0.0.0.0/0. 

    ![Брандмауэр — выбран параметр "все сети"](./media/ip-firewall/all-networks-selected.png)
1. Чтобы ограничить доступ к определенным сетям и IP-адресам, выберите вариант **Выбранные сети** . В разделе **брандмауэр** выполните следующие действия.
    1. Установите флажок **Добавить IP-адрес клиента** , чтобы предоставить текущему клиенту IP доступ к пространству имен. 
    2. В качестве **диапазона адресов**введите конкретный IPv4-адрес или диапазон IPv4-адресов в нотации CIDR. 
    3. Укажите, следует ли **разрешить доверенным службам Майкрософт обходить этот брандмауэр**. 

        ![Брандмауэр — выбран параметр "все сети"](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Нажмите кнопку **сохранить** на панели инструментов, чтобы сохранить параметры. Подождите несколько минут, пока не отобразится подтверждение уведомления на портале.


### <a name="use-resource-manager-template"></a>Использование шаблона Resource Manager
Следующий шаблон диспетчер ресурсов позволяет добавить правило IP-фильтра к существующему пространству имен ретранслятора.

Шаблон принимает один параметр: **ипмаск**, который является одним IPv4-адресом, или блоком адресов в нотации CIDR. Например, значение 70.37.104.0/24 в нотации CIDR представляет 256 IPv4-адресов в диапазоне от 70.37.104.0 до 70.37.104.255. Число 24 обозначает количество значимых битов префикса для адресов этого диапазона.

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
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
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
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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

Инструкции по развертыванию шаблона см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).



## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о других функциях, связанных с безопасностью сети, см. в разделе [Сетевая безопасность](network-security.md).


<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[LNK-Deploy]:: 
