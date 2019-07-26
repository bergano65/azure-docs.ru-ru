---
title: Развертывание шаблонов приложений логики — Azure Logic Apps
description: Узнайте, как развертывать шаблоны Azure Resource Manager, созданные для Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: dbc92502cdab6e76ba6b8730fca7e0aed3966a43
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494941"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Развертывание шаблонов Azure Resource Manager для Azure Logic Apps

После создания шаблона Azure Resource Manager для приложения логики можно развернуть шаблон следующими способами.

* [портал Azure](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Интерфейс командной строки Azure](#cli)
* [API REST диспетчера ресурсов Azure](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure Pipelines Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Развертывание с помощью портал Azure

Чтобы автоматически развернуть шаблон приложения логики в Azure, можно нажать следующую кнопку **развертывания в Azure** , которая выполняет вход в портал Azure и запрашивает сведения о приложении логики. Затем можно внести необходимые изменения в шаблон или параметры приложения логики.

[![Развертывание в Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Например, после входа в портал Azure вам будет предложено ввести эти сведения:

* имя подписки Azure;
* группу ресурсов, которую следует использовать;
* расположение приложения логики;
* Имя приложения логики.
* тестовый универсальный код ресурса (URI);
* принятие указанных условий.

Дополнительные сведения см. в следующих статьях:

* [Apache Автоматизация развертывания приложений логики с помощью шаблонов Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Развертывание ресурсов с помощью шаблонов Azure Resource Manager и портал Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Развертывание с помощью Visual Studio

Чтобы развернуть шаблон приложения логики из проекта группы ресурсов Azure, созданного с помощью Visual Studio, выполните следующие [действия, чтобы вручную развернуть приложение логики](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) в Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Развертывание с помощью Azure PowerShell

Чтобы выполнить развертывание в определенной группе ресурсов Azure, используйте следующую команду:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Дополнительные сведения см. в следующих статьях:

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Чтобы выполнить развертывание в определенной *группе ресурсов Azure*, используйте следующую команду:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Дополнительные сведения см. в следующих статьях:

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Развертывание с помощью Azure DevOps

Чтобы развернуть шаблоны приложений логики и управлять средами, группы обычно используют такие средства, как [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) в [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines предоставляет [задачу развертывания группы ресурсов Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) , которую можно добавить в любой конвейер сборки или выпуска. Для разрешения на развертывание и создание конвейера выпуска также необходим [субъект-служба](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Дополнительные сведения об [использовании субъектов-служб с Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Дополнительные сведения о непрерывной интеграции и непрерывном развертывании (CI/CD) для шаблонов Azure Resource Manager с Azure Pipelines см. в следующих разделах:

* [Интеграция шаблонов диспетчер ресурсов с Azure Pipelines](../azure-resource-manager/vs-resource-groups-project-devops-pipelines.md)
* [Учебник. Непрерывная интеграция для шаблонов Azure Resource Manager с Azure Pipelines](../azure-resource-manager/resource-manager-tutorial-use-azure-pipelines.md)

Ниже приведены общие высокоуровневые действия по использованию Azure Pipelines.

1. В Azure Pipelines создайте пустой конвейер.

1. Выберите ресурсы, необходимые для конвейера, такие как шаблон приложения логики и файлы параметров шаблона, которые создаются вручную или в рамках процесса сборки.

1. Для задания агента найдите и добавьте задачу **развертывания группы ресурсов Azure** .

   ![Добавить задачу "Развертывание группы ресурсов Azure"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Настройте с помощью [субъекта-службы](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Добавьте ссылки на шаблон приложения логики и файлы параметров шаблона.

1. При необходимости выполните соответствующие действия в процессе выпуска для других сред, автоматических тестов или механизмов утверждения.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Авторизация подключений OAuth

После развертывания приложение логики работает в сквозном виде с допустимыми параметрами. Однако по-прежнему необходимо авторизовать подключения OAuth, чтобы создать допустимые маркеры доступа для [проверки подлинности учетных данных](../active-directory/develop/authentication-scenarios.md). Ниже приведены способы авторизации подключений OAuth.

* Для автоматизированных развертываний можно использовать скрипт, который обеспечивает согласие для каждого подключения OAuth. Ниже приведен пример скрипта в GitHub в проекте [логикаппконнектионаус](https://github.com/logicappsio/LogicAppConnectionAuth) .

* Чтобы вручную авторизовать подключения OAuth, откройте приложение логики в конструкторе приложений логики либо в портал Azure, либо в Visual Studio. В конструкторе Авторизуйте все необходимые соединения.

Если вместо авторизации подключений используется [субъект-служба](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD), Узнайте, как [указать параметры субъекта — службы в шаблоне приложения логики](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Мониторинг приложений логики](../logic-apps/logic-apps-monitor-your-logic-apps.md)
