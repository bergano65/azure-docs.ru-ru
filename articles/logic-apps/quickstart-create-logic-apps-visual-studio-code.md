---
title: Создание автоматизированных рабочих процессов и управление ими в Visual Studio Code с помощью Azure Logic Apps
description: Краткое руководство о создании приложений логики и управление ими с помощью JSON в Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: f0b568289a50b9883e6e0f1cba6b793c9e6e90e8
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051732"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Краткое руководство. Создание автоматизированных рабочих процессов приложений логики и управление ими в Visual Studio Code

С помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и Visual Studio Code можно создать приложения логики, которые автоматизируют задачи, рабочие процессы и процессы для интеграции приложений, данных, систем и служб по предприятиям и организациям, и управлять ими. В этом кратком руководстве показано, как создать и изменить определения рабочего процесса, работая со схемой определения рабочего процесса в нотации объектов JavaScript (JSON) через взаимодействие на основе кода. Вы также можете работать с приложениями логики, уже развернутыми в Azure. 

Хотя эти задачи можно выполнять на портале Azure (https://portal.azure.com) ) и в Visual Studio, Visual Studio Code упрощает начало работы, если вы уже знакомы с определениями приложения логики и хотите работать непосредственно с кодом. Например, также можно отключать, включать, удалять и обновлять уже созданные приложения логики. Кроме того, вы можете работать в приложениях логики и учетных записях интеграции на любой платформе разработки с поддержкой Visual Studio Code, например Linux, Windows и Mac.

В этой статье приводятся инструкции по созданию такого же приложения логики, что и в [кратком руководстве по созданию приложения логики на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), в котором основное внимание уделяется базовым понятиям. В Visual Studio Code приложение логики выглядит так, как показано в следующем примере.

![Готовое приложение логики](./media/create-logic-apps-visual-studio-code/overview.png)

Прежде чем начать, убедитесь, что вы выполняете следующие условия.

* Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Базовые знания об [определениях рабочих процессов приложения логики](../logic-apps/logic-apps-workflow-definition-language.md) и их структуре, которая использует нотацию объектов JavaScript (JSON). 

  Если вы только начали работать с приложениями логики, изучите статью [Краткое руководство. Создание первого автоматизированного рабочего процесса с помощью Azure Logic Apps на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), который больше фокусируется на основных понятиях. 

* Доступ к Интернету для входа в Azure и в подписку Azure.

* Скачайте и установите эти средства, если вы еще этого не сделали: 

  * бесплатная [версия Visual Studio Code 1.25.1 или более поздняя версия]("https://code.visualstudio.com/);

  * расширение Visual Studio Code для Azure Logic Apps.

    Это расширение можно скачать и установить из [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) или непосредственно из Visual Studio Code. 
    Перезапустите Visual Studio Code после установки. 

    ![Поиск расширения Visual Studio Code для Azure Logic Apps](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Значок Azure на панели инструментов Visual Studio Code означает, что расширение установлено правильно. 

    ![Установленное расширение](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Дополнительные сведения см. на странице [Extension Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery) (Расширения в Marketplace). Вы также можете просмотреть и внести дополнения в версию с открытым кодом расширения [Azure Logic Apps для Visual Studio Code на сайте GitHub](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Вход в Azure

1. Откройте Visual Studio Code. На панели инструментов Visual Studio Code щелкните значок Azure. 

   ![Выбор значка Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. В окне Azure в разделе **Logic Apps** выберите пункт **Sign in to Azure** (Войти в Azure). 

   ![Выбор пункта "Sign in to Azure" (Войти в Azure).](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Вам будет предложено выполнить вход с помощью предоставленного кода проверки подлинности. 

1. Скопируйте код проверки подлинности, а затем нажмите кнопку **Копировать и открыть**, после чего откроется новое окно браузера.

   ![Запрос на вход](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Введите код проверки подлинности. При появлении запроса нажмите кнопку **Продолжить**.

   ![Ввод кода](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Выберите учетную запись Azure. После входа можно закрыть браузер и вернуться в Visual Studio Code.

   Теперь в окне Azure в разделах Logic Apps и учетных записей интеграции отображаются подписки Azure в вашей учетной записи. 

   ![Выберите подписку.](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Если нужные подписки отсутствуют, рядом с названием **Logic Apps** щелкните **Select Subscriptions** (Выбрать подписки) (значок фильтра). Найдите и выберите нужные подписки.

1. Чтобы просмотреть существующие приложения логики или учетные записи интеграции в подписке Azure, разверните подписку.

   ![Просмотр приложений логики и учетных записей интеграции](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Создание приложения логики

1. Если вы еще не вошли подписку Azure в Visual Studio Code, выполните описанные в этой статье действия по [входу](#sign-in-azure).

1. В контекстном меню подписки выберите команду **Создать**.

   ![Выбор команды "Создать"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. В списке групп ресурсов Azure в подписке выберите имеющуюся группу ресурсов или **создайте группу ресурсов**. 

   В этом примере показано создание группы ресурсов.

   ![Создание группы ресурсов](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Укажите имя для группы ресурсов Azure и нажмите клавишу ВВОД.

   ![Указание имени для группы ресурсов](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Выберите расположение центра обработки данных для сохранения метаданных приложения логики.

   ![Выбор расположения](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Укажите имя для приложения логики, а затем нажмите клавишу ВВОД.

   ![Указание имени для приложения логики](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Новое приложение логики появится в окне Azure под подпиской Azure. Теперь можно приступить к созданию определения рабочего процесса приложения логики.

1. В контекстном меню приложения логики выберите **Открыть в редакторе**. 

   ![Открытие приложения логики в редакторе](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   В Visual Studio Code откроется шаблон определения рабочего процесса приложения логики (файл logicapp.json), и вы сможете приступить к созданию рабочего процесса приложения логики.

   ![Новое определение рабочего процесса приложения логики](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. В файле шаблона определения рабочего процесса приложения логики начните создавать определение рабочего процесса приложения логики. Технические справочные сведения см. в статье [Сведения о схеме языка определения рабочих процессов в Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Ниже приведен пример определения логики. Как правило, элементы JSON отображаются в каждом разделе в алфавитном порядке, но в этом примере элементы показаны в порядке появления действий приложения логики в конструкторе.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Закончив, сохраните файл определения приложения логики. Когда в Visual Studio Code появится запрос на подтверждение отправки определения приложения логики в подписку Azure, нажмите кнопку **Отправить**.

   ![Отправка нового приложения логики](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   После того как Visual Studio Code опубликует приложение логики в Azure, приложение будет доступно и запущено на портале Azure. 

   ![Опубликованное приложение логики на портале Azure](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Изменение приложения логики

Для работы с приложением логики, уже развернутым в Azure, откройте файл определения рабочего процесса этого приложения в Visual Studio Code.

1. Если вы еще не вошли подписку Azure в Visual Studio Code, выполните описанные в этой статье действия по [входу](#sign-in-azure).

1. В окне Azure в разделе **Logic Apps** разверните подписку Azure и выберите нужное приложение логики. 

1. В меню приложения логики выберите пункт **Открыть в редакторе**. Или рядом с именем приложения логики щелкните значок редактирования.

   ![Открытие редактора для имеющегося приложения логики](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   В Visual Studio Code откроется файл logicapp.json для определения рабочего процесса приложения логики.

   ![Открытое определение рабочего процесса приложения логики](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Внесите необходимые изменения в определение приложения логики.

1. Сохраните изменения, когда закончите.

1. Когда в Visual Studio Code появится запрос на обновление определения приложения логики в подписке Azure, нажмите кнопку **Отправить**. 

   ![Отправка изменений](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> * [Создание приложений логики в Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)