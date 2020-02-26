---
title: Запуск конвейера машинного обучения из приложения логики
titleSuffix: Azure Machine Learning
description: Узнайте, как активировать выполнение конвейера машинного обучения с помощью Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122861"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Запуск конвейера Машинное обучение из приложения логики

Запуск конвейера Машинное обучение Azure при появлении новых данных. Например, может потребоваться активировать конвейер для обучения новой модели при появлении новых данных в учетной записи хранения BLOB-объектов. Настройте триггер с помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>предварительные требования

* Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](how-to-manage-workspace.md).

* Конечная точка RESTFUL для опубликованного конвейера Машинное обучение. [Создайте и опубликуйте свой конвейер](how-to-create-your-first-pipeline.md). Затем найдите конечную точку RESTFUL Публишедпипелине с помощью идентификатора конвейера:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Хранилище BLOB-объектов Azure](../storage/blobs/storage-blobs-overview.md) для хранения данных.
* [Хранилище](how-to-access-data.md) данных в рабочей области, содержащее сведения о вашей учетной записи хранилища BLOB-объектов.

## <a name="create-a-logic-app"></a>Создание приложения логики

Теперь создайте экземпляр [приложения логики Azure](../logic-apps/logic-apps-overview.md) . При желании [Используйте среду службы интеграции (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) и [Настройте управляемый клиентом ключ](../logic-apps/customer-managed-keys-integration-service-environment.md) для использования приложением логики.

После подготовки приложения логики выполните следующие действия, чтобы настроить триггер для конвейера.

1. Чтобы предоставить приложению доступ к рабочая область машинного обучения Azure, [Создайте управляемое удостоверение, назначенное системой](../logic-apps/create-managed-service-identity.md) .

1. Перейдите в представление конструктора приложений логики и выберите шаблон Пустое приложение логики. 
    > [!div class="mx-imgBorder"]
    > ![пустой шаблон](media/how-to-trigger-published-pipeline/blank-template.png)

1. В конструкторе найдите BLOB- **объект**. Выберите **при добавлении или изменении большого двоичного объекта (только свойства)** и добавьте этот триггер в приложение логики.
    > [!div class="mx-imgBorder"]
    > ![добавить триггер](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Укажите сведения о подключении для учетной записи хранения BLOB-объектов, которую вы хотите отслеживать для добавления или изменения больших двоичных объектов. Выберите контейнер для отслеживания. 
 
    Выберите **интервал** и **частоту** опроса обновлений, которые работают за вас.  

    > [!NOTE]
    > Этот триггер будет отслеживать выбранный контейнер, но не будет отслеживать вложенные папки.

1. Добавьте действие HTTP, которое будет выполняться при обнаружении нового или измененного большого двоичного объекта. Выберите **+ новый шаг**, затем найдите и выберите действие HTTP.

  > [!div class="mx-imgBorder"]
  > ![Поиск](media/how-to-trigger-published-pipeline/search-http.png) действий HTTP

  Для настройки действия используйте следующие параметры:

  | Параметр | Значение | 
  |---|---|
  | Действие HTTP | POST |
  | URI |Конечная точка для опубликованного конвейера, которую вы нашли в качестве [необходимого компонента](#prerequisites) |
  | Режим проверки подлинности | Управляемое удостоверение |

1. Настройте расписание, чтобы задать значение любого [пути PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) , который вы имеете в наличии:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Используйте `DataStoreName`, добавленные в рабочую область в качестве [необходимого компонента](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Параметры HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Выберите **сохранить** , и ваше расписание теперь готово.