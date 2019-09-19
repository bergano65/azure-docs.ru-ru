---
title: Перемещение внешних Load Balancer Azure в другой регион Azure с помощью портал Azure
description: Используйте шаблон Azure Resource Manager, чтобы переместить внешние Load Balancer Azure из одного региона Azure в другой с помощью портал Azure.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 9cf15595270e0a0040e565be8319c13e3f5da306
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092226"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-the-azure-portal"></a>Перемещение внешних Load Balancer Azure в другой регион с помощью портал Azure

Существуют различные сценарии, в которых необходимо переместить имеющуюся внешнюю подсистему балансировки нагрузки из одного региона в другой. Например, может потребоваться создать внешнюю подсистему балансировки нагрузки с той же конфигурацией для тестирования. При планировании аварийного восстановления может также потребоваться переместить внешнюю подсистему балансировки нагрузки в другой регион.

Внешние подсистемы балансировки нагрузки Azure невозможно переместить из одного региона в другой. Однако можно использовать шаблон Azure Resource Manager для экспорта существующей конфигурации и общедоступного IP-адреса внешней подсистемы балансировки нагрузки.  Затем можно разместить ресурс в другом регионе, экспортировав подсистему балансировки нагрузки и общедоступный IP-адрес в шаблон, изменив параметры в соответствии с регионом назначения, а затем развернув шаблоны в новом регионе.  Дополнительные сведения о диспетчер ресурсов и шаблонах см. [в статье экспорт групп ресурсов в шаблоны](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) .


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что внешняя подсистема балансировки нагрузки Azure находится в регионе Azure, из которого вы хотите переместиться.

- Внешние подсистемы балансировки нагрузки Azure нельзя перемещать между регионами.  Необходимо связать новую подсистему балансировки нагрузки с ресурсами в целевом регионе.

- Чтобы экспортировать конфигурацию внешней подсистемы балансировки нагрузки и развернуть шаблон для создания внешней подсистемы балансировки нагрузки в другом регионе, вам потребуется роль "участник сети" или выше.
   
- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает, но не ограничен подсистемами балансировки нагрузки, группами безопасности сети, общедоступными IP-адресами и виртуальными сетями.

