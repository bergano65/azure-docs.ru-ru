---
title: Подготовка веб-приложения с кэшем Redis для Azure
description: Используйте шаблон Azure Resource Manager для развертывания веб-приложения с кэшем Redis для Azure.
services: app-service
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 6e99c71f-ef8e-4570-a307-e4c059e60c35
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: 23b8e4e7e88f5b993f9b0f9981bbae6b884e2818
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "65911307"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Создание веб-приложения и экземпляра кэша Redis для Azure с помощью шаблона

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

В этой статье описано, как создать шаблон Azure Resource Manager для развертывания веб-приложения Azure с кэшем Redis для Azure. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Сведения о синтаксисе JSON и свойствах для типов ресурсов кэша см. в справочнике по [типам ресурсов Microsoft.Cache](/azure/templates/microsoft.cache/allversions).

См. [полный шаблон веб-приложения с кэшем Redis для Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Что именно развертывается
В этом шаблоне будут развернуты перечисленные ниже компоненты.

* Веб-приложение Azure
* Кэш Azure для Redis

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Указываемые параметры
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Переменные для имен
В этом шаблоне для формирования имен ресурсов используются переменные. Значение на основе идентификатора группы ресурсов создается с помощью функции [uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) .

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>Развертываемые ресурсы
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Кэш Azure для Redis
Создает кэш Redis для Azure, используемый с веб-приложением. Имя кэша указывается в переменной **cacheName** .

Этот шаблон создает кэш в том же месте, где находится группа ресурсов.

    {
      "name": "[variables('cacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [ ],
      "tags": {
        "displayName": "cache"
      },
      "properties": {
        "sku": {
          "name": "[parameters('cacheSKUName')]",
          "family": "[parameters('cacheSKUFamily')]",
          "capacity": "[parameters('cacheSKUCapacity')]"
        }
      }
    }


### <a name="web-app"></a>Веб-приложение
Создает веб-приложение с именем, указанным в переменной **webSiteName** .

Обратите внимание, что веб-приложение настроено со свойствами параметров приложения, которые позволяют ему работать с кэшем Redis для Azure. Эти приложения, параметры создаются динамически на основании значений, предоставленных во время развертывания.

    {
      "apiVersion": "2015-08-01",
      "name": "[variables('webSiteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
        "displayName": "Website"
      },
      "properties": {
        "name": "[variables('webSiteName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "type": "config",
          "name": "appsettings",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
            "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
          ],
          "properties": {
            "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Инфраструктура CLI Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
