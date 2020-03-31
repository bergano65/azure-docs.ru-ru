---
title: Создание рабочих процессов машинного обучения, управляемых событиями
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать сетку событий с помощью Azure Machine Learning для включения решений, управляемых событиями.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129738"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Создание рабочих процессов машинного обучения с инициативой события (Предварительный просмотр)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) поддерживает события машинного обучения Azure. Вы можете подписаться и использовать такие события, как изменение статуса выполнения, завершение выполнения, регистрация модели, развертывание модели и обнаружение дрейфа данных в рабочей области.

Для получения дополнительной информации о типах событий см. [интеграцию Azure Machine Learning с Event Grid](concept-event-grid-integration.md) и [схемой сетки событий Azure Machine Learning.](/azure/event-grid/event-schema-machine-learning)

Используйте Event Grid для включения общих сценариев, таких как:

* Отправка сообщений электронной почты при сбое выполнения и завершении выполнения
* Используйте функцию лазури после регистрации модели
* Потоковая передача событий от машинного обучения Azure в различные конечные точки
* Триггер провода ML при обнаружении дрейфа

> [!NOTE] 
> В настоящее время события runStatusChanged срабатывают только при **сбой** состояния выполнения
>

## <a name="prerequisites"></a>Предварительные требования
* Доступ участника или владельца к рабочему пространству Azure Machine Learning, для чего вы будете создавать события.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Настроить EventGrid с помощью портала Azure

