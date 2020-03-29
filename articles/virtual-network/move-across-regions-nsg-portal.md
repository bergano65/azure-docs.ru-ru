---
title: Переместите группу сетевой безопасности Azure (NSG) в другой регион Azure с помощью портала Azure
description: Используйте шаблон Azure Resource Manager, чтобы переместить группу сетевой безопасности Azure из одного региона Azure в другой с помощью портала Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647192"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Перемещение группы сетевой безопасности Azure (NSG) в другой регион с помощью портала Azure

Существуют различные сценарии, в которых необходимо переместить существующие НСГ из одного региона в другой. Например, можно создать NSG с той же конфигурацией и правилами безопасности для тестирования. Вы также можете переместить NSG в другой регион в рамках планирования аварийного восстановления.

Группы безопасности Azure не могут быть перемещены из одного региона в другой. Однако можно использовать шаблон менеджера ресурсов Azure для экспорта существующей конфигурации и правил безопасности НСГ.  Затем можно разместить ресурс в другом регионе, экспортируя NSG в шаблон, изменяя параметры в соответствии с регионом назначения, а затем развертывая шаблон в новом регионе.  Для получения дополнительной информации об [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)управлении ресурсами и шаблонах см.


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что группа сетевой безопасности Azure находится в регионе Azure, из которого требуется переместиться.

- Группы безопасности сетей Azure не могут перемещаться между регионами.  Вам придется связать новую NSG с ресурсами в целевом регионе.

- Для экспорта конфигурации NSG и развертывания шаблона для создания NSG в другом регионе вам понадобится роль элемента «Сетевой вклад» или выше.

- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Этот макет включает в себя, но не ограничивается балансомназаний нагрузки, публичных I-е и виртуальных сетей.

- Убедитесь, что подписка Azure позволяет создавать НСГ в используемом целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что ваша подписка имеет достаточно ресурсов для поддержки добавления НСУ для этого процесса.  См. дополнительные сведения о [подписке Azure, границах, квотах и ограничениях службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Следующие шаги показывают, как подготовить группу сетевой безопасности для перемещения конфигурации и правила безопасности с помощью шаблона resource Manager и перенести настройку и правила безопасности NSG в целевой регион с помощью портала.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Экспорт шаблона и развертывание с портала

1. Войти в > **ресурсные группы** [портала Azure](https://portal.azure.com).
2. Найдите группу ресурсов, содержащую источник NSG, и нажмите на него.
3. Выберите**шаблон экспорта** **> настройки.** > 
4. Выберите **развертывание** в лезвии **шаблона Экспорта.**
5. Нажмите параметры **template** > **Edit,** чтобы открыть файл **parameters.json** в онлайн-редакторе.
6. Для отсечения параметра имени NSG измените свойство **значения** по **параметрам:**

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

7. Измените значение источника NSG в редакторе на имя по вашему выбору для целевой NSG. Убедитесь, что вы приложить имя в кавычки.

8.  Нажмите **Сохранить** в редакторе.

9.  Нажмите шаблон **TEMPLATE** > **Edit,** чтобы открыть файл **template.json** в онлайн-редакторе.

10. Для отображаем целевой области, в которой будет перемещена конфигурация NSG и правила безопасности, измените свойство **местоположения** под **ресурсами** в онлайн-редакторе:

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

11. Для получения кодов местоположений регионов [см.](https://azure.microsoft.com/global-infrastructure/locations/)  Код для региона является название региона без каких-либо пространств, **Центральный** = **центральный**США .

12. Вы также можете изменить другие параметры в шаблоне, если вы выберете, и являются необязательными в зависимости от ваших требований:

    * **Правила безопасности** - Вы можете отсеивать, какие правила развернуты в целевой NSG, добавляя или удаляя правила в раздел **securityRules** в файле **template.json:**

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

      Для завершения добавления или удаления правил в целевой NSG необходимо также отсеивать типы пользовательских правил в конце файла **template.json** в формате приведенного ниже примера:

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

13. Нажмите **Сохранить** в онлайн-редакторе.

14. Нажмите **BASICS** > **Подписка,** чтобы выбрать подписку, где целевой NSG будет развернута.

15. Нажмите**группу ресурсов** **BASICS,** > чтобы выбрать группу ресурсов, где будет развернута целевая НСГ.  Вы можете нажать **Создать новый** для создания новой группы ресурсов для целевой NSG.  Убедитесь, что имя не совпадает с группой ресурсов исходного кода существующей NSG.

16. Проверка**местоположения** **BASICS** > устанавливается в целевом месте, где вы хотите, чтобы NSG был развернут.

17. **Убедитесь,** что имя соответствует имени, которое вы ввели в приведенный выше редактор параметров.

18. Проверьте поле под **TERMS И КОНДИИ.**

19. Нажмите кнопку **«Покупка»,** чтобы развернуть целевую группу безопасности сети.

## <a name="discard"></a>Игнорировать

Если вы хотите отказаться от целевой NSG, удалите группу ресурсов, содержащую целевую NSG.  Для этого выберите группу ресурсов из панели мониторинга на портале и выберите **Удалить** в верхней части страницы обзора.

## <a name="clean-up"></a>Очистка

Чтобы выполнить изменения и завершить перемещение NSG, удалите источник NSG или группу ресурсов. Для этого выберите группу сетевой безопасности или группу ресурсов из панели мониторинга на портале и выберите **Удалить** в верхней части каждой страницы.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике группа сетевой безопасности Azure перенесла группу сетевой безопасности Azure из одного региона в другой и очистила исходные ресурсы.  Чтобы узнать больше о перемещении ресурсов между регионами и аварийном восстановлении в Azure, обратитесь к:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
