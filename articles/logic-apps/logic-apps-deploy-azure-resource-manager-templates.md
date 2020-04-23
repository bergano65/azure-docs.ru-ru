---
title: Развертывание шаблонов приложений логики
description: Узнайте, как развернуть шаблоны управления ресурсами Azure, созданные для приложений логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 432e22879ce0eba89f04a1084e2d4a93a487dd45
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086442"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Развертывание шаблонов диспетчера ресурсов Azure для логических приложений Azure

После создания шаблона управления ресурсами Azure для приложения логики можно развернуть шаблон таким образом:

* [Портал Azure](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [API REST диспетчера ресурсов Azure](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Развертывание через портал Azure

Чтобы автоматически развернуть шаблон приложения логики в Azure, можно выбрать следующую кнопку **«Развертывание в Azure»,** которая подпишет вас на портал Azure и подскажет вам информацию о приложении логики. Затем можно внести все необходимые изменения в шаблон или параметры приложения логики.

[![Развертывание в Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Например, после входиный ввесенный на портал Azure вам предлагается записаться на следующий портал:

* имя подписки Azure;
* группу ресурсов, которую следует использовать;
* расположение приложения логики;
* Имя приложения логики.
* тестовый универсальный код ресурса (URI);
* принятие указанных условий.

Дополнительные сведения см. в следующих статьях:

* [Обзор: Автоматизация развертывания для логических приложений с шаблонами управления ресурсами Azure](logic-apps-azure-resource-manager-templates-overview.md)
* [Развертывание ресурсов с помощью шаблонов управления ресурсами Azure и портала Azure](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Развертывание с помощью Visual Studio

Чтобы развернуть шаблон приложения логики из проекта Группы ресурсов Azure, созданного с помощью Visual Studio, выполните следующие [действия, чтобы вручную развернуть приложение логики](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) в Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Развертывание с помощью Azure PowerShell

Для развертывания в определенной *группе ресурсов Azure*используйте следующую команду:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Дополнительные сведения см. в следующих статьях:

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Для развертывания в определенной *группе ресурсов Azure*используйте следующую команду:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Дополнительные сведения см. в следующих статьях:

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Развертывание с Azure DevOps

Для развертывания шаблонов логических приложений и управления средами команды обычно используют такой инструмент, как [Azure Pipelines,](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) в [Azure DevOps.](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services) Ресурсные конвейеры Azure обеспечивают [задачу развертывания группы ресурсов Azure,](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) которую можно добавить к любому конвейеру сборки или выпуска. Для развертывания и генерации конвейера выпуска также необходим [принцип службы](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Подробнее об [использовании основ служб с помощью проводов Azure Pipelines.](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)

Для получения дополнительной информации о непрерывной интеграции и непрерывном развертывании (CI/CD) для шаблонов Управления ресурсами Azure с azure Pipelines см.

* [Интеграция шаблонов диспетчера ресурсов с конвейерами Azure](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Руководство по Непрерывная интеграция шаблонов Azure Resource Manager с Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Пример: Подключение к очередям в шины Azure из приложений логики Azure и развертывание с помощью проводов Azure в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Подключение к учетным записям хранения Azure из приложений логики Azure и развертывание с помощью конвейеров Azure в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Настройка действия приложения функции для приложений Логики Azure и развертывание с помощью проводов Azure в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Подключение к учетной записи интеграции из приложений логики Azure и развертывание с помощью проводов Azure в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Пример: Оркестровые трубопроводы Azure с помощью приложений Логики Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Вот общие высококачественные шаги для использования трубопроводов Azure:

1. В трубопроводах Azure создайте пустой конвейер.

1. Выберите ресурсы, необходимые для конвейера, такие как шаблон логики приложения и файлы параметров шаблона, которые вы генерируете вручную или в процессе сборки.

1. Для выполнения задания агента найдите и добавьте задачу **развертывания группы ресурсов Azure.**

   ![Добавление задачи «Развертывание группы ресурсов Azure»](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Настройка с [помощью основного сервиса](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Добавьте ссылки на шаблон логики приложения и файлы параметров шаблона.

1. При необходимости выполните соответствующие действия в процессе выпуска для других сред, автоматических тестов или механизмов утверждения.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Авторизация подключений OAuth

После развертывания приложение логики работает сквозной с допустимыми параметрами. Тем не менее, вы все равно должны разрешить любые соединения OAuth для создания действительных токенов доступа для [проверки подлинности учетных данных.](../active-directory/develop/authentication-scenarios.md) Вот способы, которые вы можете разрешить соединения OAuth:

* Для автоматизированных развертываний можно использовать скрипт, предоставляющий согласие на каждое соединение OAuth. Вот пример сценария в GitHub в проекте [LogicAppConnectionAuth.](https://github.com/logicappsio/LogicAppConnectionAuth)

* Чтобы вручную разрешить соединения OAuth, откройте приложение логики в Logic App Designer, либо на портале Azure, либо в Visual Studio. В конструкторе авторизуйте все необходимые соединения.

Если вместо этого вы используете [принцип службы](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD), чтобы авторизовать соединения, узнайте, как [указать основные параметры службы в шаблоне приложения логики.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Мониторинг приложений логики](../logic-apps/monitor-logic-apps.md)