1. Откройте [портал Azure](https://portal.azure.com) и перейдите в рабочее пространство Azure Machine Learning.

1. Из левой панели выберите __События,__ а затем выберите **Подписку на события.** 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Выберите тип события для потребления. Например, на следующем скриншоте выбрана __зарегистрированная модель,__ __развернута модель,__ __завершена,__ а __дрейф набора данных обнаружен:__

    ![тип добавления](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Выберите конечную точку для публикации события. На следующем скриншоте __концентратор событий__ является выбранной конечной точкой:

    ![выбрать-обработчик событий](./media/how-to-use-event-grid/select-event-handler.png)

После того как вы подтвердили свой выбор, нажмите __Создать__. После настройки эти события будут перенесены в конечную точку.


### <a name="configure-eventgrid-using-the-cli"></a>Настройка EventGrid с помощью CLI

Вы можете установить новейший [ClI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)или использовать облачную оболочку Azure, которая предоставляется как часть подписки Azure.

Для установки расширения Event Grid используйте следующую команду из CLI:

```azurecli-interactive
az add extension --name eventgrid
```

В следующем примере показано, как выбрать подписку Azure и создается новая подписка на событие для машинного обучения Azure:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="filter-events"></a>События фильтра

При настройке событий можно применять фильтры только для запуска определенных данных событий. В приведенном ниже примере для измененных событий статуса выполнения можно отфильтровать по типам выполнения. Событие запускается только при соблюдении критериев. Для того чтобы узнать о данных событий, которые можно отфильтровать, обратитесь к [схеме сетки событий Azure Machine Learning.](/azure/event-grid/event-schema-machine-learning) 

1. Перейдите на портал Azure, выберите новую подписку или существующую. 

1. Выберите вкладку фильтров и прокрутите вниз, чтобы Расширенные фильтры. В **ключе** и **значении** укажите типы свойств, которые вы хотите отфильтровать. Здесь вы можете видеть, что событие срабатывает только при запуске типа запуска или выполнения шага конвейера.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="события фильтра":::

## <a name="sample-scenarios"></a>Примеры сценариев

### <a name="use-a-logic-app-to-send-email-alerts"></a>Используйте logic App для отправки оповещений по электронной почте

Используйте [приложения Логика Azure](https://docs.microsoft.com/azure/logic-apps/) для настройки электронных писем для всех ваших событий. Настраивайте с помощью условий и укажите получателей, чтобы обеспечить совместную работу и информированность между группами.

1. На портале Azure перейдите в рабочее пространство Azure Machine Learning и выберите вкладку событий из левой панели. Отсюда выберите __приложения Logic__. 

    ![выберите-логика-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Вопийте в систему iI Logic App и выберите службу машинного обучения в качестве типа темы. 

    ![выбрать-тип темы](./media/how-to-use-event-grid/select-topic-type.png)

1. Выберите событие (ы), о котором следует уведомлять. Например, следующий скриншот __RunCompleted__.

    ![выбор-событие-запуск](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Метод фильтрации можно использовать в разделе выше или добавить фильтры, чтобы вызвать приложение логики только на подмножестве типов событий. На следующем скриншоте используется __фильтр префикса__ __/datadriftID/runs/.__

    ![фильтр-события](./media/how-to-use-event-grid/filtering-events.png)

1. Затем добавьте шаг к потреблению этого события и поиск электронной почты. Есть несколько различных учетных записей почты, которые можно использовать для получения событий. Вы также можете настроить условия, когда отправить оповещение по электронной почте.

    ![выбрать-электронное действие](./media/how-to-use-event-grid/select-email-action.png)

1. Выберите __Отправить электронное письмо__ и заполнить параметры. В тему можно включить тип и __тему__ __события,__ чтобы помочь фильтровать события. Вы также можете включить ссылку на страницу рабочего пространства для запусков в теле сообщения. 

    ![настроить-электронную почту-тело](./media/how-to-use-event-grid/configure-email-body.png)

1. Чтобы сохранить это действие, выберите **Сохранить Как** в левом углу страницы. Из правой панели, которая появляется, подтвердить создание этого действия.

    ![подтвердить-логику-приложение-создание](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Используйте logic App для запуска рабочих процессов переподготовки при дрейфе данных

Модели со временем не устаревает и не остаются полезными в контексте, в который они работают. Один из способов определить, пришло ли время переподготовки модели, это обнаружение дрейфа данных. 

В этом примере показано, как использовать сетку событий с приложением Azure Logic для запуска переподготовки. Пример запускает конвейер Azure Data Factory при дрейфе данных между обучением модели и наборами данных.

Перед тем, как начать, выполните следующие действия:

* Настройка монитора набора данных для [обнаружения дрейфа данных]( https://aka.ms/datadrift) в рабочей области
* Создайте опубликованный [конвейер Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/)

В этом примере простой конвейер Data Factory используется для копирования файлов в хранилище капли и запуска опубликованного конвейера машинного обучения. Для получения дополнительной информации об этом сценарии узнайте, как настроить [этап машинного обучения в Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-этап](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Начните с создания приложения логики. Перейдите на [портал Azure,](https://portal.azure.com)ищите логические приложения и выберите создать.

    ![поиск-логика-приложение](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Заполните запрашиваемую информацию. Чтобы упростить работу, используйте ту же группу подписок и ресурсов, что и конвейер Azure Data Factory и рабочее пространство машинного обучения Azure.

    ![настройка-логика-приложение для Adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. После создания приложения логики выберите __время возникновения события ресурса Event Grid.__ 

    ![выбор-событие-сетка-триггер](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Войти и заполнить детали для события. Установите __имя ресурса__ в названии рабочего пространства. Установите __тип события__ на __DatasetDriftDetected__.

    ![Войти-и-добавить-событие](./media/how-to-use-event-grid/login-and-add-event.png)

1. Добавьте новый шаг и ищите __фабрику данных Azure.__ Выберите __Создать запуск конвейера.__ 

    ![создать-adfpipeline-запуск](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Войти и указать опубликованный конвейер Azure Data Factory для запуска.

    ![specify-adf-конвейер](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Сохранить и создать логическое приложение с помощью кнопки **сохранения** в верхнем левом верхнем левом конце страницы. Чтобы просмотреть приложение, перейдите в рабочее пространство на [портале Azure](https://portal.azure.com) и нажмите на **события.**

    ![шоу-логика-приложение-вебхук](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Теперь конвейер фабрики данных срабатывает при возникновении дрейфа. Просмотр сведений о пробеге дрейфа данных и конвейере машинного обучения на [новом портале рабочего пространства.](https://ml.azure.com) 

![вид в рабочей области](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Используйте функции Azure для развертывания модели, основанной на тегах

Объект модели машинного обучения Azure содержит параметры, которые можно развернуть развертывания на таких как имя модели, версия, тег и свойство. Событие регистрации модели может вызвать конечную точку, и вы можете использовать функцию Azure для развертывания модели на основе значения этих параметров.

Например, просмотрите [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) репозиторий и выполните последующие действия в файле **чтения.**

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать больше об доступных событиях, смотрите [схему событий Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)
