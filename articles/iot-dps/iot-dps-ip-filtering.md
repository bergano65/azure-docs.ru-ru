---
title: Фильтры IP-подключений Azure IoT DP | Документация Майкрософт
description: Использование фильтрации IP-адресов для блокировки подключений с определенных IP-адресов к экземпляру службы Azure IoT DP. Вы можете блокировать подключения с отдельных IP-адресов или их диапазонов.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79284919"
---
# <a name="use-ip-filters"></a>Использование фильтрации IP-адресов

Безопасность является важным аспектом любого решения Интернета вещей. Иногда при настройке системы безопасности необходимо явно указать IP-адреса, с которых могут подключаться устройства. Функция *IP-фильтра* для службы подготовки устройств для центра Интернета вещей Azure (DPS) позволяет настраивать правила для отклонения или приема трафика от конкретных IPv4-адресов.

## <a name="when-to-use"></a>Назначение

Существует два особых варианта использования, когда полезно блокировать подключения к конечной точке DPS с определенных IP-адресов:

* Служба DPS должна принимать трафик только из указанного диапазона IP-адресов и отклонять все остальное. Например, вы используете службу DPS с помощью [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) для создания частных подключений между службой DPS и вашими устройствами.

* Необходимо отклонять трафик с IP-адресов, идентифицированных администратором DPS как подозрительные.

## <a name="how-filter-rules-are-applied"></a>Применение правил фильтрации

Правила IP-фильтра применяются на уровне экземпляра DPS. Поэтому они действуют для всех подключений с устройств и внутренних приложений, использующих любые поддерживаемые протоколы.

Любая попытка подключения с IP-адреса, соответствующего правилу отклоненных IP-адресов в экземпляре DPS, получает несанкционированный код состояния 401 и описание. В ответном сообщении не указывается правило фильтрации IP-адресов.

## <a name="default-setting"></a>Параметр по умолчанию

По умолчанию сетка **IP-фильтра** на портале для DPS пуста. Этот параметр по умолчанию означает, что служба DPS принимает подключения с любого IP-адреса. То есть, такое значение параметра по умолчанию равноценно правилу, которое принимает диапазон IP-адресов 0.0.0.0/0.

![Параметры IP-фильтра по умолчанию для IoT DP](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Добавление или изменение правила фильтрации IP-адресов

Чтобы добавить правило фильтрации IP-адресов, выберите **+ Добавить правило IP-фильтра**.

![Добавление правила IP-фильтра к DP центра Интернета вещей](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

После нажатия кнопки **Добавить правило IP-фильтра**заполните поля.

![После выбора параметра Добавить правило IP-фильтра](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Укажите **имя** для правила IP-фильтра. Это должна быть уникальная, без учета регистра, буквенно-цифровая строка длиной до 128 символов. Допускаются только 7-разрядные буквы и цифры ASCII, а также `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Укажите один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, в нотации CIDR 192.168.100.0/22 обозначает диапазон из 1024 IPv4-адресов от 192.168.100.0 до 192.168.103.255.

* Выберите **Разрешить** или **заблокировать** в качестве **действия** для правила IP-фильтра.

После заполнения полей выберите **сохранить** , чтобы сохранить правило. Появится предупреждение о том, что обновление выполняется.

![Уведомление о сохранении правила фильтрации IP-адресов](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Параметр **Добавить** становится неактивным, когда достигается максимальное число правил фильтрации IP-адресов (10).

Чтобы изменить существующее правило, выберите данные, которые необходимо изменить, внесите изменения, а затем нажмите кнопку **сохранить** , чтобы сохранить изменения.

> [!NOTE]
> Отклонение IP-адресов может препятствовать взаимодействию других служб Azure с экземпляром DPS.

## <a name="delete-an-ip-filter-rule"></a>Удаление правила фильтрации IP-адресов

Чтобы удалить правило фильтрации IP-адресов, выберите значок корзины в этой строке и нажмите кнопку **сохранить**. Правило удаляется, а изменение сохраняется.

![Удаление правила IP-фильтра IoT DP](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Обновление правил IP-фильтра в коде

Вы можете получить и изменить фильтр IP-адресов DPS с помощью конечной точки службы "ОСТАВШАЯся сторона поставщика ресурсов Azure". Подробные сведения о `properties.ipFilterRules` см. в статье [Iot Hub Resource — Create Or Update](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate) (Ресурс Центра Интернета вещей. Создание или обновление).

Обновление правил фильтра IP-адресов DPS в настоящее время не поддерживается для Azure CLI или Azure PowerShell но может быть выполнено с помощью шаблонов Azure Resource Manager. Рекомендации по использованию шаблонов диспетчер ресурсов см. в [Azure Resource Manager шаблонах](../azure-resource-manager/templates/overview.md) . В приведенных ниже примерах шаблонов показано, как создавать, изменять и удалять правила IP-фильтра DPS.

### <a name="add-an-ip-filter-rule"></a>Добавление правила IP-фильтра

В следующем примере шаблона создается новое правило IP-фильтра с именем "Алловалл", которое принимает весь трафик.

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

Обновите атрибуты правила фильтрации IP-адресов для шаблона в соответствии с вашими требованиями.

| Атрибут                | Описание |
| ------------------------ | ----------- |
| **FilterName**           | Укажите имя для правила IP-фильтра. Это должна быть уникальная, без учета регистра, буквенно-цифровая строка длиной до 128 символов. Только 7-разрядные буквы и цифры в формате ASCII, плюс {'-', ': ', '\'/', ', '. ', ' + ', '% ', ' _ ', ' # ', ' * ', '? ', "!", "(", ")", ",", "=", "@", ";", "" "} принимаются. |
| **Действие**               | Принятые значения **принимаются** или **отклоняются** в качестве действия для правила IP-фильтра. |
| **ипмаск**               | Укажите один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, в нотации CIDR 192.168.100.0/22 обозначает диапазон из 1024 IPv4-адресов от 192.168.100.0 до 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Обновление правила фильтрации IP-адресов

В следующем примере шаблона обновляется правило IP-фильтра с именем "Алловалл", показанное ранее, чтобы отклонить весь трафик.

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

В следующем примере шаблона удаляются все правила IP-фильтра для экземпляра DP.

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

![Изменение порядка правил фильтрации IP-адресов DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения об управлении DPS см. в следующих статьях:

* [Основные сведения об IP-адресах центра Интернета вещей](iot-dps-understand-ip-address.md)
* [Настройка DPS с помощью Azure CLI](how-to-manage-dps-with-cli.md)
* [Управление доступом к DP](how-to-control-access.md)
