---
title: Переместите виртуальную сеть Azure в другой регион Azure с помощью портала Azure.
description: Переместите виртуальную сеть Azure из одного региона Azure в другой с помощью шаблона «Менеджер ресурсов» и портала Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640794"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Переместите виртуальную сеть Azure в другой регион с помощью портала Azure

Существуют различные сценарии перемещения существующей виртуальной сети Azure из одного региона в другой. Например, можно создать виртуальную сеть с той же конфигурацией для тестирования и доступности, что и существующая виртуальная сеть. Или вы можете переместить производственную виртуальную сеть в другой регион в рамках планирования аварийного восстановления.

Для завершения перемещения виртуальной сети в другой регион можно использовать шаблон менеджера ресурсов Azure. Вы делаете это, экспортируя виртуальную сеть в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблон в новом регионе. Для получения дополнительной информации о [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)шаблонах управления ресурсами см.


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что ваша виртуальная сеть находится в регионе Azure, из которого вы хотите перейти.

- Для экспорта виртуальной сети и развертывания шаблона для создания виртуальной сети в другом регионе необходимо иметь роль вкладчика сети или выше.

- Виртуальные врашители сети не будут воссозданы, и они потерпят неудачу, если они все еще присутствуют в шаблоне. Перед экспортом шаблона необходимо удалить любые виртуальные сетевые узлы. Затем их можно восстановить после перемещения виртуальной сети.

- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается баланселизаторами нагрузки, группами сетевой безопасности (NSGs) и публичными ИП.

- Убедитесь, что подписка Azure позволяет создавать виртуальные сети в целевом регионе. Для обеспечения необходимой квоты обратитесь в службу поддержки.

- Убедитесь, что ваша подписка имеет достаточно ресурсов для поддержки добавления виртуальных сетей для этого процесса. Дополнительные сведения см. в статье [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Подготовка к переезду
В этом разделе вы готовите виртуальную сеть к перемещению с помощью шаблона Resource Manager. Затем виртуальная сеть перемещается в целевой регион с помощью портала Azure.

Для экспорта виртуальной сети и развертывания целевой виртуальной сети с помощью портала Azure сделайте следующее:

1. Вопийте на [портале Azure,](https://portal.azure.com)а затем выберите **группы ресурсов.**
1. Найдите группу ресурсов, содержащую виртуальную сеть исходного кода, а затем выберите ее.
1. Выберите**шаблон «Экспорт** **настроек».** > 
1. В панели шаблона Экспорт **ам-конторы**выберите Deploy . **Export template**
1. Чтобы открыть файл *parameters.json* в онлайн-редакторе, выберите параметры **шаблона** > **edit.**
1. Для отсечения параметра названия виртуальной сети измените свойство **значения** по **параметрам:**

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```

1. В редакторе измените значение имени виртуальной сети в редакторе на имя, которое требуется для целевой виртуальной сети. Не забудьте приложить имя в кавычки.

1. Выберите **Сохранить** в редакторе.

1. Чтобы открыть файл *template.json* в онлайн-редакторе, выберите шаблон **Template** > **Edit.**

1. В онлайн-редакторе, чтобы отредовать целевой регион, где виртуальная сеть будет перемещена, измените свойство **местоположения** под **ресурсами:**

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

1. Для получения кодов местоположений регионов [см.](https://azure.microsoft.com/global-infrastructure/locations/) Код для региона является названием региона, без пробелов (например, **Центральный** = **центральный США**).

1. (Необязательно) Вы также можете изменить другие параметры в шаблоне, в зависимости от ваших требований:

    * **Адрес пространства**: Прежде чем сохранить файл, вы можете изменить адресное пространство виртуальной сети, изменив раздел **адрес ресурсовSpace** > **addressSpace** и изменение **адресаPrefixes** свойства:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Подсеть**: Вы можете изменить или добавить к имени подсети и расположению адреса подсети, изменив раздел **подсетей** шаблона. Вы можете изменить название подсети, изменив свойство **имени.** И вы можете изменить пространство адреса подсети, изменив свойство **адресPrefix:**

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Чтобы изменить префикс адреса в файле *template.json,* отобрадайте ее в двух местах: в коде в предыдущем разделе и в разделе **типа** следующего кода. Измените свойство **адресаPrefix** в следующем коде, чтобы соответствовать свойству **адресаPrefix** в коде в предыдущем разделе.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. В онлайн-редакторе выберите **Сохранить**.

1. Чтобы выбрать подписку, в которой будет развернута целевая виртуальная сеть, выберите**подписку** **Basics.** > 

1. Чтобы выбрать группу ресурсов, в которой будет развернута целевая виртуальная сеть, выберите группу **Basics** > **Resource.** 

    Если вам нужно создать новую группу ресурсов для целевой виртуальной сети, выберите **Создать новый**. Убедитесь, что имя не совпадает с именем группы ресурсов исходного ресурса в существующей виртуальной сети.

1. Проверить, что **Basics** > **Местоположение** настроено в целевом месте, где вы хотите, чтобы виртуальная сеть была развернута.

1. В **настройках**убедитесь, что имя совпадает с именем, которое вы ввели ранее в редакторе параметров.

1. Выберите флажок **«Условия и условия».**

1. Чтобы развернуть целевую виртуальную сеть, выберите **Покупка.**

## <a name="delete-the-target-virtual-network"></a>Удалить целевую виртуальную сеть

Чтобы отказаться от целевой виртуальной сети, вы удаляете группу ресурсов, содержащую целевую виртуальную сеть. Для этого:
1. На панели мониторинга портала Azure выберите группу ресурсов.
1. В верхней части панели **Обзора** выберите **Удалить**.

## <a name="clean-up"></a>Очистка

Чтобы совершить изменения и завершить перемещение виртуальной сети, вы удалите исходную виртуальную сеть или группу ресурсов. Для этого:
1. На панели мониторинга портала Azure выберите виртуальную группу или группу ресурсов.
1. В верхней части каждой панели выберите **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы переместили виртуальную сеть Azure из одного региона в другой с помощью портала Azure, а затем очистили ненужные исходные ресурсы. Подробнее о перемещении ресурсов между регионами и аварийном восстановлении в Azure можно узнать:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Переместите виртуальные машины Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
