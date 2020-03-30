---
title: Обзор - Автоматизация развертывания для приложений логики Azure
description: Узнайте о шаблонах управления ресурсами Azure для автоматизации развертывания для приложений логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 920d8bfbcef33464d528306113abe6223d752889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477754"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Обзор: Автоматизация развертывания для приложений логики Azure с помощью шаблонов управления ресурсами Azure

Когда вы будете готовы к автоматизации создания и развертывания приложения логики, можно расширить определение рабочего процесса в базовом приложении логики в [шаблон Azure Resource Manager.](../azure-resource-manager/management/overview.md) Этот шаблон определяет инфраструктуру, ресурсы, параметры и другую информацию для подготовки и развертывания приложения логики. Определяя параметры значений, которые различаются при развертывании, также известные как *параметры,* можно повторно и последовательно развертывать логические приложения на основе различных потребностей развертывания.

Например, при развертывании в средах для разработки, тестирования и производства, скорее всего, для каждой среды используются разные строки соединения. Можно объявить параметры шаблона, которые принимают различные строки соединения, а затем хранить эти строки в отдельном [файле параметров.](../azure-resource-manager/templates/parameter-files.md) Таким образом, вы можете изменить эти значения без необходимости обновления и передислокации шаблона. Для сценариев, где у вас есть значения параметров, которые являются конфиденциальными или должны быть защищены, такие как пароли и секреты, вы можете хранить эти значения в [Убежище ключа Azure](../azure-resource-manager/templates/key-vault-parameter.md) и иметь файл параметров получить эти значения. Однако в этих сценариях необходимо перераспределить сярты для получения текущих значений.

