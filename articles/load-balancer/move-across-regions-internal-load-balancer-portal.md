---
title: Переместите внутренний балансер загрузки Azure в другой регион Azure с помощью портала Azure
description: Используйте шаблон менеджера ресурсов Azure для перемещения баланса внутренней нагрузки Azure из одного региона Azure в другой с помощью портала Azure
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638815"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Переместите балансировора внутренней нагрузки Azure в другой регион с помощью портала Azure

Существуют различные сценарии, в которых необходимо переместить существующий балансера внутренней нагрузки из одного региона в другой. Например, можно создать внутренний балансиватор нагрузки с той же конфигурацией для тестирования. Возможно, необходимо переместить балансируг внутренней нагрузки в другой регион в рамках планирования аварийного восстановления.

Балансиваторы внутренней нагрузки Azure не могут быть перемещены из одного региона в другой. Однако можно использовать шаблон Azure Resource Manager для экспорта существующей конфигурации и виртуальной сети внутреннего балансируза.  Затем можно разместить ресурс в другом регионе, экспортируя балансистер нагрузки и виртуальную сеть в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблоны в новом регионе.  Для получения дополнительной информации об [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)управлении ресурсами и шаблонах см.


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что балансизатор внутренней нагрузки Azure находится в регионе Azure, из которого требуется переместиться.

- Балансиваторы внутренней нагрузки Azure не могут перемещаться между регионами.  Придется связать новый балансоилизатор нагрузки с ресурсами в целевом регионе.

- Для экспорта конфигурации баланса внутренней нагрузки и развертывания шаблона для создания баланса внутренней нагрузки в другом регионе потребуется роль элемента «Сетевой вклад» или выше.

- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается баланселизаторами нагрузки, группами сетевой безопасности, виртуальными машинами и виртуальными сетями.

