---
title: Создание рабочей области Log Analytics с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как создать рабочую область Log Analytics для включения решений по управлению и сбора данных из облачной и локальной сред с помощью Azure PowerShell.
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
ms.openlocfilehash: 6f27aeb65cb9077011e662c165ca26202546db26
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905738"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Создание рабочей области Log Analytics с помощью Azure PowerShell

Модуль PowerShell используется для создания ресурсов Azure и управления ими с помощью командной строки PowerShell или сценариев. В этом кратком руководстве показано, как развернуть рабочую область Log Analytics в Azure Monitor с помощью модуля Azure PowerShell. Рабочая область Log Analytics — это уникальная среда для данных журналов Azure Monitor. Каждая рабочая область имеет свои репозиторий данных и конфигурации, при этом источники данных и решения настроены для хранения данных в определенной рабочей области. Вам понадобится рабочая область Log Analytics, если вы собираете данные из следующих источников:

* ресурсы Azure в подписке;  
* локальные компьютеры, которые отслеживает System Center Operations Manager;  
* коллекции устройств из System Center Configuration Manager;  
* Данные диагностики и журнала из службы хранилища Azure  
 
Сведения о других источниках, таких как виртуальные машины Azure и виртуальные машины Windows или Linux в вашей среде, см. в статьях ниже.

* [Сбор данных на виртуальных машинах Azure](../learn/quick-collect-azurevm.md)
* [Собирать данные c компьютера Linux гибридных](../learn/quick-collect-linux-computer.md)
* [Собирать данные c компьютера Windows гибридного](quick-collect-windows-computer.md)

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать PowerShell локально, в этом руководстве требуется модуль Azure PowerShell Az. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="create-a-workspace"></a>Создание рабочей области
Создание рабочей области с [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). В следующем примере создается рабочая область с именем *TestWorkspace* в группе ресурсов *Lab* в регионе *eastus* с помощью шаблона Resource Manager на локальном компьютере. Шаблон в формате JSON настроен так, чтобы осталось только указать имя рабочей области и задать значения по умолчанию для других параметров, которые скорее всего будут использоваться в качестве стандартной конфигурации в вашей среде. 

Сведения о поддерживаемых регионах см. в разделе [регионах, доступны в Log Analytics](https://azure.microsoft.com/regions/services/) и выполните поиск Azure Monitor от **поиск продукта** поля. 

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
4. Теперь вы можете развернуть этот шаблон. Используйте следующие команды из папки, содержащей шаблон:

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Развертывание может занять несколько минут. По завершении выполнения появится сообщение с результатами наподобие приведенного ниже.

![Пример результатов по завершении развертывания](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда рабочая область доступна, вы можете настроить сбор данных телеметрии для мониторинга, выполнять поиск по журналам для анализа этих данных, а также добавить решение по управлению для предоставления дополнительных данных и аналитических сведений.  

* Сведения о том, как включить сбор данных из ресурсов Azure с помощью системы диагностики Azure или хранилища Azure, см. в статье [Сбор журналов и метрик для служб Azure для использования в Azure Monitor](../platform/collect-azure-metrics-logs.md).  
* Добавьте [System Center Operations Manager в качестве источника данных](../platform/om-agents.md), чтобы собирать данные с агентов, которые предоставляют отчеты группе управления Operations Manager, и хранить эти данные в рабочей области Log Analytics.  
* Подключите [Configuration Manager](../platform/collect-sccm.md) для импорта данных с компьютеров, которые являются элементами коллекций в иерархии.  
* Просмотрите список доступных [решений для мониторинга](../insights/solutions.md) и узнайте, как добавить решение в рабочую область или удалить его из нее.
