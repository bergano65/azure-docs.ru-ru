---
title: Развертывание приложений логики с помощью шаблонов Azure Resource Manager — Azure Logic Apps
description: Развертывание приложений логики с помощью шаблонов Azure Resource Manager
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 7543859a916de97d471db2894887e640db51dfc2
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893430"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Развертывание приложений логики с помощью шаблонов Azure Resource Manager

После создания шаблона Azure Resource Manager для развертывания приложения логики, можно развернуть шаблон в следующих случаях:

* [Портал Azure](#portal)
* [Azure PowerShell](#powershell)
* [Инфраструктура CLI Azure](#cli)
* [REST API Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Развертывание с помощью портала Azure

Чтобы автоматически развернуть шаблон приложения логики Azure, можно выбрать следующие **развертывание в Azure** кнопку, которая выполняется вход в портал Azure и запрашивает сведения о приложении логики. Необходимые изменения можно внести в шаблон приложения логики или параметры.

[![Dазвернуть в Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Например будет предложено ввести эту информацию, после входа на портал Azure:

* имя подписки Azure;
* группу ресурсов, которую следует использовать;
* расположение приложения логики;
* Имя приложения логики.
* тестовый универсальный код ресурса (URI);
* принятие указанных условий.

Дополнительные сведения см. в разделе [развертывание ресурсов с использованием шаблонов Azure Resource Manager и портала Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Авторизация подключений OAuth

После развертывания приложение логики будет полноценно работать с допустимыми параметрами. Но чтобы создать допустимый маркер доступа, необходимо авторизовать подключения OAuth. Для автоматического развертывания можно использовать скрипт, который дает согласие на каждое подключение OAuth, например это [пример сценария в проекте GitHub LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth). Также можно авторизовать подключения OAuth на портале Azure или в Visual Studio, открыв приложение логики в конструкторе приложений логики.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Развертывание с помощью Azure PowerShell

Для развертывания с конкретными *группы ресурсов Azure*, используйте следующую команду:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Чтобы развернуть в определенной подписке Azure, используйте следующую команду:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Для развертывания с конкретными *группы ресурсов Azure*, используйте следующую команду:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Чтобы развернуть в определенной подписке Azure, используйте следующую команду:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Дополнительные сведения см. в следующих статьях: 

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Развертывание с помощью Azure DevOps

Чтобы развернуть шаблоны приложений логики и управлять средами, команды часто используют средство такие как [конвейеры Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) в [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Конвейеры Azure предоставляет [задачи развертывания группы ресурсов Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) можно добавить в любую сборку или конвейер выпуска.
Для авторизации для развертывания и создания конвейера выпуска, необходимо также Azure Active Directory (AD) [субъекта-службы](../active-directory/develop/app-objects-and-service-principals.md). Дополнительные сведения о [с помощью субъектов-служб с конвейерами Azure](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Ниже приведены шаги высокого уровня для использования конвейеров в Azure.

1. В конвейерах Azure создайте пустой конвейер.

1. Выберите ресурсы, необходимые для конвейера, например, шаблон приложения логики и файлов параметров шаблонов, создаваемым вручную или как часть процесса построения.

1. Для задания агента, найдите и добавьте **развертывания группы ресурсов Azure** задачи.

   ![Добавьте задачу «Развертывание группы ресурсов Azure»](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Настройка с помощью [субъекта-службы](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Добавьте ссылки на ваш шаблон приложения логики и файлы параметров шаблона.

1. При необходимости выполните соответствующие действия в процессе выпуска для других сред, автоматических тестов или механизмов утверждения.

## <a name="get-support"></a>Получение поддержки

Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Мониторинг приложений логики](../logic-apps/logic-apps-monitor-your-logic-apps.md)