- Убедитесь, что подписка Azure позволяет создавать внутренние балансы нагрузки в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что ваша подписка имеет достаточно ресурсов для поддержки добавления балансеров нагрузки для этого процесса.  Просматривайте [ограничения подписки и обслуживания Azure, квоты и ограничения](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Подготовка и перемещение
Следующие шаги показывают, как подготовить баланселизатор внутренней нагрузки для перемещения с помощью шаблона «Менеджер ресурсов» и перенести конфигурацию баланса внутренней нагрузки в целевой регион с помощью портала Azure.  В рамках этого процесса, виртуальная конфигурация сети внутреннего баланса нагрузки должны быть включены и должны быть сделаны в первую очередь перед перемещением внутреннего баланса нагрузки.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Экспорт виртуального шаблона сети и развертывание с портала Azure

1. Войти в > **ресурсные группы** [портала Azure](https://portal.azure.com).
2. Найдите группу ресурсов, которая содержит виртуальную сеть исходного кода, и нажмите на нее.
3. Выберите**шаблон экспорта** **> настройки.** > 
4. Выберите **развертывание** в лезвии **шаблона Экспорта.**
5. Нажмите параметры **template** > **Edit,** чтобы открыть файл **parameters.json** в онлайн-редакторе.
6. Для отсечения параметра названия виртуальной сети измените свойство **значения** по **параметрам:**

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
7. Измените значение исходного виртуального значения названия сети в редакторе на имя по вашему выбору для целевого VNET. Убедитесь, что вы приложить имя в кавычки.

8. Нажмите **Сохранить** в редакторе.

9. Нажмите шаблон **TEMPLATE** > **Edit,** чтобы открыть файл **template.json** в онлайн-редакторе.

10. Для отображаем область целевого значения, в которой будет перемещен VNET, измените свойство **местоположения** под ресурсами:

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

11. Для получения кодов местоположений регионов [см.](https://azure.microsoft.com/global-infrastructure/locations/)  Код для региона является название региона без каких-либо пространств, **Центральный** = **центральный**США .

12. Вы также можете изменить другие параметры в файле **template.json,** если вы выберете, и являются необязательными в зависимости от ваших требований:

    * **Адресное пространство** - Адресное пространство VNET может быть изменено до экономии, изменив раздел**addressSpace** **ресурсов** > и изменив свойство **адресаPrefixes** в файле **template.json:**

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

    * **Subnet** - Имя подсети и пространство адреса подсети могут быть изменены или добавлены путем изменения раздела **подсетей** файла **template.json.** Название подсети может быть изменено путем изменения свойства **имени.** Пространство адреса подсети можно изменить, изменив свойство **адресаPrefix** в файле **template.json:**

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

         В файле **template.json,** чтобы изменить префикс адреса, он должен быть отредактирован в двух местах, раздел, перечисленный выше, и **раздел типа,** перечисленные ниже.  Измените свойство **адресPrefix,** чтобы соответствовать вышеуказанному:

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

13. Нажмите **Сохранить** в онлайн-редакторе.

14. Нажмите **BASICS** > **Подписка,** чтобы выбрать подписку, где целевой VNET будет развернута.

15. Нажмите**группу ресурсов** **BASICS,** > чтобы выбрать группу ресурсов, в которой будет развернута цель VNET.  Вы можете нажать **Создать новый** для создания новой группы ресурсов для целевой VNET.  Убедитесь, что имя не совпадает с группой исходных ресурсов существующего VNET.

16. Проверка**местоположения** **BASICS** > устанавливается в целевом месте, где вы хотите, чтобы VNET был развернут.

17. **Убедитесь,** что имя соответствует имени, которое вы ввели в приведенный выше редактор параметров.

18. Проверьте поле под **TERMS И КОНДИИ.**

19. Нажмите кнопку **«Покупка»,** чтобы развернуть целевую виртуальную сеть.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Экспорт шаблона внутреннего баланса нагрузки и развертывание с Azure PowerShell

1. Войти в > **ресурсные группы** [портала Azure](https://portal.azure.com).
2. Найдите группу ресурсов, содержащую балансизатор внутренней нагрузки и нажмите на него.
3. Выберите**шаблон экспорта** **> настройки.** > 
4. Выберите **развертывание** в лезвии **шаблона Экспорта.**
5. Нажмите параметры **template** > **Edit,** чтобы открыть файл **parameters.json** в онлайн-редакторе.

6. Для отсечения параметра имени внутреннего балансомливщика нагрузки измените значение свойства **значения** баланса внутренней нагрузки на имя вашего целевого внутреннего балансоилира нагрузки, убедитесь, что имя находится в кавычках:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Чтобы отсеивать значение целевой виртуальной сети, которая была перемещена выше, необходимо сначала получить идентификатор ресурса, а затем скопировать и вставить его в файл **parameters.json.** Для получения удостоверения личности:

    1. Войти в > **группу ресурсов** [портала Azure](https://portal.azure.com)в другой вкладке или окне браузера.
    2. Найдите группу целевых ресурсов, содержащую перемещенную виртуальную сеть с вышеуказанных шагов, и нажмите на нее.
    3. Выберите**свойства** **> настройки.** > 
    4. В лезвии справа выделите **идентификатор ресурса** и скопируйте его на буфер обмена.  Кроме того, вы можете нажать на **копию, чтобы** буфер обмена кнопку справа от пути **идентификатора ресурсов.**
    5. Вставьте идентификатор ресурса в свойство **defaultValue** в редактор **Edit Parameters,** открытый в другом окне или вкладке браузера:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Нажмите **Сохранить** в онлайн-редакторе.

7. Нажмите шаблон **TEMPLATE** > **Edit,** чтобы открыть файл **template.json** в онлайн-редакторе.
8. Для отображеный целевой области, в которой будет перемещена конфигурация баланса внутренней нагрузки, измените свойство **местоположения** под **ресурсами** в файле **template.json:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Для получения кодов местоположений регионов [см.](https://azure.microsoft.com/global-infrastructure/locations/)  Код для региона является название региона без каких-либо пространств, **Центральный** = **центральный**США .

10. Вы также можете изменить другие параметры в шаблоне, если вы выберете, и являются необязательными в зависимости от ваших требований:

    * **Sku** - Вы можете изменить sku внутреннего баланса нагрузки в конфигурации от стандартного к базовому или базовому к стандарту, изменив свойство **sku** > **имя** в файле **template.json:**

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Для получения дополнительной информации о различиях между основными [Azure Standard Load Balancer overview](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) и стандартными балансиваторами нагрузки sku, см.

    * **Правила балансировки нагрузки** - Вы можете добавить или удалить правила балансировки нагрузки в конфигурации, добавив или удалив записи в раздел **loadBalancingRules** файла **template.json:**

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Для получения дополнительной информации о [What is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) правилах балансировки нагрузки см.

    * **Зонды** - Вы можете добавить или удалить зонд для балансораля нагрузки в конфигурации, добавив или удалив **записи** в раздел зондов **файла template.json:**

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Для получения дополнительной информации о зондах [Load Balancer health probes](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) здоровья Azure Load Balancer см.

    * **Входящие правила NAT** - Вы можете добавить или удалить входящие правила NAT для балансиватора нагрузки, добавив или удалив записи в раздел **inboundNatRules** **файла template.json:**

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Для завершения добавления или удаления входящего правила NAT правило должно присутствовать или удаляться в качестве свойства **типа** в конце файла **template.json:**

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Для получения дополнительной информации о [What is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) входящих правилах NAT см.

12. Нажмите **Сохранить** в онлайн-редакторе.

13. Нажмите **BASICS** > **Подписка,** чтобы выбрать подписку, где будет развернут целевой внутренний балансер нагрузки.

15. Нажмите**группу ресурсов** **BASICS,** > чтобы выбрать группу ресурсов, где будет развернут баланселизатор целевой нагрузки.  Вы можете нажать **Создать новый** для создания новой группы ресурсов для целевой внутренней нагрузки балансиста или выбрать существующую группу ресурсов, которая была создана выше для виртуальной сети.  Убедитесь, что имя не совпадает с группой ресурсов исходного кода существующего баланса внутренней нагрузки источника.

16. Проверка**местоположения** **BASICS** > устанавливается в целевом месте, где вы хотите, чтобы внутренний баланселизатор нагрузки, которые будут развернуты.

17. **Убедитесь,** что имя соответствует имени, которое вы ввели в приведенный выше редактор параметров.  Проверка идентиверий ресурсов населена для любых виртуальных сетей в конфигурации.

18. Проверьте поле под **TERMS И КОНДИИ.**

19. Нажмите кнопку **«Покупка»,** чтобы развернуть целевую виртуальную сеть.

## <a name="discard"></a>Игнорировать

Если вы хотите отказаться от целевой виртуальной сети и внутреннего балансиста нагрузки, удалите группу ресурсов, содержащую целевую виртуальную сеть и внутренний балансерагруз нагрузки.  Для этого выберите группу ресурсов из панели мониторинга на портале и выберите **Удалить** в верхней части страницы обзора.

## <a name="clean-up"></a>Очистка

Чтобы совершить изменения и завершить перемещение виртуальной сети и внутреннего балансируза нагрузки, удалите исходную виртуальную сеть и внутренний баланселизатор нагрузки или группу ресурсов. Для этого выберите виртуальную сеть и балансизатор внутренней нагрузки или группу ресурсов из панели мониторинга на портале и выберите **Удалить** в верхней части каждой страницы.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы переместили балансируев внутренней нагрузки Azure из одного региона в другой и очистили исходные ресурсы.  Чтобы узнать больше о перемещении ресурсов между регионами и аварийном восстановлении в Azure, обратитесь к:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