В этом обзоре описаны атрибуты в шаблоне «Менеджер ресурсов», который включает определение рабочего процесса логического приложения. Как шаблон, так и определение рабочего процесса используют синтаксис JSON, но некоторые различия существуют, поскольку определение рабочего процесса также соответствует [схеме определения языка рабочего процесса.](../logic-apps/logic-apps-workflow-definition-language.md) Например, выражения шаблонов и выражения определения рабочего процесса различаются по [параметрам](#parameter-references) и значениям, которые они могут принять.

> [!TIP]
> Для того чтобы самый простой способ получить действительный параметризированный шаблон приложения логики, который в основном готов к развертыванию, используйте Visual Studio (бесплатное издание сообщества или больше) и инструменты Azure Logic Apps Tools для Visual Studio. Затем можно [создать логическое приложение в Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) или найти и загрузить [существующее логическое приложение из Azure в Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)
>
> Или вы можете создавать шаблоны логических приложений с помощью [Azure PowerShell с модулем LogicAppTemplate.](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)

В приложении логики примера в этой теме используется [триггер Office 365 Outlook,](/connectors/office365/) который срабатывает при попаске нового письма, и [действие Azure Blob Storage,](/connectors/azureblob/) которое создает каплю для тела электронной почты и загружает эту каплю в контейнер для хранения Azure. Примеры также показывают, как параметризировать значения, которые различаются при развертывании.

Для получения дополнительной информации о шаблонах менеджера ресурсов см.

* [Структура шаблона и синтаксис менеджера ресурсов Azure](../azure-resource-manager/templates/template-syntax.md)
* [Рекомендации по работе с шаблонами Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md)
* [Разработка шаблонов Azure Resource Manager для обеспечения согласованности с облаком](../azure-resource-manager/templates/templates-cloud-consistency.md)

Для примеров шаблонов приложений логики см.

* [Полный шаблон,](#full-example-template) используемый для примеров этой темы
* [Пример шаблона логики быстрого запуска](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) в GitHub

Для информации о ресурсах шаблонов, специфичной для логических приложений, учетных записей интеграции и артефактов учетных записей интеграции, [см.](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)

<a name="template-structure"></a>

## <a name="template-structure"></a>Структура шаблона

На верхнем уровне шаблон Resource Manager следует этой структуре, которая полностью описана в [структуре шаблона управления ресурсами Azure и теме синтаксиса:](../azure-resource-manager/templates/template-syntax.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Для шаблона приложения логики вы в первую очередь работаете с этими объектами шаблонов:

| Атрибут | Описание |
|-----------|-------------|
| `parameters` | Объявляет [параметры шаблона](../azure-resource-manager/templates/template-syntax.md#parameters) для принятия значений, которые можно использовать при создании и настройке ресурсов для развертывания в Azure. Например, эти параметры принимают значения для имени и местоположения приложения логики, соединений и других ресурсов, необходимых для развертывания. Эти значения параметров можно хранить в [файле параметров,](#template-parameter-files)который описан позже в этой теме. Для получения общих подробностей [см.](../azure-resource-manager/templates/template-syntax.md#parameters) |
| `resources` | Определяет [ресурсы](../azure-resource-manager/templates/template-syntax.md#resources) для создания или обновления и развертывания в группе ресурсов Azure, таких как приложение логики, соединения, учетные записи хранения Azure и так далее. Для получения общих подробностей [см.](../azure-resource-manager/templates/template-syntax.md#resources) |
||||

Ваш шаблон приложения логики использует этот формат имени файла:

**<*логика-приложение-имя*>.json**

> [!IMPORTANT]
> Синтаксис шаблона является чувствительным к делу, поэтому убедитесь, что вы используете согласованный корпус. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Параметры шаблона

Шаблон приложения логики `parameters` имеет несколько объектов, которые существуют на разных уровнях и выполняют различные функции. Например, на верхнем уровне можно объявить [параметры шаблона](../azure-resource-manager/templates/template-syntax.md#parameters) для принятия и использования значений при развертывании при создании и развертывании ресурсов в Azure, например:

* Приложение логики
* Соединения, которые использует ваша логика для доступа к другим службам и системам через [управляемые разъемы](../connectors/apis-list.md)
* Другие ресурсы, необходимые приложению логики для развертывания

  Например, если приложение логики использует [учетную запись интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) для сценариев «бизнес-бизнес» (B2B), объект верхнего уровня `parameters` шаблона объявляет параметр, принимающий идентификатор ресурса для этой учетной записи интеграции.

Вот общая структура и синтаксис для определения параметров, который полностью описан [Параметры - Структура шаблона Resource Manager и синтаксис:](../azure-resource-manager/templates/template-syntax.md#parameters)

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

В этом примере отображаются только параметры шаблона для значений, используемых для создания и развертывания этих ресурсов в Azure:

* Имя и местоположение приложения логики
* Идентификатор для использования для учетной записи интеграции, связанной с приложением логики

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

За исключением параметров, обрабатываемых чувствительных или должны быть защищены, таких как имена `defaultValue` пользователей, пароли и секреты, все эти параметры включают атрибуты, хотя в некоторых случаях значения по умолчанию являются пустыми значениями. Значения развертывания для использования для этих параметров шаблона предоставляются [файлом параметров](#template-parameter-files) выборки, описанным позднее в этой теме.

Для получения дополнительной информации о защите параметров шаблона см.

* [Рекомендации по безопасности для параметров шаблона](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Улучшение безопасности параметров шаблона](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Передайте защищенные значения параметров с помощью Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Другие объекты шаблона часто ссылаются на параметры шаблона, чтобы они могли использовать значения, которые проходят через параметры шаблона, например:

* [Объект ресурсов шаблона,](#template-resources)описанный позднее в этой теме, определяет каждый ресурс в Azure, который требуется создать и развернуть, например [определение ресурсов приложения логики.](#logic-app-resource-definition) Эти ресурсы часто используют значения параметров шаблона, такие как имя и информация о местоположении и подключении приложения логики.

* На более глубоком уровне в определении ресурсов приложения логики [объект параметров определения рабочего процесса](#workflow-definition-parameters) декларирует параметры значений для использования в времени выполнения приложения логики. Например, можно объявить параметры определения рабочего процесса для имени пользователя и пароля, который использует триггер HTTP для проверки подлинности. Чтобы указать значения параметров определения рабочего `parameters` процесса, используйте объект, который находится *вне* определения рабочего процесса, но все еще *находится в* определении ресурсов приложения логики. В этом `parameters` внешнем объекте можно ссылаться на ранее объявленные параметры шаблона, которые могут принимать значения при развертывании из файла параметров.

При ссылках на параметры выражения шаблонов и функции используют различные синтаксисы и ведут себя по-разному от выражений и функций определения рабочего процесса. Для получения дополнительной информации об этих [различиях, см. Ссылки на параметры](#parameter-references) позже в этой теме.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Лучшие практики - параметры шаблона

Вот некоторые рекомендации по определению параметров:

* Объявить параметры только для значений, которые меняются, в зависимости от ваших потребностей развертывания. Не объявляйте параметры значений, которые остаются неизменными в различных требованиях развертывания.

* Включите `defaultValue` атрибут, который может указывать пустые значения, для всех параметров, за исключением чувствительных или чувствительных значений. Всегда используйте защищенные параметры для имен пользователей, паролей и секретов. Чтобы скрыть или защитить чувствительные значения параметров, следуйте инструкциям по следующим темам:

  * [Рекомендации по безопасности для параметров шаблона](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Улучшение безопасности параметров шаблона](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Передайте защищенные значения параметров с помощью Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Чтобы дифференцировать имена параметров шаблона от названий параметров определения рабочего процесса, можно использовать описательные имена параметров шаблона, например:`TemplateFabrikamPassword`

Для получения дополнительной рекомендации шаблона [см.](../azure-resource-manager/templates/template-best-practices.md#parameters)

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Файл параметров шаблона

Чтобы обеспечить значения параметров шаблона, храните эти значения в [файле параметров.](../azure-resource-manager/templates/parameter-files.md) Таким образом, можно использовать различные файлы параметров в зависимости от потребностей развертывания. Вот формат имени файла для использования:

* Имя файла шаблона логики приложения: ** < *имя логики-приложения*>.json**
* Имя файла параметров: ** < *logic-app-name*>.parameters.json**

Вот структура внутри файла параметров, которая включает в себя ссылку на хранилище ключей для [прохождения защищенного значения параметра с Azure Key Vault:](../azure-resource-manager/templates/key-vault-parameter.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

В этом примере файл параметров определяет значения параметров шаблона, заявленные ранее в этой теме:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Ресурсы шаблона

Шаблон имеет `resources` объект, который представляет собой массив, содержащий определения для каждого ресурса для создания и развертывания в Azure, например определение ресурсов приложения [логики,](#connection-resource-definitions)любые определения ресурсов соединения и любые другие ресурсы, необходимые для развертывания приложению логики. [logic app's resource definition](#logic-app-resource-definition)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Шаблоны могут включать определения ресурсов для нескольких логических приложений, поэтому убедитесь, что все ресурсы приложения логики указывают одну и ту же группу ресурсов Azure. При развертывании шаблона в группу ресурсов Azure с помощью Visual Studio вам подсказывается, какое логическое приложение требуется открыть. Кроме того, проект группы ресурсов Azure может содержать несколько шаблонов, поэтому убедитесь, что вы выбираете правильный файл параметров при запросе.

Для получения общей информации о ресурсах шаблонов и их атрибутах см.

* [Ресурсы - структура шаблонов и синтаксис менеджера ресурсов](../azure-resource-manager/templates/template-syntax.md#resources)
* [Рекомендации для шаблонных ресурсов](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Определение ресурсов логического приложения

Определение ресурсов приложения логики начинается с `properties` объекта, который включает в себя эту информацию:

* Состояние приложения логики при развертывании
* Идентификатор для любой учетной записи интеграции, используемой вашим приложением логики
* Определение рабочего процесса приложения логики
* Объект, `parameters` который устанавливает значения для использования во время выполнения
* Прочая информация о вашем приложении логики, например имя, тип, местоположение и т.д.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Вот атрибуты, которые специфичны для определения ресурсов приложения логики:

| Атрибут | Обязательно | Тип | Описание |
|-----------|----------|------|-------------|
| `state` | Да | Строка | Состояние приложения логики при `Enabled` развертывании, где `Disabled` означает, что приложение логики находится в реальном времени и означает, что приложение логики неработает. Например, если вы не готовы к тому, что приложение логики будет работать `Disabled` в реальном времени, но хотите развернуть черновой вариант, можно использовать эту опцию. |
| `integrationAccount` | нет | Объект | Если приложение логики использует учетную запись интеграции, в которой хранятся артефакты для `id` бизнес-бизнес-сценариев (B2B), этот объект включает в себя атрибут, который определяет идентификатор для учетной записи интеграции. |
| `definition` | Да | Объект | Базовое определение рабочего процесса вашего приложения логики, которое является тем же объектом, который отображается в представлении кода и полностью описан в [справке Schema для](../logic-apps/logic-apps-workflow-definition-language.md) темы языка определения рабочего процесса. В этом определении `parameters` рабочего процесса объект объявляет параметры для значений, которые можно использовать во времени выполнения приложения логики. Для получения дополнительной [информации см.](#workflow-definition-parameters) <p><p>Чтобы просмотреть атрибуты в определении рабочего процесса приложения логики, переключитесь с "представления о дизайне" на "вид кода" на портале Azure или Visual Studio, или с помощью инструмента, такого как [Azure Resource Explorer.](https://resources.azure.com) |
| `parameters` | нет | Объект | [Значения параметров определения рабочего процесса](#workflow-definition-parameters) для использования во времени выполнения приложения логики. Определения параметров для этих значений отображаются в [объекте параметров определения рабочего процесса.](#workflow-definition-parameters) Кроме того, если приложение логики использует [управляемые разъемы](../connectors/apis-list.md) для `$connections` доступа к другим службам и системам, этот объект включает объект, который устанавливает значения соединения для использования во время выполнения. |
| `accessControl` | нет | Объект | Для указания атрибутов безопасности для приложения логики, таких как ограничение доступа к IP для запроса триггеров или выполнения входов и выводов истории. Для получения дополнительной информации [см.](../logic-apps/logic-apps-securing-a-logic-app.md) |
||||

Для информации о ресурсах шаблонов, специфичной для логических приложений, учетных записей интеграции и артефактов учетных записей интеграции, [см.](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Определение и параметры рабочего процесса

Определение рабочего процесса приложения логики `definition` отображается в `properties` объекте, который отображается в объекте внутри определения ресурсов приложения логики. Этот `definition` объект является тем же объектом, который отображается в представлении кода и полностью описан в [ссылке Schema для](../logic-apps/logic-apps-workflow-definition-language.md) темы языка определения рабочего процесса. Определение рабочего процесса `parameters` включает объект внутреннего декларирования, где можно определить новые или отсваивать существующие параметры для значений, используемых определением рабочего процесса во время выполнения. Затем можно ссылаться на эти параметры внутри триггера или действия в рабочем процессе. По умолчанию `parameters` этот объект пуст, если приложение логики не создает соединения с другими службами и системами через [управляемые разъемы.](../connectors/apis-list.md)

Чтобы установить значения для параметров определения `parameters` рабочего процесса, используйте объект, который находится *вне* определения рабочего процесса, но все еще *находится в* определении ресурсов приложения логики. В этом `parameters` внешнем объекте можно ссылаться на ранее объявленные параметры шаблона, которые могут принимать значения при развертывании из файла параметров.

> [!TIP]
>
> В качестве наилучшей практики не ссылайтесь непосредственно на параметры шаблона, которые оцениваются при развертывании, из определения рабочего процесса. Вместо этого объявить параметр определения рабочего процесса, который можно установить в `parameters` объекте, который находится за *пределами* определения рабочего процесса, но все еще находится *внутри* определения ресурсов приложения логики. Для получения дополнительной [информации см.](#parameter-references)

Этот синтаксис показывает, где можно объявить параметры как на уровне определения шаблона, так и на уровне определения рабочего процесса, а также где можно установить эти значения параметров, ссылаясь на параметры шаблона и определения рабочего процесса:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": "[parameters('<template-parameter-name>')]"
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Безопасные параметры определения рабочего процесса

Для параметра определения рабочего процесса, который обрабатывает конфиденциальную информацию, пароли, ключи доступа или `securestring` `secureobject` секреты во время выполнения, декларировать или отсеивать параметр для использования типа параметра. Этот параметр можно ссылаться на всей части и в пределах определения рабочего процесса. На верхнем уровне шаблона объявите параметр, который имеет тот же тип для обработки этой информации при развертывании.

Чтобы установить значение для параметра определения `parameters` рабочего процесса, используйте объект, который находится *вне* определения рабочего процесса, но все еще *находится внутри* определения ресурсов приложения логики, чтобы ссылаться на параметр шаблона. Наконец, чтобы передать значение параметру шаблона при развертывании, храните это значение в [Хранилище ключей Azure](../azure-resource-manager/templates/key-vault-parameter.md) и ссылка на хранилище ключей в [файле параметров,](#template-parameter-files) который используется шаблоном при развертывании.

Этот пример шаблона показывает, как вы можете выполнить эти задачи, определив обеспеченные параметры, когда это необходимо, так что вы можете хранить их значения в Azure Key Vault:

* Объявить защищенные параметры значений, используемых для проверки подлинности доступа.
* Используйте эти значения как на уровнях шаблона, так и на уровнях определения рабочего процесса.
* Предоставьте эти значения с помощью файла параметров.

**Шаблон**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Файл параметров**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Лучшие практики - параметры определения рабочего процесса

Чтобы убедиться, что Logic App Designer может правильно показать параметры определения рабочего процесса, следуйте следующим рекомендациям:

* Включите `defaultValue` атрибут, который может указывать пустые значения, для всех параметров, за исключением чувствительных или чувствительных значений.

* Всегда используйте защищенные параметры для имен пользователей, паролей и секретов. Чтобы скрыть или защитить чувствительные значения параметров, следуйте инструкциям по следующим темам:

  * [Рекомендации по безопасности по параметрам действия](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Рекомендации по безопасности параметров в определениях рабочего процесса](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Передайте безопасные значения параметров с помощью Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Для получения дополнительной информации о параметрах определения рабочего процесса [см.](../logic-apps/logic-apps-workflow-definition-language.md#parameters)

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Определения ресурсов соединения

Когда приложение логики создает и использует соединения с другими службами и `resources` системой с помощью [управляемых разъемов,](../connectors/apis-list.md)объект шаблона содержит определения ресурсов для этих соединений.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Определения ресурсов соединения ссылаются на параметры верхнего уровня шаблона для их значений, что означает, что вы можете предоставить эти значения при развертывании с помощью файла параметров. Убедитесь, что соединения используют ту же группу ресурсов и местоположение Azure, что и приложение логики.

Вот пример определения ресурсов для подключения Office 365 Outlook и соответствующих параметров шаблона:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

Определение ресурсов приложения логики также работает с определениями ресурсов соединения таким образом:

* В определении рабочего `parameters` процесса объект `$connections` объявляет параметр для значений соединения для использования во времени выполнения приложения логики. Кроме того, триггер или действие, создающее соединение, использует `$connections` соответствующие значения, которые проходят через этот параметр.

* *За пределами* определения *inside* рабочего процесса, но все `parameters` еще в определении ресурсов приложения логики, другой объект устанавливает значения для использования во времени выполнения `$connections` параметра, ссылаясь на соответствующие параметры шаблона. Эти значения используют выражения шаблонов для справочных ресурсов, которые надежно хранят метаданные для соединений в приложении логики.

  Например, метаданные могут включать строки соединения и маркеры доступа, которые можно хранить в [Azure Key Vault.](../azure-resource-manager/templates/key-vault-parameter.md) Чтобы передать эти значения параметрам шаблона, вы ссылаетесь на хранилище ключей в [файл параметров,](#template-parameter-files) который используется шаблоном при развертывании. Для получения дополнительной информации о различиях в параметрах ссылки [см.](#parameter-references)

  При открытии определения рабочего процесса приложения в представлении кода через `$connections` портал Azure или Visual Studio объект отображается вне определения рабочего процесса, но на том же уровне. Этот заказ в представлении кода упрощает ссылку на эти параметры при ручном обновлении определения рабочего процесса:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Определение ресурсов приложения логики `dependsOn` имеет объект, который определяет зависимости от соединений, используемых приложением логики.

Каждое создаваемое соединение имеет уникальное имя в Azure. При создании нескольких подключений к одной и той же службе или системе каждое имя соединения прикрепится с числом, которое приращается с каждым новым соединением, созданным, например, `office365` `office365-1`и так далее.

В этом примере показаны взаимодействия между определением ресурсов приложения логики и определением ресурсов соединения для Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Параметры безопасного соединения

Для параметра соединения, который обрабатывает конфиденциальную информацию, пароли, ключи доступа `parameterValues` или секреты, определение ресурса соединения включает объект, который определяет эти значения в формате пары именной ценности. Чтобы скрыть эту информацию, можно объявить или отсваивать `securestring` `secureobject` параметры шаблона для этих значений с помощью типов параметров или параметров. Затем эту информацию можно хранить в [Хранилище ключей Azure.](../azure-resource-manager/templates/key-vault-parameter.md) Чтобы передать эти значения параметрам шаблона, вы ссылаетесь на хранилище ключей в [файл параметров,](#template-parameter-files) который используется шаблоном при развертывании.

Вот пример, который предоставляет имя учетной записи и ключ доступа для подключения к хранению Azure Blob:

**Файл параметров**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Шаблон**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Аутентификативные соединения

После развертывания приложение логики работает сквозной с допустимыми параметрами. Тем не менее, вы все равно должны разрешить любые соединения OAuth для создания действительных токенов доступа для [проверки подлинности учетных данных.](../active-directory/develop/authentication-scenarios.md) Для получения дополнительной [информации см.](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)

Некоторые соединения поддерживают сяочку с помощью [принципа службы](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) для авторизации соединений для логического приложения, [зарегистрированного в Azure AD.](../active-directory/develop/quickstart-register-app.md) Например, в этом определении ресурсов соединения Azure Data Lake показано, как ссылаться на параметры шаблона, которые обрабатывают информацию директора службы, и как шаблон декларирует эти параметры:

**Определение ресурсов соединения**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Атрибут | Описание |
|-----------|-------------|
| `token:clientId` | Приложение или идентификатор клиента, связанный с вашим директором службы |
| `token:clientSecret` | Ключевое значение, связанное с основным сервисом |
| `token:TenantId` | Идентификатор каталога для вашего арендатора Azure AD |
| `token:grantType` | Запрошенный тип гранта, `client_credentials`который должен быть . Для получения дополнительной [информации см. платформу идентификации Майкрософт и поток учетных данных клиентов OAuth 2.0.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) |
|||

**Определения параметров шаблона**

Объект верхнего уровня `parameters` шаблона объявляет эти параметры для примерного соединения:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Для получения дополнительной информации о работе с директорами служб ы см.

* [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../active-directory/develop/howto-create-service-principal-portal.md)
* [Создайте принцип службы Azure с помощью Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Создайте основной сервис с сертификатом с помощью Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Ссылки на параметры

Для ссылки параметров шаблона можно использовать выражения шаблонов с [функциями шаблона,](../azure-resource-manager/templates/template-functions.md)которые оцениваются при развертывании. Выражения шаблона используют квадратные скобки **(**

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Для ссылки на параметры определения рабочего процесса [используются выражения и функции языка определения рабочего процесса,](../logic-apps/workflow-definition-language-functions-reference.md)которые оцениваются во время выполнения. Вы можете заметить, что некоторые функции шаблона и функции определения рабочего процесса имеют одно и то же имя. Выражения определения рабочего процесса начинаются с**@** символа "at" ( ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Значения параметров шаблона можно передать в определение рабочего процесса для приложения логики для использования во время выполнения. Однако избегайте использования параметров шаблона, выражений и синтаксиса в определении рабочего процесса, поскольку Логический Разработчик приложений не поддерживает элементы шаблона. Кроме того, синтаксис шаблонов и их выражения могут усложнить код из-за различий в том, когда будут оцениваться выражения.

Вместо этого выполните эти общие шаги, чтобы объявить и ссылку параметров определения рабочего процесса для использования во время выполнения, объявить и ссылку параметров шаблона для использования при развертывании, и указать значения, которые можно пройти при развертывании с помощью файла параметров. Для получения подробной информации смотрите [определение рабочего процесса и параметры,](#workflow-definition-parameters) раздел ранее в этой теме.

1. Создайте шаблон и обижайте параметры шаблона для принятия и использования значений при развертывании.

1. В определении рабочего процесса объявите параметры для принятия и использования значений во время выполнения. Затем можно ссылаться на эти значения во всем и в пределах определения рабочего процесса.

1. В `parameters` объекте, который находится *за пределами* определения рабочего процесса, но все еще *находится внутри* определения ресурсов приложения логики, установите значения параметров определения рабочего процесса, ссылаясь на соответствующие параметры шаблона. Таким образом, вы можете передать значения параметров шаблона в параметры определения рабочего процесса.

1. В файле параметров укажите значения для шаблона для использования при развертывании.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Шаблон с полным примером

Вот параметризированный шаблон образца, который используется в примерах этой темы:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание шаблонов приложений логики](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