- Убедитесь, что подписка Azure позволяет создавать внешние подсистемы балансировки нагрузки в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что у вашей подписки достаточно ресурсов для поддержки добавления подсистем балансировки нагрузки для этого процесса.  См. статью [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Ниже описано, как подготовить внешнюю подсистему балансировки нагрузки для перемещения с помощью шаблона диспетчер ресурсов и переместить конфигурацию внешней подсистемы балансировки нагрузки в целевую область с помощью портал Azure.  В рамках этого процесса должна быть включена общедоступная IP-конфигурация внешней подсистемы балансировки нагрузки, которая должна быть выполнена перед перемещением внешней подсистемы балансировки нагрузки.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-the-portal"></a>Экспорт шаблона общедоступного IP-адреса и его развертывание на портале

1. Войдите в [портал Azure](http://portal.azure.com) > **группы ресурсов**.
2. Выберите группу ресурсов, содержащую общедоступный исходный IP-адрес, и щелкните ее.
3. Выберите **Параметры** > >**Экспорт шаблона**.
4. Выберите **развернуть** в колонке **Экспорт шаблона** .
5. Щелкните **шаблон** > **изменить параметры** , чтобы открыть файл **Parameters. JSON** в интерактивном редакторе.
8. Чтобы изменить параметр имени общедоступного IP-адреса, измените**значение** свойства в разделе **Параметры** > из исходного общедоступного IP-адреса на имя целевого общедоступного IP-адреса и убедитесь, что имя указано в кавычках:

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

    Нажмите кнопку **сохранить** в редакторе.

9.  Щелкните **шаблон** > **изменить шаблон** , чтобы открыть файл **template. JSON** в интерактивном редакторе. 

10. Чтобы изменить целевой регион, в который будет перемещен общедоступный IP-адрес, измените свойство **Location** в разделе **ресурсы**:

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
  
11. Чтобы получить коды расположения регионов, см. раздел [расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Код для региона — это имя региона без пробелов, **Центральная американская** = **centralus**.
    
12. Кроме того, можно изменить другие параметры в шаблоне, если вы выбираете и являются необязательными в зависимости от ваших требований:

    * **SKU** . Вы можете изменить номер SKU общедоступного IP-адреса в конфигурации с Standard на Basic или Basic на Standard, изменив свойство**имя** **SKU** > в файле **template. JSON** :

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

        Дополнительные сведения о различиях между общедоступными IP-адресами уровня "базовый" и "Стандартный" см. в статье [Создание, изменение или удаление общедоступного адреса](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Метод выделения общедоступного IP-адреса** и **время ожидания простоя** . Вы можете изменить оба этих параметра в шаблоне, изменив свойство **публиЦипаллокатионмесод** с **dynamic** на **static** или **static** на **dynamic.** . Время ожидания простоя можно изменить, изменив свойство **идлетимеаутинминутес** на желаемое значение.  Значение по умолчанию — **4**:

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

        Дополнительные сведения о методах выделения и значениях времени ожидания простоя см. в разделе [Создание, изменение или удаление общедоступного IP-адреса](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Нажмите кнопку **сохранить** в интерактивном редакторе.

14. Щелкните **основные** > **подписки** , чтобы выбрать подписку, в которой будет развернут целевой общедоступный IP-адрес.

15. Щелкните **основные** > **ресурсы группа ресурсов** , чтобы выбрать группу ресурсов, в которой будет развернут целевой общедоступный IP-адрес.  Можно щелкнуть **создать** , чтобы создать новую группу ресурсов для целевого общедоступного IP-адреса.  Убедитесь, что имя не совпадает с исходной группой ресурсов существующего исходного общедоступного IP-адреса. 

16. Убедитесь, что в качестве **основы** > **расположения** выбрано целевое расположение, в котором требуется развернуть общедоступный IP-адрес.

17. Проверьте в разделе **Параметры** , имя которых совпадает с именем, введенным в редакторе параметров выше.

18. Установите флажок в разделе **условия**.

19. Нажмите кнопку **приобрести** , чтобы развернуть целевой общедоступный IP-адрес.
20. Если у вас есть другой общедоступный IP-адрес, используемый для исходящего трафика NAT для перемещаемой подсистемы балансировки нагрузки, повторите описанные выше действия, чтобы экспортировать и развернуть второй исходящий общедоступный IP-адрес в целевом регионе.

### <a name="export-the-external-load-balancer-template-and-deploy-from-the-azure-portal"></a>Экспорт шаблона внешней подсистемы балансировки нагрузки и развертывание из портал Azure

1. Войдите в [портал Azure](http://portal.azure.com) > **группы ресурсов**.
2. Выберите группу ресурсов, содержащую источник внешней подсистемы балансировки нагрузки, и щелкните ее.
3. Выберите **Параметры** > >**Экспорт шаблона**.
4. Выберите **развернуть** в колонке **Экспорт шаблона** .
5. Щелкните **шаблон** > **изменить параметры** , чтобы открыть файл **Parameters. JSON** в интерактивном редакторе.

5. Чтобы изменить параметр имени внешней подсистемы балансировки нагрузки, измените **значение** свойства имя исходной внешней подсистемы балансировки нагрузки на имя целевой внешней подсистемы балансировки нагрузки и убедитесь, что имя находится в кавычках:

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

6.  Чтобы изменить значение целевого общедоступного IP-адреса, который был перемещен выше, сначала необходимо получить идентификатор ресурса, а затем скопировать и вставить его в файл **примечанием. JSON** .  Чтобы получить идентификатор:
    
    1. Войдите в [портал Azure](http://portal.azure.com) > **группы ресурсов** на другой вкладке или в окне браузера.
    2. Выберите целевую группу ресурсов, содержащую перемещенный общедоступный IP-адрес, из описанных выше действий и щелкните его.
    3. Выберите **Параметры** > >**Свойства**.
    4. В колонке справа выделите **идентификатор ресурса** и скопируйте его в буфер обмена.  Кроме того, можно щелкнуть кнопку **Копировать в буфер обмена** справа от параметра путь к **идентификатору ресурса** .
    5. Вставьте идентификатор ресурса в свойство **значение** в редакторе редактор **параметров** открыть в другом окне браузера или на вкладке:

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
    6. Нажмите кнопку **сохранить** в интерактивном редакторе.
   

7.  Если вы настроили исходящие правила NAT и исходящего трафика для балансировщика нагрузки, в этом файле будет указана третья запись для внешнего идентификатора исходящего общедоступного IP-адреса.  Повторите описанные выше действия в **целевом регионе** , чтобы получить идентификатор исходящего общедоступного IP-адреса и вставить эту запись в файл **Parameters. JSON** :

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

8.  Щелкните **шаблон** > **изменить шаблон** , чтобы открыть файл **template. JSON** в интерактивном редакторе.
9.  Чтобы изменить целевой регион, в который будет перемещена конфигурация внешней подсистемы балансировки нагрузки, измените свойство **Location** в разделе **ресурсы** в файле **template. JSON** :

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

10. Чтобы получить коды расположения регионов, см. раздел [расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Код для региона — это имя региона без пробелов, **Центральная американская** = **centralus**.

11. Кроме того, можно изменить другие параметры в шаблоне, если вы выбираете и являются необязательными в зависимости от ваших требований:
    
    * **SKU** . номер SKU внешней подсистемы балансировки нагрузки можно изменить в конфигурации с Standard на Basic или Basic на Standard, изменив свойство**имя** **SKU** > в файле **template. JSON** :

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
      Дополнительные сведения о различиях между подсистемами балансировки нагрузки уровня "базовый" и "Стандартный" см. в статье [обзор Load Balancer (цен. Категория "Стандартный") Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) .

    * **Правила балансировки нагрузки** . в конфигурацию можно добавить или удалить правила балансировки нагрузки, добавив или удалив записи в разделе **лоадбаланЦингрулес** файла **template. JSON** :

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
       Дополнительные сведения о правилах балансировки нагрузки см [. в разделе что такое Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Пробы** . Вы можете добавить или удалить пробу для балансировщика нагрузки в конфигурации, добавив или удалив записи в разделе **зонды** файла **template. JSON** :

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
       Дополнительные сведения о Azure Load Balancer зондах работоспособности см. в разделе [проверки работоспособности Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) .

    * **Правила NAT для входящего трафика** . для подсистемы балансировки нагрузки можно добавить или удалить правила NAT для входящего трафика, добавив или удалив записи в разделе **inboundNatRules** файла **template. JSON** :

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
        Чтобы завершить добавление или удаление правила NAT для входящего трафика, оно должно присутствовать или удалено в качестве свойства **типа** в конце файла **template. JSON** :

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
        Дополнительные сведения о правилах NAT для входящего трафика см. в разделе [что такое Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Правила исходящего трафика** . Вы можете добавить или удалить правила исходящего трафика в конфигурации, изменив свойство **аутбаундрулес** в файле **template. JSON** :

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

         Дополнительные сведения о правилах исходящих подключений см. в разделе [Load Balancer правила исходящего трафика](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview) .

12. Нажмите кнопку **сохранить** в интерактивном редакторе.
    
13. Щелкните **основные** > **подписки** , чтобы выбрать подписку, в которой будет развернута Целевая внешняя подсистема балансировки нагрузки.

15. Щелкните **основные** > **ресурсы группа ресурсов** , чтобы выбрать группу ресурсов, в которой будет развернута Целевая подсистема балансировки нагрузки.  Можно щелкнуть **создать** , чтобы создать новую группу ресурсов для целевой внешней подсистемы балансировки нагрузки, или выбрать существующую группу ресурсов, созданную ранее для общедоступного IP-адреса.  Убедитесь, что имя не совпадает с исходной группой ресурсов существующего источника внешняя подсистема балансировки нагрузки. 

16. Убедитесь, что в качестве **основы** > **расположения** выбрано целевое расположение, в котором требуется развернуть внешнюю подсистему балансировки нагрузки.

17. Проверьте в разделе **Параметры** , имя которых совпадает с именем, введенным в редакторе параметров выше.  Убедитесь, что идентификаторы ресурсов заполнены для всех общедоступных IP-адресов в конфигурации.

18. Установите флажок в разделе **условия**.

19. Нажмите кнопку **приобрести** , чтобы развернуть целевой общедоступный IP-адрес.

## <a name="discard"></a>Отменить 

Если вы хотите отменить целевой общедоступный IP-адрес и внешнюю подсистему балансировки нагрузки, удалите группу ресурсов, содержащую целевой общедоступный IP-адрес и внешнюю подсистему балансировки нагрузки.  Для этого выберите группу ресурсов на панели мониторинга на портале и щелкните **Удалить** в верхней части страницы обзора.

## <a name="clean-up"></a>Очистка

Чтобы зафиксировать изменения и завершить перемещение общедоступного IP-адреса и внешней подсистемы балансировки нагрузки, удалите исходный общедоступный IP-адрес и внешнюю подсистему балансировки нагрузки или группу ресурсов. Для этого выберите общедоступный IP-адрес и внешнюю подсистему балансировки нагрузки или группу ресурсов на панели мониторинга на портале и щелкните **Удалить** в верхней части каждой страницы.

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы переместили внешнюю подсистему балансировки нагрузки Azure из одного региона в другой и очистили исходные ресурсы.  Дополнительные сведения о перемещении ресурсов между регионами и аварийным восстановлением в Azure см. по следующим ссылке:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
