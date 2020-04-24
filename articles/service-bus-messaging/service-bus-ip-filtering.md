---
title: Настройка правил брандмауэра IP-адресов для служебной шины Azure
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
ms.openlocfilehash: 9601689bbce9566b52664058911e9c45647152d6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116824"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Настройка правил брандмауэра IP-адресов для служебной шины Azure
По умолчанию пространства имен служебной шины доступны через Интернет при условии, что запрос поступает с действительной проверкой подлинности и авторизацией. С помощью IP-брандмауэра вы можете ограничить его более ограниченным набором адресов IPv4 или диапазонами IPv4-адресов в нотации [CIDR (без класса для маршрутизации между доменами)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Эта функция полезна в сценариях, в которых служебная шина Azure должна быть доступна только с определенных хорошо известных сайтов. Правила брандмауэра позволяют настроить правила приема трафика, исходящего с конкретных IPv4-адресов. Например, если вы используете служебную шину с [Azure Express Route][express-route], вы можете создать **правило брандмауэра** , разрешающее трафик только от локальных IP-адресов инфраструктуры или адресов корпоративного шлюза NAT. 

> [!IMPORTANT]
> Брандмауэры и виртуальные сети поддерживаются только на уровне **Premium** служебной шины. Если обновление до уровня **Premier** не является возможностью, рекомендуется защитить маркер подписанного URL-адрес (SAS) и предоставить к нему доступ только полномочным пользователям. Сведения о проверке подлинности SAS см. в разделе [Проверка подлинности и авторизация](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Правила брандмауэра для IP-адресов
Правила брандмауэра IP-адресов применяются на уровне пространства имен служебной шины. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам. Любые попытки подключения с IP-адресов, которые не соответствуют правилу разрешенных IP-адресов для пространства имен Служебной шины, отклоняются. В ответе клиенту правило фильтрации IP-адресов не упоминается. Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

>[!WARNING]
> Реализация правил брандмауэра может предотвратить взаимодействие других служб Azure со Служебной шиной.
>
> Доверенные службы Майкрософт не поддерживаются, если реализована фильтрация IP (правила брандмауэра). Их поддержка будет доступна в ближайшее время.
>
> Распространенные сценарии Azure, которые не работают с фильтрацией IP (обратите внимание, что список **НЕ** является исчерпывающим):
> - Интеграция со службой "Сетка событий Azure".
> - Маршруты Центра Интернета вещей Azure.
> - Device Explorer Интернета вещей Azure.
>
> Следующие службы Майкрософт должны находиться в виртуальной сети
> - Служба приложений Azure
> - Функции Azure

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как использовать портал Azure для создания правил брандмауэра IP-адресов для пространства имен служебной шины. 

1. Перейдите к **пространству имен служебной шины** в [портал Azure](https://portal.azure.com).
2. В меню слева выберите пункт Параметры **сети** . По умолчанию выбран параметр **все сети** . Пространство имен служебной шины принимает подключения с любого IP-адреса. То есть, такое значение параметра по умолчанию равноценно правилу, которое принимает диапазон IP-адресов 0.0.0.0/0. 

    ![Брандмауэр — выбран параметр "все сети"](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Выберите параметр **Выбранные сети** в верхней части страницы. В разделе **брандмауэр** выполните следующие действия.
    1. Установите флажок **Добавить IP-адрес клиента** , чтобы предоставить текущему клиенту IP доступ к пространству имен. 
    2. В качестве **диапазона адресов**введите конкретный IPv4-адрес или диапазон IPv4-адресов в нотации CIDR. 
    3. Укажите, следует ли **разрешить доверенным службам Майкрософт обходить этот брандмауэр**. 

        ![Брандмауэр — выбран параметр "все сети"](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Нажмите кнопку **сохранить** на панели инструментов, чтобы сохранить параметры. Подождите несколько минут, пока не отобразится подтверждение уведомления на портале.

## <a name="use-resource-manager-template"></a>Использование шаблона Resource Manager
В этом разделе приведен пример шаблона Azure Resource Manager, который создает виртуальную сеть и правило брандмауэра.


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

## <a name="next-steps"></a>Дальнейшие шаги

Инструкции по ограничению доступа к Служебной шине из виртуальных сетей Azure, см. по следующей ссылке:

- [Use Virtual Network service endpoints with Azure Service Bus][lnk-vnet] (Использование конечных точек служб для виртуальной сети для Служебной шины Azure)

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
