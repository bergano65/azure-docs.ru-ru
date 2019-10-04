---
title: Создание рабочей области Log Analytics с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как создать рабочую область Log Analytics, чтобы включить решения по управлению и сбор данных из облачной и локальной сред с помощью Azure CLI.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: 7ee8302a026cf7584996bca481e79190586b77b3
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838850"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Создание рабочей области Log Analytics с помощью Azure CLI 2.0

Azure CLI 2.0 используется для создания ресурсов Azure и управления ими из командной строки или с помощью сценариев. В этом кратком руководстве показано, как развернуть рабочую область Log Analytics в Azure Monitor с помощью Azure CLI 2.0. Рабочая область Log Analytics — это уникальная среда для данных журналов Azure Monitor. Каждая рабочая область имеет свои репозиторий данных и конфигурации, при этом источники данных и решения настроены для хранения данных в определенной рабочей области. Вам понадобится рабочая область Log Analytics, если вы собираете данные из следующих источников:

* ресурсы Azure в подписке;  
* локальные компьютеры, которые отслеживает System Center Operations Manager;  
* коллекции устройств из System Center Configuration Manager;  
* Данные диагностики и журнала из службы хранилища Azure  
 
Сведения о других источниках, таких как виртуальные машины Azure и виртуальные машины Windows или Linux в вашей среде, см. в статьях ниже.

* [Сбор данных о виртуальных машинах Azure](../learn/quick-collect-azurevm.md)
* [Настройка агента Log Analytics для компьютеров Linux в гибридной среде](../learn/quick-collect-linux-computer.md)
* [Настройка агента Log Analytics для компьютеров Windows в гибридной среде](quick-collect-windows-computer.md)

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Создать рабочую область
Создайте рабочую область с помощью команды [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). В следующем примере создается рабочая область в расположении *eastus* с помощью шаблона диспетчер ресурсов на локальном компьютере. Шаблон в формате JSON настроен так, чтобы осталось только указать имя рабочей области и задать значения по умолчанию для других параметров, которые скорее всего будут использоваться в качестве стандартной конфигурации в вашей среде. Кроме того, шаблон можно хранить в учетной записи хранения Azure для общего доступа в организации. Дополнительную информацию о работе с шаблонами см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md).

Сведения о поддерживаемых регионах см. [в разделе регионы log Analytics доступны в](https://azure.microsoft.com/regions/services/) и выполните поиск Azure Monitor в поле **поиска по полю продукта** . 

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

2. Отредактируйте шаблон с учетом ваших требований. Просмотрите справочник по [шаблону Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) с описанием поддерживаемых свойств и значений. 
3. Сохраните этот файл как **deploylaworkspacetemplate.json** в локальной папке.   
4. Теперь вы можете развернуть этот шаблон. Используйте следующие команды из папки, содержащей шаблон. При запросе имени рабочей области Укажите имя, которое глобально уникально для всех подписок Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Развертывание может занять несколько минут. По завершении выполнения появится сообщение с результатами наподобие приведенного ниже.

![Пример результатов по завершении развертывания](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Следующие шаги
Теперь, когда рабочая область доступна, вы можете настроить сбор данных телеметрии для мониторинга, выполнять поиск по журналам для анализа этих данных, а также добавить решение по управлению для предоставления дополнительных данных и аналитических сведений.  

* Сведения о том, как включить сбор данных из ресурсов Azure с помощью системы диагностики Azure или службы хранилища Azure, см. в статье [Сбор журналов и метрик для служб Azure для использования в Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Добавьте [System Center Operations Manager в качестве источника данных](../platform/om-agents.md), чтобы собирать данные с агентов, которые предоставляют отчеты группе управления Operations Manager, и хранить эти данные в рабочей области Log Analytics.  
* Подключите [Configuration Manager](../platform/collect-sccm.md) для импорта данных с компьютеров, которые являются элементами коллекций в иерархии.  
* Просмотрите список доступных [решений для мониторинга](../insights/solutions.md) и узнайте, как добавить решение в рабочую область или удалить его из нее.
