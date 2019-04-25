---
title: Конечные точки службы виртуальной сети для Центров событий Azure | Документация Майкрософт
description: В этой статье сведения о том, как добавить конечную точку службы Microsoft.EventHub к виртуальной сети.
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
ms.openlocfilehash: 7b5a62f81238d1ae2b627c395613066350b36efe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343449"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Использование конечных точек службы виртуальной сети с Центрами событий Azure

Интеграция Центров событий с [конечными точками службы виртуальной сети][vnet-sep] обеспечивает безопасный доступ к возможностям обмена сообщениями из рабочих нагрузок, таких как виртуальные машины, привязанные к виртуальным сетям, где трафик защищен с обеих сторон.

После настройки привязано к конечной точке службы по крайней мере одну подсеть виртуальной сети, соответствующего пространства имен концентраторов событий больше не принимает трафик из любого места, но авторизованные подсетей в виртуальных сетях. С точки зрения виртуальной сети привязка пространства имен Центров событий к конечной точке службы настраивает изолированный сетевой туннель от подсети виртуальной сети к службе обмена сообщениями. 

Результатом является частная и изолированная взаимосвязь между рабочими нагрузками, привязанными к подсети, и соответствующим пространством имен Центров событий, несмотря на то что наблюдаемый сетевой адрес конечной точки службы обмена сообщениями находится в общедоступном диапазоне IP-адресов. Существует исключение в такой ситуации. Включение конечной точки службы по умолчанию включает правило "denyall" в брандмауэре IP-адрес, связанный с виртуальной сетью. Можно добавить определенные IP-адреса в брандмауэре IP-адрес, чтобы разрешить доступ к общедоступной конечной точки концентратора событий. 


>[!WARNING]
> Реализация интеграции виртуальных сетей может препятствовать взаимодействию других служб Azure с Центрами событий.
>
> Доверенные службы Майкрософт не поддерживаются, если реализованы виртуальные сети.
>
> Распространенные сценарии Azure, которые не работают с виртуальными сетями (обратите внимание, что список **НЕ** является исчерпывающим):
> - Azure Monitor
> - Azure Stream Analytics
> - Интеграция со службой "Сетка событий Azure".
> - Маршруты Центра Интернета вещей Azure.
> - Device Explorer Интернета вещей Azure.
> - Обозреватель данных Azure
>
> В виртуальной сети должны присутствовать следующие службы Майкрософт:
> - Веб-приложения Azure.
> - Функции Azure

> [!IMPORTANT]
> Виртуальные сети поддерживаются в **стандартном** и **выделенном** уровнях Центра событий. В "базовом" уровне не поддерживается.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Расширенные сценарии обеспечения безопасности, доступные при интеграции с виртуальной сетью 

Решений, требующих безопасности тесной и подразделяется и сегментации между compartmentalized службами, и если обеспечивают подсети виртуальной сети по-прежнему требуется путей передачи данных между сервисами, размещенными в этих секциях.

Любой прямой IP-маршрут между секциями, включая передачу трафика HTTPS через TCP/IP, несет риск использования уязвимостей на сетевом уровне. Службы обмена сообщениями обеспечивают полностью изолированные пути взаимодействия, где сообщения даже записываются на диск при переходе между сторонами. Рабочие нагрузки в двух разных виртуальных сетях, которые связаны с одним и тем же экземпляром Центров событий, могут эффективно и надежно связываться через сообщения, в то время как целостность границ изолированной сети сохраняется.
 
Это означает, что ваши конфиденциальные облачные решения, влияющие на безопасность, не только получают доступ к ведущим в отрасли надежным и масштабируемым асинхронным службам обмена сообщениями Azure, но теперь они могут использовать обмен сообщениями для создания путей передачи данных между защищенными секциями решений, которые по своей сути более безопасны, чем степень безопасности в любом режиме одноранговой связи, включая HTTPS и другие протоколы сокетов TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Привязка Центров событий к виртуальным сетям

*Правила виртуальной сети* — это функция безопасности брандмауэра, которая контролирует, принимает ли пространство имен Центров событий Azure соединения из определенной подсети виртуальной сети.

Привязка пространства имен Центров событий к виртуальной сети состоит из двух этапов. Сначала необходимо создать **конечную точку службы виртуальной сети** в подсети виртуальной сети и подключить ее к Microsoft.EventHub, как описано в статье [Конечные точки службы виртуальной сети][vnet-sep]. После добавления конечной точки службы вы привязываете пространство имен Центров событий к ней с помощью *правила виртуальной сети*.

Правило виртуальной сети — это связь пространства имен Центров событий с подсетью виртуальной сети. Пока правило существует, всем рабочим нагрузкам, привязанным к подсети, предоставляется доступ к пространству имен Центров событий. Центры событий никогда не устанавливают исходящие подключения, не нуждаются в доступе и поэтому не получают доступ к вашей подсети, если это правило включено.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Создание правила виртуальной сети с использованием шаблонов Azure Resource Manager

Следующий шаблон Resource Manager позволяет добавить правило виртуальной сети в существующее пространство имен Центров событий.

Параметры шаблона:

* **namespaceName.** Пространство имен Центров событий.
* **vnetRuleName.** Имя создаваемого правила виртуальной сети.
* **virtualNetworkingSubnetId.** Полный путь Resource Manager для подсети виртуальной сети (например, `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` для подсети виртуальной сети по умолчанию).

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

Инструкции по развертыванию шаблона см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell][lnk-deploy].

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о виртуальных сетях см. по следующим ссылкам:

- [Конечные точки служб виртуальной сети][vnet-sep]
- [IP-фильтры Центров событий Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
