---
title: Переместите балансиватор внешней нагрузки Azure в другой регион Azure с помощью портала Azure
description: Используйте шаблон менеджера ресурсов Azure для перемещения баланса внешней нагрузки из одного региона Azure в другой с помощью портала Azure.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638533"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Перемещение баланса внешней нагрузки в другой регион с помощью портала Azure

Существуют различные сценарии, в которых необходимо переместить балансирумы внешней нагрузки из одного региона в другой. Например, можно создать другой внешний балансера с внешней нагрузкой с той же конфигурацией для тестирования. Кроме того, в рамках планирования аварийного восстановления может потребоваться перемещение сальдо внешнего сальдо груза в другой регион.

В буквальном смысле вы не можете перемещать балансирумы внешней нагрузки Azure из одного региона в другой. Но для экспорта существующей конфигурации и общедоступного IP-адреса внешнего баланса нагрузки можно использовать шаблон менеджера ресурсов Azure. Затем можно разместить ресурс в другом регионе, экспортируя балансистер нагрузки и публичный IP в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблон в новый регион. Для получения дополнительной информации о [Export resource groups to templates](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)ресурсном менеджере и шаблонах см.


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что внешний балансер нагрузки Azure находится в регионе Azure, из которого требуется переместиться.

- Внешние балансиюры нагрузки Azure не могут перемещаться между регионами. Придется связать новый балансоилизатор нагрузки с ресурсами в целевом регионе.

- Для экспорта конфигурации баланса внешней нагрузки и развертывания шаблона для создания внешнего баланса нагрузки в другом регионе необходимо назначить роль элемента «Автор сети» или выше.

- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается баланселизаторами нагрузки, группами сетевой безопасности, публичными ими и виртуальными сетями.

