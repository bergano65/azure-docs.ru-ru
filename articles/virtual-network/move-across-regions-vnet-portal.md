---
title: Переместите виртуальную сеть Azure в другой регион Azure, используя портал Azure.
description: Используйте шаблон Azure Resource Manager, чтобы переместить виртуальную сеть Azure из одного региона Azure в другой с помощью портал Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 2a1ee358a6c97b721ec6f0da3eb70269239b0737
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077676"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Перемещение виртуальной сети Azure в другой регион с помощью портал Azure

Существуют различные сценарии, в которых необходимо переместить существующие виртуальные сети Azure (виртуальных сетей) из одного региона в другой. Например, может потребоваться создать виртуальную сеть с такой же конфигурацией для тестирования и доступности существующей виртуальной сети. При планировании аварийного восстановления может также потребоваться переместить производственную виртуальную сеть в другой регион.

Вы можете использовать шаблон Azure Resource Manager для завершения перемещения виртуальной сети в другой регион. Для этого необходимо экспортировать виртуальную сеть в шаблон, изменить параметры в соответствии с регионом назначения, а затем развернуть шаблон в новом регионе.  Дополнительные сведения о Диспетчер ресурсов и шаблонах см. в [разделе Краткое руководство. Создание и развертывание шаблонов Azure Resource Manager с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что виртуальная сеть Azure находится в регионе Azure, из которого вы хотите переместиться.

- Чтобы экспортировать виртуальную сеть и развернуть шаблон для создания виртуальной сети в другом регионе, вам потребуется роль "участник сети" или выше.

- Пиринг виртуальных сетей не будет создан повторно и завершится ошибкой, если они все еще находятся в шаблоне.  Перед экспортом шаблона необходимо удалить все узлы виртуальных сетей, а затем повторно установить узлы после перемещения виртуальной сети.
    
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает, но не ограничен подсистемами балансировки нагрузки, группами безопасности сети (группы безопасности сети) и общедоступными IP-адресами.

- Убедитесь, что подписка Azure позволяет создавать виртуальные сети в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что у вашей подписки достаточно ресурсов для поддержки добавления виртуальных сетей для этого процесса.  См. статью [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Ниже показано, как подготовить виртуальную сеть для перемещения с помощью шаблона диспетчер ресурсов и переместить виртуальную сеть в целевой регион с помощью портала.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Экспорт шаблона и его развертывание на портале

1. Войдите в [портал Azure](http://portal.azure.com) > **группы ресурсов**.
2. Выберите группу ресурсов, содержащую исходную виртуальную сеть, и щелкните ее.
3. Выберите **Параметры** > >**Экспорт шаблона**.
4. Выберите **развернуть** в колонке **Экспорт шаблона** .
5. Щелкните **шаблон** > **изменить параметры** , чтобы открыть файл **Parameters. JSON** в интерактивном редакторе.
6. Чтобы изменить параметр имени виртуальной сети, измените свойство **value** в разделе **Параметры**.
    
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
7. Измените значение параметра имя исходной виртуальной сети в редакторе на выбранное имя для целевой сети. Обязательно заключите имя в кавычки.

8.  Нажмите кнопку **сохранить** в редакторе.

9.  Щелкните **шаблон** > **изменить шаблон** , чтобы открыть файл **template. JSON** в интерактивном редакторе. 

10. Чтобы изменить целевой регион, в который будет перемещена виртуальная сеть, измените свойство **Location** в разделе **ресурсы** в сетевом редакторе:

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
 
11. Чтобы получить коды расположения регионов, см. раздел [расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Код для региона — это имя региона без пробелов, **Центральная американская** = **centralus**.
 
12. Кроме того, можно изменить другие параметры в шаблоне, если вы выбираете и являются необязательными в зависимости от ваших требований:
    
    * **Адресное пространство** . адресное пространство виртуальной сети можно изменить перед сохранением, изменив раздел **Resources** > **аддрессспаце** и изменив свойство **аддресспрефиксес** в файле **template. JSON** :

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

    * **Подсеть** — имя подсети и адресное пространство подсети можно изменить или добавить в, изменив раздел **подсети** файла **template. JSON** . Имя подсети можно изменить, изменив свойство **Name** . Адресное пространство подсети можно изменить, изменив свойство **addressPrefix** в файле **template. JSON** :

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

         В файле **template. JSON** для изменения префикса адреса его необходимо изменить в двух местах: в разделе, приведенном выше, и в разделе **Type** , приведенном ниже.  Измените свойство **addressPrefix** , чтобы оно совпадало с указанным выше:

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

13. Нажмите кнопку **сохранить** в интерактивном редакторе.

14. Щелкните **основные** > **подписки** , чтобы выбрать подписку, в которой будет развернута Целевая виртуальная сеть.

15. Щелкните **основные** > **ресурсы группа ресурсов** , чтобы выбрать группу ресурсов, в которой будет развернута Целевая виртуальная сеть.  Можно щелкнуть **создать** , чтобы создать новую группу ресурсов для целевой виртуальной сети.  Убедитесь, что имя не совпадает с исходной группой ресурсов существующей виртуальной сети. 

16. Убедитесь, что в качестве **основы** > **расположения** задано целевое расположение, в котором требуется развернуть виртуальную сеть.

17. Проверьте в разделе **Параметры** , имя которых совпадает с именем, введенным в редакторе параметров выше.

18. Установите флажок в разделе **условия**.

19. Нажмите кнопку **приобрести** , чтобы развернуть целевую виртуальную сеть.

## <a name="discard"></a>Отменить 

Если вы хотите отменить целевую виртуальную сеть, удалите группу ресурсов, содержащую целевую виртуальную сеть.  Для этого выберите группу ресурсов на панели мониторинга на портале и щелкните **Удалить** в верхней части страницы обзора.

## <a name="clean-up"></a>Очистка

Чтобы сохранить изменения и завершить перемещение виртуальной сети, удалите исходную виртуальную сеть или группу ресурсов. Для этого выберите виртуальную сеть или группу ресурсов на панели мониторинга на портале и щелкните **Удалить** в верхней части каждой страницы.

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы переместили виртуальную сеть Azure из одного региона в другой и очистили исходные ресурсы.  Дополнительные сведения о перемещении ресурсов между регионами и аварийным восстановлением в Azure см. по следующим ссылке:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
