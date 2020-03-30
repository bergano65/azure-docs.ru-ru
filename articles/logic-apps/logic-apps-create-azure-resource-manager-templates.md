---
title: Создание шаблонов приложений логики для развертывания
description: Узнайте, как создать шаблоны управления ресурсами Azure для автоматизации развертывания в приложениях логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972646"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Создание шаблонов управления ресурсами Azure для автоматизации развертывания для приложений логики Azure

Чтобы помочь автоматизировать создание и развертывание приложения логики, в этой статье описаны способы создания [шаблона управления ресурсами Azure](../azure-resource-manager/management/overview.md) для приложения логики. Обзор структуры и синтаксиса для шаблона, включающий определение рабочего процесса [Overview: Automate deployment for logic apps with Azure Resource Manager templates](logic-apps-azure-resource-manager-templates-overview.md)и другие ресурсы, необходимые для развертывания, см.

Azure Logic Apps предоставляет [заранее построенное логическое приложение Azure Resource Manager шаблон,](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) который можно повторно использовать не только для создания логических приложений, но и для определения ресурсов и параметров для развертывания. Вы можете использовать этот шаблон для собственных бизнес-сценариев или настроить его в соответствии со своими требованиями.

> [!IMPORTANT]
> Убедитесь, что соединения в шаблоне используют ту же группу ресурсов и местоположение Azure, что и приложение логики.

Для получения дополнительной информации о шаблонах управления ресурсами Azure см.

* [Структура шаблона и синтаксис менеджера ресурсов Azure](../azure-resource-manager/templates/template-syntax.md)
* [Шаблоны диспетчера ресурсов Azure](../azure-resource-manager/templates/template-syntax.md)
* [Разработка шаблонов Azure Resource Manager для обеспечения согласованности с облаком](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Создание шаблонов с помощью Visual Studio

Для того чтобы простой способ создать допустимые шаблоны шаблонов логических приложений, которые в основном готовы к развертыванию, используйте Visual Studio (бесплатное издание сообщества или больше) и инструменты Azure Logic Apps Tools для Visual Studio. Затем можно [создать логическое приложение в Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) или найти и загрузить [существующее приложение логики с портала Azure в Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)

Загрузив логическое приложение, вы получите шаблон, который включает определения для приложения логики и других ресурсов, таких как соединения. Шаблон также *параметры,* или определяет параметры для, значения, используемые для развертывания приложения логики и других ресурсов. Значения этих параметров можно предоставить в отдельном файле параметров. Таким образом, вы можете легко изменить эти значения в зависимости от ваших потребностей развертывания. Дополнительные сведения см. в следующих статьях:

* [Создание приложений логики в Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Управление приложениями логики в Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Создание шаблонов с помощью Azure PowerShell

Шаблоны управления ресурсами можно создавать с помощью Azure PowerShell с [модулем LogicAppTemplate.](https://github.com/jeffhollan/LogicAppTemplateCreator) Этот модуль с открытым исходным кодом сначала оценивает ваше логическое приложение и любые соединения, которые использует приложение логики. Модуль затем генерирует шаблонные ресурсы с необходимыми параметрами для развертывания.

Например, предположим, что у вас есть логическое приложение, которое получает сообщение из очереди Azure Service Bus и загружает данные в базу данных Azure S'L. Модуль сохраняет всю логику оркестровки и параметры соединения S'L и Service Bus, чтобы вы могли предоставить и изменить эти значения в зависимости от ваших потребностей развертывания.

Эти примеры показывают, как создавать и развертывать логические приложения с помощью шаблонов Azure Resource Manager, конвейеров Azure в Azure DevOps и Azure PowerShell:

* [Пример: Подключение к очередям в автобусе Azure из приложений логики Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Подключение к учетным записям хранения Azure из приложений логики Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Настройка действия приложения функции для приложений логики Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Подключение к учетной записи интеграции из приложений логики Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Установка модулей PowerShell

1. Если вы еще не сделали этого, [установите Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

1. Для простого способа установки модуля LogicAppTemplate из [галереи PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate)запустите эту команду:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Чтобы обновить последнюю версию, запустите эту команду:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Или, чтобы установить вручную, следуйте инструкциям в GitHub для [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Установка клиента-менеджера ресурсов Azure

Для того, чтобы модуль LogicAppTemplate работал с любым арендатором Azure и маркером доступа к подписке, установите [клиентский инструмент Azure Resource Manager,](https://github.com/projectkudu/ARMClient)который является простым инструментом командной строки, который вызывает API менеджера ресурсов Azure.

При запуске `Get-LogicAppTemplate` команды с помощью этого инструмента команда сначала получает токен доступа через инструмент ARMClient, подкладывает токен к сценарию PowerShell и создает шаблон в виде файла JSON. Для получения дополнительной информации об этом инструменте смотрите [статью о клиентском инструменте Azure Resource Manager.](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)

### <a name="generate-template-with-powershell"></a>Создание шаблона с помощью PowerShell

Для создания шаблона после установки модуля LogicAppTemplate и [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)запустите команду PowerShell:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Чтобы следовать рекомендации по трубопроводу в маркере из [клиентского инструмента Azure Resource Manager,](https://github.com/projectkudu/ARMClient)запустите эту команду, а не где `$SubscriptionId` находится идентификатор подписки Azure:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

После извлечения можно создать файл параметров из шаблона, запустив эту команду:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Для извлечения со ссылками Azure Key Vault (только статик) запустите эту команду:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Параметры | Обязательно | Описание |
|------------|----------|-------------|
| TemplateFile | Да | Путь файла к файлу шаблона |
| Хранилище ключей | нет | В описании описано, как обрабатывать возможные значения хранилища ключей. Значение по умолчанию — `None`. |
||||

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание шаблонов приложений логики](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
