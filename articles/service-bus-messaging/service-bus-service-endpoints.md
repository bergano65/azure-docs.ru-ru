---
title: Конечные точки службы виртуальной сети — служебная шина Azure
description: Добавление конечной точки службы Microsoft.ServiceBus в виртуальную сеть.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: aschhab
ms.openlocfilehash: f32a67dc6d3b3f869afaa532403c05b218588552
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786386"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Использование конечных точек служб для виртуальной сети со Служебной шиной Azure

Интеграция служебной шины с [конечными точками службы виртуальной сети][vnet-sep] обеспечивает безопасный доступ к возможностям обмена сообщениями из рабочих нагрузок, таких как виртуальные машины, привязанные к виртуальным сетям, с помощью пути сетевого трафика, защищенного как в начинает.

Если настроена привязка по крайней мере к одной конечной точке службы подсети виртуальной сети, пространство имен соответствующей Служебной шины принимает трафик только из авторизованных виртуальных сетей. С точки зрения виртуальной сети привязка пространства имен Служебной шины к конечной точке службы настраивает изолированный сетевой туннель от подсети виртуальной сети к службе обмена сообщениями.

Результатом является частная и изолированная взаимосвязь между рабочими нагрузками, связанными с подсетью, и соответствующим пространством имен Служебной шины, несмотря на то что наблюдаемый сетевой адрес конечной точки службы обмена сообщениями находится в общедоступном диапазоне IP-адресов.

>[!WARNING]
> Реализация интеграции виртуальных сетей может препятствовать взаимодействию других служб Azure со служебной шиной.
>
> Доверенные службы Майкрософт не поддерживаются, если реализованы виртуальные сети.
>
> Распространенные сценарии Azure, которые не работают с виртуальными сетями (обратите внимание, что список **НЕ** является исчерпывающим):
> - Azure Monitor
> - Azure Stream Analytics
> - Интеграция со службой "Сетка событий Azure".
> - Маршруты Центра Интернета вещей Azure.
> - Device Explorer Интернета вещей Azure.
> - Azure Data Explorer
>
> В виртуальной сети должны присутствовать следующие службы Майкрософт:
> - Служба приложений Azure
> - Функции Azure

> [!IMPORTANT]
> Виртуальные сети поддерживаются только в пространствах имен службы "Служебная шина" [ценовой категории "Премиум"](service-bus-premium-messaging.md).

## <a name="enable-service-endpoints-with-service-bus"></a>Включение конечных точек служб с использованием службы "Служебная шина"

Если вы используете конечные точки служб виртуальной сети со службой "Служебная шина", важно учесть, что не следует включать эти конечные точки в приложениях, где одновременно применяются пространства имен службы "Служебная шина" ценовых категорий "Стандартный" и "Премиум". В ценовой категории "Стандартный" виртуальные сети не поддерживаются, поэтому конечная точка ограничивается только пространствами имен ценовой категории "Премиум".

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Расширенные сценарии обеспечения безопасности, доступные при интеграции с виртуальной сетью 

Решения, требующие жесткой и раздельной защиты, где подсети виртуальной сети обеспечивают сегментирование между разделенными службами, обычно все же нуждаются в путях взаимодействия между службами, находящимися в этих секциях.

Любой прямой IP-маршрут между секциями, включая передачу трафика HTTPS через TCP/IP, несет риск использования уязвимостей на сетевом уровне. Службы обмена сообщениями обеспечивают полностью изолированные пути взаимодействия, где сообщения даже записываются на диск при передаче между сторонами. Рабочие нагрузки в двух разных виртуальных сетях, которые связаны с одним и тем же экземпляром Служебной шины, могут эффективно и надежно связываться с использованием сообщений, в то время как целостность границ изолированной сети сохраняется.
 
Это означает, что ваши конфиденциальные облачные решения, влияющие на безопасность, не только получают доступ к ведущим в отрасли надежным, масштабируемым и асинхронным службам обмена сообщениями Azure, но теперь могут использовать обмен сообщениями, чтобы создавать пути передачи данных между защищенными секциями решений. По своей сути такие пути обеспечивают больше безопасности, чем при любом режиме одноранговой связи, включая HTTPS и другие протоколы сокетов TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Привязка Служебной шины к виртуальным сетям

*Правила виртуальной сети* — это функция безопасности брандмауэра, которая позволяет контролировать, принимает ли сервер Служебной шины Azure соединения из определенной подсети виртуальной сети.

Привязка пространства имен Служебной шины к виртуальной сети состоит из двух этапов. Сначала необходимо создать **конечную точку службы виртуальной** сети в подсети виртуальной сети и включить ее для Microsoft. servicebus, как описано в [обзоре конечной точки службы][vnet-sep]. Добавив конечную точку службы, вы привязываете к ней пространство имен Служебной шины с помощью *правила виртуальной сети*.

Правило виртуальной сети — это связь пространства имен Служебной шины с подсетью виртуальной сети. Пока правило существует, всем рабочим нагрузкам, привязанным к подсети, предоставляется доступ к пространству имен Служебной шины. Служебная шина никогда не устанавливает исходящие подключения и не нуждается в доступе, и поэтому это правило не предоставляет ей доступ к вашей подсети.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Создание правила виртуальной сети с использованием шаблонов Azure Resource Manager

Следующий шаблон Resource Manager позволяет добавить правило виртуальной сети в пространство имен существующей Служебной шины.

Параметры шаблона.

* **namespaceName**: пространство имен служебной шины;
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

Шаблон:

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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о виртуальных сетях см. по следующим ссылкам:

- [Конечные точки службы виртуальной сети Azure][vnet-sep]
- [IP-фильтрация в служебной шине Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
