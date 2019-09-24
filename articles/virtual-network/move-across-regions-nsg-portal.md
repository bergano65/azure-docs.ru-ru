---
title: Перемещение группы безопасности сети Azure (NSG) в другой регион Azure с помощью портал Azure
description: Используйте шаблон Azure Resource Manager, чтобы переместить группу безопасности сети Azure из одного региона Azure в другой с помощью портал Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 839e608aa4bba26712ae5b0c160da40db279bbc9
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219186"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Перемещение группы безопасности сети Azure (NSG) в другой регион с помощью портал Azure

Существуют различные сценарии, в которых необходимо переместить существующий группы безопасности сети из одного региона в другой. Например, может потребоваться создать NSG с теми же конфигурацией и правилами безопасности для тестирования. Вы также можете переместить NSG в другой регион в рамках планирования аварийного восстановления.

Группы безопасности Azure нельзя перемещать из одного региона в другой. Однако можно использовать шаблон Azure Resource Manager для экспорта существующих правил конфигурации и безопасности NSG.  Затем можно разместить ресурс в другом регионе, экспортировав NSG в шаблон, изменив параметры в соответствии с регионом назначения, а затем развернув шаблон в новом регионе.  Дополнительные сведения о Диспетчер ресурсов и шаблонах см. [в разделе Краткое руководство. по созданию и развертыванию шаблонов Azure Resource Manager с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что группа безопасности сети Azure находится в регионе Azure, из которого вы хотите переместиться.

- Группы безопасности сети Azure нельзя перемещать между регионами.  Необходимо связать новый NSG с ресурсами в целевом регионе.

- Чтобы экспортировать конфигурацию NSG и развернуть шаблон для создания NSG в другом регионе, вам потребуется роль «участник сети» или выше.

- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает, но не ограничен подсистемами балансировки нагрузки, общедоступными IP-адресами и виртуальными сетями.

- Убедитесь, что ваша подписка Azure позволяет создать группы безопасности сети в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что у вашей подписки достаточно ресурсов для поддержки добавления группы безопасности сети для этого процесса.  Ознакомьтесь со статьей [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Ниже показано, как подготовить группу безопасности сети для переноса правила конфигурации и безопасности с помощью шаблона диспетчер ресурсов и переместить конфигурацию NSG и правила безопасности в целевую область с помощью портала.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Экспорт шаблона и его развертывание на портале

1. Войдите в [портал Azure](https://portal.azure.com) > **группы ресурсов**.
2. Выберите группу ресурсов, содержащую исходный NSG, и щелкните ее.
3. Выберите **Параметры** > >**Экспорт шаблона**.
4. Выберите **развернуть** в колонке **Экспорт шаблона** .
5. Щелкните **шаблон** > **изменить параметры** , чтобы открыть файл **Parameters. JSON** в интерактивном редакторе.
6. Чтобы изменить параметр имени NSG, измените **значение свойства Value** в разделе **Параметры**:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Измените значение Source NSG в редакторе на нужное имя для целевого NSG. Обязательно заключите имя в кавычки.

8.  Нажмите кнопку **сохранить** в редакторе.

9.  Щелкните **шаблон** > **изменить шаблон** , чтобы открыть файл **template. JSON** в интерактивном редакторе.

10. Чтобы изменить целевой регион, в который будут перемещены конфигурация NSG и правила безопасности, измените свойство **Location** в разделе **ресурсы** в сетевом редакторе:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Чтобы получить коды расположения регионов, см. раздел [расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Код для региона — это имя региона без пробелов, **Центральная американская** = **centralus**.

12. Кроме того, можно изменить другие параметры в шаблоне, если вы выбираете и являются необязательными в зависимости от ваших требований:

    * **Правила безопасности** . правила, которые развертываются в целевом NSG, можно изменить, добавив или удалив правила в разделе **securityRules** файла **template. JSON** :

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Чтобы завершить добавление или удаление правил в целевом NSG, необходимо также изменить типы настраиваемых правил в конце файла **template. JSON** в следующем формате:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Нажмите кнопку **сохранить** в интерактивном редакторе.

14. Щелкните **основные** > **подписки** , чтобы выбрать подписку, в которой будет развернут целевой NSG.

15. Щелкните **основные** > **ресурсы группа ресурсов** , чтобы выбрать группу ресурсов, в которой будет развернут целевой NSG.  Можно щелкнуть **создать** , чтобы создать группу ресурсов для целевого NSG.  Убедитесь, что имя не совпадает с исходной группой ресурсов существующего NSG.

16. Убедитесь, что в качестве **основы** > **расположения** задано целевое расположение, в котором требуется развернуть NSG.

17. Проверьте в разделе **Параметры** , имя которых совпадает с именем, введенным в редакторе параметров выше.

18. Установите флажок в разделе **условия**.

19. Нажмите кнопку **приобрести** , чтобы развернуть целевую группу безопасности сети.

## <a name="discard"></a>Отменить

Если вы хотите удалить целевой NSG, удалите группу ресурсов, содержащую целевой NSG.  Для этого выберите группу ресурсов на панели мониторинга на портале и щелкните **Удалить** в верхней части страницы обзора.

## <a name="clean-up"></a>Очистка

Чтобы сохранить изменения и завершить перемещение NSG, удалите исходный NSG или группу ресурсов. Для этого выберите группу безопасности сети или группу ресурсов на панели мониторинга на портале и щелкните **Удалить** в верхней части каждой страницы.

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы переместили группу безопасности сети Azure из одного региона в другой и очистили исходные ресурсы.  Дополнительные сведения о перемещении ресурсов между регионами и аварийным восстановлением в Azure см. по следующим ссылке:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
