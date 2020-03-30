---
title: Фильтры IP-соединения Azure IoT DPS Документы Майкрософт
description: Как использовать фильтрацию IP для блокирования подключений с определенных IP-адресов в экземплярaz IoT DPS. Вы можете блокировать подключения с отдельных IP-адресов или их диапазонов.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284919"
---
# <a name="use-ip-filters"></a>Использование фильтрации IP-адресов

Безопасность является важным аспектом любого решения IoT. Иногда при настройке системы безопасности необходимо явно указать IP-адреса, с которых могут подключаться устройства. Функция *IP-фильтра* для службы обеспечения устройств Ip-концентратора Azure IoT (DPS) позволяет настроить правила для отклонения или приема трафика с определенных адресов IPv4.

## <a name="when-to-use"></a>Назначение

Есть два конкретных случая использования, где полезно блокировать соединения с конечными точками DPS с определенных IP-адресов:

* Ваш DPS должен получать трафик только с определенного диапазона IP-адресов и отвергать все остальное. Например, вы используете DPS с [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) для создания частных соединений между DPS и устройствами.

* Необходимо отклонить трафик с IP-адресов, которые были идентифицированы администратором DPS как подозрительные.

## <a name="how-filter-rules-are-applied"></a>Применение правил фильтрации

Правила IP-фильтра применяются на уровне dPS instance. Поэтому они действуют для всех подключений с устройств и внутренних приложений, использующих любые поддерживаемые протоколы.

Любая попытка подключения с IP-адреса, который соответствует отрицая правило IP в экземпляре DPS, получает несанкционированный код статуса 401. В ответном сообщении не указывается правило фильтрации IP-адресов.

## <a name="default-setting"></a>Параметр по умолчанию

По умолчанию сетка **IP-фильтра** на портале для DPS пуста. Эта настройка по умолчанию означает, что dPS принимает соединения с любого IP-адреса. То есть, такое значение параметра по умолчанию равноценно правилу, которое принимает диапазон IP-адресов 0.0.0.0/0.

![Настройки IP-фильтра IoT DPS по умолчанию](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Добавление или изменение правила фильтрации IP-адресов

Чтобы добавить правило IP-фильтра, выберите **правило IP-фильтра.**

![Добавьте правило IP-фильтра в IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

После выбора **Добавить IP фильтр правило,** заполнить поля.

![После выбора Правила IP-фильтра](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Укажите **название** для правила IP Filter. Это должна быть уникальная, нечувствительная, алфавитная строка длиной до 128 символов. Допускаются только 7-разрядные буквы и цифры ASCII, а также `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Укажите один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, в нотации CIDR 192.168.100.0/22 обозначает диапазон из 1024 IPv4-адресов от 192.168.100.0 до 192.168.103.255.

* Выберите **«Разрешить** или **блокировать»** в качестве **действия** для правила IP-фильтра.

После заполнения полей выберите **Сохранить,** чтобы сохранить правило. Вы видите оповещение, уведомляющее вас о том, что обновление продолжается.

![Уведомление о сохранении правила фильтрации IP-адресов](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Параметр **Добавить** становится неактивным, когда достигается максимальное число правил фильтрации IP-адресов (10).

Чтобы отсечь существующее правило, выберите данные, которые вы хотите изменить, внести изменения, а затем выберите **Сохранить,** чтобы сохранить свое изменение.

> [!NOTE]
> Отказ от IP-адресов может помешать другим службам Azure взаимодействовать с экземпляром DPS.

## <a name="delete-an-ip-filter-rule"></a>Удаление правила фильтрации IP-адресов

Чтобы удалить правило IP-фильтра, выберите значок мусорного бака на этой строке, а затем выберите **Сохранить.** Правило удаляется и изменение сохраняется.

![Удалить правило IP-фильтра IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Обновление правил IP-фильтра в коде

Вы можете получить и изменить свой IP-фильтр DPS с помощью endpoint REST поставщика ресурсов Azure. Подробные сведения о `properties.ipFilterRules` см. в статье [Iot Hub Resource — Create Or Update](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate) (Ресурс Центра Интернета вещей. Создание или обновление).

Обновление правил IP-фильтра DPS в настоящее время не поддерживается с помощью Azure CLI или Azure PowerShell, но может быть выполнено с помощью шаблонов Azure Resource Manager. См., [шаблоны менеджера ресурсов Azure](../azure-resource-manager/templates/overview.md) для руководства по использованию шаблонов управления ресурсами. Примеры шаблонов, которые следуют, показывают, как создавать, отсваивать и удалять правила IP-фильтра DPS.

### <a name="add-an-ip-filter-rule"></a>Добавить правило IP-фильтра

Следующий пример шаблона создает новое правило IP-фильтра под названием "AllowAll", которое принимает весь трафик.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Обновление атрибутов правила IP-фильтра шаблона на основе ваших требований.

| Атрибут                | Описание |
| ------------------------ | ----------- |
| **ФильтрИмя**           | Укажите название для правила IP Filter. Это должна быть уникальная, нечувствительная, алфавитная строка длиной до 128 символов. Только 7-битные алфавитные символы ASCII плюс :',',',',,',\'',', ',', ',', ', ', ', ',', ',', ', '' принимаются. |
| **Действие**               | Принятые значения **принимаются** или **отклоняются** в качестве действия для правила IP-фильтра. |
| **ipМаск**               | Укажите один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, в нотации CIDR 192.168.100.0/22 обозначает диапазон из 1024 IPv4-адресов от 192.168.100.0 до 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Обновление правила IP-фильтра

Следующий пример шаблона обновляет правило IP-фильтра под названием "AllowAll", показанное ранее, чтобы отклонить весь трафик.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Удаление правила фильтрации IP-адресов

Следующий пример шаблона удаляет все правила IP-фильтра для экземпляра DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Оценка правила фильтрации IP-адресов

Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

Например, если вы хотите, чтобы адреса в диапазоне 192.168.100.0/22 принимались, а все остальные отклонялись, то первым правилом в списке должно быть именно правило о приеме трафика из диапазона адресов 192.168.100.0/22. Следующим должно быть правило об отклонении всех адресов; для этого используется диапазон 0.0.0.0/0.

Последовательность правил фильтрации IP-адресов в списке можно изменить. Для этого щелкните три вертикальные точки в начале строки и воспользуйтесь функцией перетаскивания.

Чтобы сохранить новую последовательность, нажмите кнопку **Сохранить**.

![Измените порядок правил IP-фильтра DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Дальнейшие действия

Для дальнейшего изучения управления DPS, см.:

* [Понимание IP-адресов IoT DPS](iot-dps-understand-ip-address.md)
* [Настройка DPS с помощью Azure CLI](how-to-manage-dps-with-cli.md)
* [Контроль доступа к DPS](how-to-control-access.md)
