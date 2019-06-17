---
title: Создание шаблонов развертывания для Azure Logic Apps | Документация Майкрософт
description: Создание шаблонов Azure Resource Manager для развертывания приложений логики
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128606"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Создание шаблонов Azure Resource Manager для развертывания приложений логики

При создании приложения логики, вы можете развернуть в определение приложения логики [шаблона Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Затем можно использовать этот шаблон для автоматизации развертывания, определяя ресурсы и параметры, которые нужно использовать для развертывания и предоставления значений параметров через [файл параметров](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Таким образом, вы можете развернуть приложения логики более просто и в любой среде или группе ресурсов Azure необходимо. 

Azure Logic Apps предоставляет [шаблон Azure Resource Manager готовых logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) что можно повторно использовать, не только для создания приложений логики, но также для определения ресурсов и параметров для развертывания. Вы можете использовать этот шаблон для собственных бизнес-сценариев или настроить его в соответствии со своими требованиями. Дополнительные сведения о [Resource Manager структура и синтаксис шаблона](../azure-resource-manager/resource-group-authoring-templates.md). Свойства и синтаксис JSON см. в разделе о [типах ресурсов Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

Дополнительные сведения о шаблонах Azure Resource Manager см. в статьях:

* [Шаблоны диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Разработка шаблонов Azure Resource Manager для обеспечения согласованности облака](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Структура приложения логики

Определение приложения логики имеет следующие основные разделы, которые можно просмотреть, переключение с «представления конструктора», «view code» или с помощью средства, такие как [обозревателя ресурсов Azure](http://resources.azure.com). Определения приложений логики используйте Javascript Object Notation (JSON), поэтому Дополнительные сведения о синтаксисе JSON и свойствах см. в разделе [типов ресурсов Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

* **Ресурс приложения логики**. Описание сведений, таких как расположение приложения логики или регион, тарифный план и определение рабочего процесса.

* **Определение рабочего процесса**. Описывает приложение логики триггеров, действий и работы рабочего процесса с помощью службы Azure Logic Apps. В представлении кода вы найдете определения рабочего процесса в `definition` разделе.

* **Подключения**. Если вы используете управляемые соединители в приложении логики `$connections` раздел ссылается на другие ресурсы для безопасного хранения метаданных об этих соединений между свое приложение логики и других систем или служб, таких как строки подключения и маркеры доступа. В определение приложения логики, ссылки на эти подключения отображаются внутри определения приложения логики `parameters` раздел.

Для создания шаблона приложения логики, можно использовать с развертываний групп ресурсов Azure, необходимо определить ресурсы и при необходимости параметризовать. Например, если развертывание осуществляется в тестовой, рабочей среде или в среде разработки, то скорее всего в каждой из этих сред потребуется использовать различные строки подключения к базе данных SQL.
Или выполнить развертывания в разных подписках или группах ресурсов.

## <a name="create-logic-app-deployment-templates"></a>Создание шаблонов для развертывания приложений логики

Самый простой способ создать шаблон развертывания допустимый логики приложения используйте Visual Studio и средства Azure Logic Apps для расширения Visual Studio. Загружая приложения логики на портале Azure в Visual Studio, получить шаблон развертывания допустимый, который можно использовать с любой подписки Azure и расположения. Кроме того загружаются в приложение логики автоматически параметризует определение приложения логики, которое внедрено в шаблоне.
Дополнительные сведения о создании и управлении ими приложений логики в Visual Studio, см. в разделе [создания приложений логики с помощью Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) и [управление приложениями логики в Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Кроме Visual Studio или создание шаблона, а необходимые параметры, следуя инструкциям в этом разделе вручную, можно также использовать [модуль PowerShell для создания шаблонов приложений логики](https://github.com/jeffhollan/LogicAppTemplateCreator). Этот модуль с открытым исходным кодом сначала вычисляет, приложение логики и все подключения, которые использует приложение логики. Затем модуль создает ресурсы шаблона с необходимыми параметрами для развертывания. Например предположим, что у вас есть приложение логики, которое получает сообщение из очереди служебной шины Azure и добавляет данные в базе данных Azure SQL. Средство модуль сохраняет всю логику оркестрации и параметризует строки подключения SQL и служебной шины, таким образом, вы можете задать эти значения во время развертывания.

> [!IMPORTANT]
> Подключения должны существовать в той же группе ресурсов Azure, что приложение логики.
> Для модуля PowerShell для работы с любой Azure клиента и подписки доступа, использования маркеров модуль с помощью [клиентского средства Azure Resource Manager](https://github.com/projectkudu/ARMClient). Дополнительные сведения см. в этом [статьи о средстве клиента Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) программа ARMClient рассматривается более подробно.

### <a name="install-powershell-module-for-logic-app-templates"></a>Установите модуль PowerShell для шаблонов приложений логики

Самый простой способ установить модуль из [коллекции PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), используйте следующую команду:

`Install-Module -Name LogicAppTemplate`

Можно также вручную установить модуль PowerShell:

1. Загрузите последнюю версию [создания шаблона приложения логики](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Извлечение папки в папку модуля PowerShell, как правило, это `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Создание шаблона приложения логики — PowerShell

После установки PowerShell можно создать шаблон, используя следующую команду:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` — это идентификатор подписки Azure. Эта строка сначала получает маркер доступа через программу ARMClient, затем передает его по конвейеру в сценарий PowerShell и создает шаблон в JSON-файл.

## <a name="parameters-in-logic-app-templates"></a>Параметры в шаблоны приложений логики

После создания шаблона приложения логики, можно добавлять и редактировать все необходимые параметры. Шаблон содержит несколько `parameters` раздела, например: 

* Определение рабочего процесса приложения логики имеет свой собственный [ `parameters` разделе](../logic-apps/logic-apps-workflow-definition-language.md#parameters) где можно определить все параметры, которые использует ваше приложение логики для приема входных данных во время развертывания.

* Шаблон Resource Manager имеет свой собственный [ `parameters` разделе](../azure-resource-manager/resource-group-authoring-templates.md#parameters), отдельно от приложения логики `parameters` раздел. Пример:

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Например предположим, определение приложения логики, ссылается на идентификатор ресурса, представляющий функцию Azure или рабочего процесса приложения логики вложенные, и вы хотите развернуть этот идентификатор ресурса, вместе с приложением логики, как одно развертывание. Вы можете добавить этот идентификатор в качестве ресурса в шаблоне и параметризовать этим идентификатором. Этот же подход можно использовать для ссылки на пользовательские конечные точки API-интерфейсы или OpenAPI (прежнее название — «Swagger»), которые требуется развернуть с каждой группой ресурсов Azure.

При использовании параметров в шаблоне развертывания, выполните в этом руководстве, чтобы конструктор приложений логики правильное отображение этих параметров.

* Используйте параметры только в эти триггеры и действия:

  * Приложение функции Azure
  * Вложенные или дочерние рабочего процесса приложения логики
  * Вызов API управления
  * URL-адрес API среды выполнения
  * Путь для подключения к API.

* При определении параметров, убедитесь, что необходимо предоставить значения по умолчанию с помощью `defaultValue` значение свойства, например:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Для защиты или сокрытия сведений в шаблонах, Защитите свои параметры. Дополнительные сведения о [способ использования защищенной параметров](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Ниже приведен пример, в котором показано, как можно параметризовать действие «Отправить сообщение» служебной шины Azure:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Ссылок на зависимые ресурсы

Если приложение логики требуются ссылки на зависимые ресурсы, вы можете использовать [функции шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) в шаблоне развертывания приложения логики. Например предположим, требуется, чтобы приложение логики, чтобы ссылаться на функцию Azure или учетную запись интеграции с определениями для партнеров, соглашений и другие артефакты, которые нужно развернуть вместе с приложением логики.
Можно использовать функции шаблонов диспетчера ресурсов, такие как `parameters`, `variables`, `resourceId`, `concat`, и т. д.

Ниже приведен пример, в котором показано, как вы можете заменить идентификатор ресурса для функции Azure с помощью определения этих параметров:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Вот, как использовать эти параметры при указании ссылки на функции Azure:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Добавление логики приложения в проект группы ресурсов

Если у вас есть существующий проект группы ресурсов Azure, можно добавить свое приложение логики на этот проект, используя окно "Структура JSON". Рядом с созданным ранее приложением логики можно добавить новые приложения.

1. В обозревателе решений откройте файл `<template>.json`.

2. Из **представление** меню, выберите **Other Windows** > **структура JSON**.

3. Чтобы добавить ресурс в файл шаблона, выберите **добавить ресурс** в верхней части окна структуры JSON. Также можно щелкнуть правой кнопкой мыши **ресурсы** в окне "Структура JSON", а затем выбрать пункт **Добавить новый ресурс**.

   ![Окно "Структура JSON"](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. В диалоговом окне **Добавление ресурса** найдите и выберите **Приложение логики**. Присвойте имя приложению логики и выберите **Добавить**.

   ![Добавление ресурса](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Получение поддержки

Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание шаблонов приложений логики](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
