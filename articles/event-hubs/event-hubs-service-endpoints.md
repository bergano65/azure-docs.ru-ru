---
title: Конечные точки служб для виртуальной сети для Центров событий Azure | Документация Майкрософт
description: В этой статье содержатся сведения о том, как добавить конечную точку службы Microsoft. EventHub в виртуальную сеть.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 5b02b79980ebe5ea91a1cf16d3ea453ebef3bf08
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279785"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Использование конечных точек служб для виртуальной сети с Центрами событий Azure

Интеграция концентраторов событий с [конечными точками службы виртуальной сети][vnet-sep] обеспечивает безопасный доступ к возможностям обмена сообщениями из рабочих нагрузок, таких как виртуальные машины, привязанные к виртуальным сетям, с помощью пути сетевого трафика, защищенного как в начинает.

После настройки привязки хотя бы к одной конечной точке службы подсети виртуальной сети соответствующее пространство имен концентраторов событий больше не будет принимать трафик из любого места, но с полномочными подсетями в виртуальных сетях. С точки зрения виртуальной сети привязка пространства имен Центров событий к конечной точке службы настраивает изолированный сетевой туннель от подсети виртуальной сети к службе обмена сообщениями. 

Результатом является частная и изолированная взаимосвязь между рабочими нагрузками, привязанными к подсети, и соответствующим пространством имен Центров событий, несмотря на то что наблюдаемый сетевой адрес конечной точки службы обмена сообщениями находится в общедоступном диапазоне IP-адресов. Исключение этого поведения. Включение конечной точки службы по умолчанию включает правило денялл в IP-брандмауэре, связанном с виртуальной сетью. Вы можете добавить определенные IP-адреса в брандмауэре IP-адресов, чтобы разрешить доступ к общедоступной конечной точке концентратора событий. 


>[!WARNING]
> Реализация интеграции виртуальных сетей может препятствовать взаимодействию других служб Azure с Центрами событий.
>
> Доверенные службы Майкрософт не поддерживаются, если реализованы виртуальные сети.
>
> Распространенные сценарии Azure, которые не работают с виртуальными сетями (обратите внимание, что список **НЕ** является исчерпывающим):
> - Azure Stream Analytics
> - Интеграция со службой "Сетка событий Azure".
> - Маршруты Центра Интернета вещей Azure.
> - Device Explorer Интернета вещей Azure.
>
> В виртуальной сети должны присутствовать следующие службы Майкрософт:
> - Веб-приложения Azure.
> - Функции Azure

> [!IMPORTANT]
> Виртуальные сети поддерживаются в **стандартном** и **выделенном** уровнях Центра событий. В "базовом" уровне не поддерживается.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Расширенные сценарии обеспечения безопасности, доступные при интеграции с виртуальной сетью 

Решения, требующие тесной и подразделяетсяй безопасности, а также, где подсети виртуальной сети обеспечивают сегментацию между службами подразделяется, по-прежнему нуждаются в путях обмена данными между службами, находящимися в этих секциях.

Любой прямой IP-маршрут между секциями, включая передачу трафика HTTPS через TCP/IP, несет риск использования уязвимостей на сетевом уровне. Службы обмена сообщениями обеспечивают полностью изолированные пути взаимодействия, где сообщения даже записываются на диск при переходе между сторонами. Рабочие нагрузки в двух разных виртуальных сетях, которые связаны с одним и тем же экземпляром Центров событий, могут эффективно и надежно связываться через сообщения, в то время как целостность границ изолированной сети сохраняется.
 
Это означает, что ваши конфиденциальные облачные решения, влияющие на безопасность, не только получают доступ к ведущим в отрасли надежным и масштабируемым асинхронным службам обмена сообщениями Azure, но теперь они могут использовать обмен сообщениями для создания путей передачи данных между защищенными секциями решений, которые по своей сути более безопасны, чем степень безопасности в любом режиме одноранговой связи, включая HTTPS и другие протоколы сокетов TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Привязка Центров событий к виртуальным сетям

*Правила виртуальной сети* — это функция безопасности брандмауэра, которая контролирует, принимает ли пространство имен Центров событий Azure соединения из определенной подсети виртуальной сети.

Привязка пространства имен Центров событий к виртуальной сети состоит из двух этапов. Сначала необходимо создать **конечную точку службы виртуальной** сети в подсети виртуальной сети и включить ее для Microsoft. EventHub, как описано в [обзоре конечной точки службы][vnet-sep]. После добавления конечной точки службы вы привязываете пространство имен Центров событий к ней с помощью *правила виртуальной сети*.

Правило виртуальной сети — это связь пространства имен Центров событий с подсетью виртуальной сети. Пока правило существует, всем рабочим нагрузкам, привязанным к подсети, предоставляется доступ к пространству имен Центров событий. Центры событий никогда не устанавливают исходящие подключения, не нуждаются в доступе и поэтому не получают доступ к вашей подсети, если это правило включено.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Создание правила виртуальной сети с использованием шаблонов Azure Resource Manager

Следующий шаблон Resource Manager позволяет добавить правило виртуальной сети в существующее пространство имен Центров событий.

Параметры шаблона:

* **namespaceName**. Пространство имен Центров событий.
* **vnetRuleName**. Имя создаваемого правила виртуальной сети.
* **virtualNetworkingSubnetId**: полный путь Resource Manager для подсети виртуальной сети, например `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` для подсети виртуальной сети по умолчанию.

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Чтобы развернуть шаблон, следуйте инструкциям по [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о виртуальных сетях см. по следующим ссылкам:

- [Конечные точки службы виртуальной сети Azure][vnet-sep]
- [IP-фильтрация концентраторов событий Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