- Убедитесь, что подписка Azure позволяет создавать внешние балансы нагрузки в целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что в подписке достаточно ресурсов для поддержки добавления балансов нагрузки. См. дополнительные сведения о [подписке Azure, границах, квотах и ограничениях службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Подготовка и перемещение
Следующие процедуры показывают, как подготовить сальдо внешнего груза к перемещению с помощью шаблона «Менеджер ресурсов» и перенести конфигурацию баланса внешней нагрузки в целевой регион с помощью портала Azure. Сначала необходимо экспортировать общедоступную конфигурацию IP-баланса внешней нагрузки.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Экспорт общедоступного шаблона IP и развертывание общедоступной ИС с портала

1. Вопийте на [портале Azure](https://portal.azure.com) и выберите **группы ресурсов.**
2. Найдите группу ресурсов, содержащую исходный общедоступный IP, и выберите его.
3. Выберите**шаблон «Экспорт** **настроек».** > 
4. Выберите **Развертывание** под **шаблоном Экспорта**.
5. Выберите параметры **template** > **Edit,** чтобы открыть файл parameters.json в онлайн-редакторе.
8. Чтобы отсеивать параметр публичного имени IP, измените свойство **значения** по **параметрам** от имени исходного публичного IP на имя вашего целевого публичного IP-адреса. Приложить имя в кавычки.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Выберите **Сохранить** в редакторе.

9.  Выберите шаблон **TEMPLATE** > **Edit,** чтобы открыть файл template.json в онлайн-редакторе.

10. Для отобрагиваем целевой регион, в который будет перемещен публичный IP, измените свойство **местоположения** под **ресурсами:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    Чтобы получить коды местоположений регионов, смотрите [места расположения Azure.](https://azure.microsoft.com/global-infrastructure/locations/) Код региона — это название региона без пробелов. Например, код для Центральной США является **центральным.**
    
12. Вы также можете изменить другие параметры в шаблоне, если вы хотите или нужно, в зависимости от ваших требований:

    * **SKU**. Вы можете изменить SKU публичного IP в конфигурации со стандарта на базовый или с базового на стандартный, изменив свойство **имени** под **sku** в файле template.json:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Для получения информации о различиях между базовыми и стандартными [IP-адресами](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)SKU см.

    * **Метод распределения IP и** **тайм-аут для простоя**. Можно изменить общедоступный метод распределения IP, изменив свойство **publicIPAllocationMethod** с **dynamic** на **Static** или с **Static** на **динамический.** Вы можете изменить время простоя, изменив свойство **idleoutInMinutes** на нужное значение. По умолчанию **4**.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Для получения информации о методах распределения и значениях простоя тайм-аута [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

 
13. Выберите **Сохранить** в онлайн-редакторе.

14. Выберите**подписку** **BASICS,** > чтобы выбрать подписку, в которой будет развернут целевой публичный IP.

15. Выберите**группу ресурсов** **BASICS,** > чтобы выбрать группу ресурсов, в которой будет развернут айб целевого публичного IP. Можно выбрать **Создание нового** для создания новой группы ресурсов для целевого публичного IP- иС. Убедитесь, что имя не совпадает с группой ресурсов исходного кода существующего исходного кода IP.

16. Проверить, что**местоположение** **BASICS** > настроено в целевом месте, где вы хотите, чтобы общественный IP был развернут.

17. В соответствии с **SETTINGS**убедитесь, что имя совпадает с именем, которое вы ввели ранее в редакторе параметров.

18. Выберите флажок **TERMS AND CONDITIONS.**

19. Выберите **Покупка** для развертывания целевого общедоступного IP- иС.

20. Если у вас есть другой общедоступный IP, который используется для исходящего NAT для перемещения балансомера нагрузки, повторите предыдущие шаги по экспорту и разместите второй исходящий публичный IP в целевой регион.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Экспорт шаблона баланса внешней нагрузки и развертывание балансо-баланса нагрузки с портала Azure

1. Вопийте на [портале Azure](https://portal.azure.com) и выберите **группы ресурсов.**
2. Найдите группу ресурсов, содержащую балансировора внешней нагрузки источника, и выберите его.
3. Выберите**шаблон «Экспорт** **настроек».** > 
4. Выберите **Развертывание** под **шаблоном Экспорта**.
5. Выберите параметры **template** > **Edit,** чтобы открыть файл parameters.json в онлайн-редакторе.

5. Для отослаить параметр имени сальдо внешнего груза, измените **свойство значения** имени баланса внешней нагрузки источника на имя вашего целевого внешнего сальдона нагрузки. Приложить имя в кавычки.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Чтобы отсеивать значение целевого публичного IP, которое вы перемещали в предыдущих шагах, необходимо сначала получить идентификатор ресурса, а затем вставить его в файл parameters.json. Для получения удостоверения личности:

    1. В другой вкладке или окне браузера вопийте на [порталaz](https://portal.azure.com) и выберите **группы ресурсов.**
    2. Найдите группу целевых ресурсов, содержащую общедоступный IP- испогружемый в предыдущих шагах. Выберите его.
    3. Выберите **Параметры** > **Свойства**.
    4. В лезвии справа выделите **идентификатор ресурса** и скопируйте его на буфер обмена. Кроме того, можно выбрать **копию буфера обмена** справа от пути **идентификатора ресурса.**
    5. Вставьте идентификатор ресурса в **свойство значения** в редакторе **параметров edit,** который открыт в другом окне или вкладке браузера:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Выберите **Сохранить** в онлайн-редакторе.


7.  Если вы настроили исходящие правила NAT и исходящих для балансоилира нагрузки, вы увидите третью запись в этом файле для внешнего идентификатора идентификатора исходящего публичного IP- иС. Повторите предыдущие шаги в **целевом регионе** для получения идентификатора для исходящего публичного IP- ис. Вставьте этот идентификатор в файл parameters.json:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Выберите шаблон **TEMPLATE** > **Edit,** чтобы открыть файл template.json в онлайн-редакторе.
9.  Для отображены йебовой области, в которую будет перемещена конфигурация баланса внешней нагрузки, измените свойство **местоположения** под **ресурсами** в файле template.json:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Чтобы получить коды местоположений регионов, смотрите [места расположения Azure.](https://azure.microsoft.com/global-infrastructure/locations/) Код региона — это название региона без пробелов. Например, код для Центральной США является **центральным.**

11. Вы также можете изменить другие параметры в шаблоне, если вы хотите или нужно, в зависимости от ваших требований:

    * **SKU**. Вы можете изменить SKU сбаланса внешней нагрузки в конфигурации со стандарта на базовый или с базового на стандартный, изменив свойство **имени** под **sku** в файле template.json:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Для получения информации о различиях между базовыми [Azure Standard Load Balancer overview](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)и стандартными балансиваторами нагрузки SKU см.

    * **Правила балансировки нагрузки.** Вы можете добавить или удалить правила балансировки нагрузки в конфигурации путем добавления или удаления записей в разделе **loadBalancingRules** файла template.json:

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
       Для получения информации о [What is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)правилах балансировки нагрузки см.

    * **Зонды**. Можно добавить или удалить зонд для балансоилира нагрузки в конфигурации, добавив или удалив записи в разделе **зондов** файла template.json:

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
       Для получения дополнительной [информации](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)см.

    * **Входящие правила NAT**. Вы можете добавить или удалить входящие правила NAT для балансирутеля нагрузки, добавив или удалив записи в разделе **inboundNatRules** файла template.json:

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
        Для завершения добавления или удаления входящего правила NAT правило должно присутствовать или удаляться в качестве свойства **типа** в конце файла template.json:

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
        Для получения информации о входящих правилах NAT [см.](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Правила выезда**. Вы можете добавить или удалить исходящие правила в конфигурации, редактируя свойство **outboundRules** в файле template.json:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Для получения дополнительной [информации](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)см.

12. Выберите **Сохранить** в онлайн-редакторе.

13. Выберите**подписку** **BASICS,** > чтобы выбрать подписку, в которой будет развернут целевой внешний балансерагруз.

15. Выберите**группу ресурсов** **BASICS,** > чтобы выбрать группу ресурсов, в которой будет развернут балансилер целевой нагрузки. Можно выбрать **Создание нового** для создания новой группы ресурсов для целевого внешнего баланса нагрузки. Или вы можете выбрать существующую группу ресурсов, созданную ранее для общедоступной ИС. Убедитесь, что имя не совпадает с группой ресурсов исходного кода существующего баланса внешней нагрузки источника.

16. Проверить, что**местоположение** **BASICS** > настроено в целевом месте, где требуется развертывание баланса внешней нагрузки.

17. В соответствии с **SETTINGS**убедитесь, что имя совпадает с именем, которое вы ввели ранее в редакторе параметров. Убедитесь, что идентиматы ресурсов заполнены для любых общедоступных ИП в конфигурации.

18. Выберите флажок **TERMS AND CONDITIONS.**

19. Выберите **Покупка** для развертывания целевого общедоступного IP- иС.

## <a name="discard"></a>Игнорировать

Если вы хотите отказаться от целевого публичного IP и внешнего баланса нагрузки, удалите группу ресурсов, которая их содержит. Для этого выберите группу ресурсов из панели мониторинга на портале, а затем выберите **Удалить** в верхней части страницы обзора.

## <a name="clean-up"></a>Очистка

Чтобы совершить изменения и завершить перемещение публичного ip-баланса и внешней нагрузки, удалите исходное публичное IP и внешний балансеер нагрузки или группу ресурсов. Для этого выберите эту группу ресурсов из панели мониторинга на портале, а затем выберите **Удалить** в верхней части каждой страницы.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы переместили балансируев внешней нагрузки Azure из одного региона в другой и очистили исходные ресурсы. Подробнее о перемещении ресурсов между регионами и аварийном восстановлении в Azure можно узнать:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
