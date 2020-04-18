---
title: Создание рабочей области Log Analytics с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как создать рабочую область Log Analytics, чтобы включить решения по управлению и сбор данных из облачной и локальной сред с помощью Azure CLI.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 0e91bc9c994a48b335c3ccb7373a9f4f5dc6d1e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605086"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Создание рабочей области Log Analytics с помощью Azure CLI 2.0

Azure CLI 2.0 используется для создания ресурсов Azure и управления ими из командной строки или с помощью сценариев. В этом кратком руководстве показано, как развернуть рабочую область Log Analytics в Azure Monitor с помощью Azure CLI 2.0. Рабочая область Log Analytics — это уникальная среда для данных журналов Azure Monitor. Каждая рабочая область имеет свои репозиторий данных и конфигурации, при этом источники данных и решения настроены для хранения данных в определенной рабочей области. Вам понадобится рабочая область Log Analytics, если вы собираете данные из следующих источников:

* ресурсы Azure в подписке;  
* локальные компьютеры, которые отслеживает System Center Operations Manager;  
* Коллекции устройств от менеджера конфигурации  
* Данные диагностики и журнала из службы хранилища Azure  

Сведения о других источниках, таких как виртуальные машины Azure и виртуальные машины Windows или Linux в вашей среде, см. в статьях ниже.

* [Сбор данных о виртуальных машинах Azure](../learn/quick-collect-azurevm.md)
* [Настройка агента Log Analytics для компьютеров Linux в гибридной среде](../learn/quick-collect-linux-computer.md)
* [Настройка агента Log Analytics для компьютеров Windows в гибридной среде](quick-collect-windows-computer.md)

Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Создание рабочей области
Создайте рабочую область с помощью команды [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). Следующий пример создает рабочее пространство в *восточном* месте с помощью шаблона Resource Manager из локальной машины. Шаблон в формате JSON настроен так, чтобы осталось только указать имя рабочей области и задать значения по умолчанию для других параметров, которые скорее всего будут использоваться в качестве стандартной конфигурации в вашей среде. Кроме того, шаблон можно хранить в учетной записи хранения Azure для общего доступа в организации. Дополнительную информацию о работе с шаблонами см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

Для получения информации об поддерживаемых регионах см. [регионы, в которые можно ознакомиться, доступна система «Аналитика журналов»](https://azure.microsoft.com/regions/services/) и поиск Azure Monitor из поля **поиска продукта.**

Для следующих параметров задаются значения по умолчанию.

* location — по умолчанию используется значение East US.
* sku — по умолчанию используется новый тарифный план с платой за гигабайт, выпущенный в апреле 2018 года.

>[!WARNING]
>При создании или настройке рабочей области Log Analytics в подписке, использующей модель ценообразования от апреля 2018 года, доступна только ценовая категория **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Создание и развертывание шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Отредактируйте шаблон с учетом ваших требований. Просмотрите справочник по [шаблону Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) с описанием поддерживаемых свойств и значений.
3. Сохраните этот файл как **deploylaworkspacetemplate.json** в локальной папке.   
4. Теперь вы можете развернуть этот шаблон. Используйте следующие команды из папки, содержащей шаблон. Когда вам будет предложено назвать рабочее пространство, укажите имя, которое является уникальным во всем мире во всех подписках Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Развертывание может занять несколько минут. По завершении выполнения появится сообщение с результатами наподобие приведенного ниже.

![Пример результатов по завершении развертывания](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда рабочая область доступна, вы можете настроить сбор данных телеметрии для мониторинга, выполнять поиск по журналам для анализа этих данных, а также добавить решение по управлению для предоставления дополнительных данных и аналитических сведений.  

* Сведения о том, как включить сбор данных из ресурсов Azure с помощью системы диагностики Azure или службы хранилища Azure, см. в статье [Сбор журналов и метрик для служб Azure для использования в Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Добавьте [менеджера по операциям System Center в качестве источника данных](../platform/om-agents.md) для сбора данных от агентов, сообщающих об управлении группой управления операциями, и их хранения в рабочей области Log Analytics.  
* Подключите [Configuration Manager](../platform/collect-sccm.md) для импорта данных с компьютеров, которые являются элементами коллекций в иерархии.  
* Просмотрите список доступных [решений для мониторинга](../insights/solutions.md) и узнайте, как добавить решение в рабочую область или удалить его из нее.
