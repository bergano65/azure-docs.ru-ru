---
title: Триггер запуска провода ML из logic App
titleSuffix: Azure Machine Learning
description: Узнайте, как запустить запуск конвейера ML с помощью приложений Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122861"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Триггер запуска трубопровода машинного обучения из приложения логики

Запуск конвейера машинного обучения Azure при пояслом новых данных. Например, при пояснении новых данных в учетной записи хранилища blob может потребоваться запуск конвейера для обучения новой модели. Настройка триггера с [помощью приложений Azure Logic Apps.](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область машинного обучения Azure. Для получения дополнительной информации [см.](how-to-manage-workspace.md)

* Конечная точка REST для опубликованного конвейера машинного обучения. [Создайте и опубликуйте конвейер.](how-to-create-your-first-pipeline.md) Затем найдите конечную точку REST вашего PublishedPipeline с помощью идентификатора конвейера:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Хранилище azure blob](../storage/blobs/storage-blobs-overview.md) для хранения данных.
* [Хранилище данных](how-to-access-data.md) в рабочем пространстве, содержащее сведения о вашей учетной записи для хранения капли.

## <a name="create-a-logic-app"></a>Создание приложения логики

Теперь создайте экземпляр [приложения Azure Logic App.](../logic-apps/logic-apps-overview.md) При желании [воспользуйтесь средой служб ы интеграции (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) и [настройте ключ, управляемый клиентом,](../logic-apps/customer-managed-keys-integration-service-environment.md) для использования вашим приложением Logic App.

После того, как приложение Logic App будет подготовлено, используйте эти шаги для настройки триггера для конвейера:

1. [Создайте системную управляемую идентификацию,](../logic-apps/create-managed-service-identity.md) чтобы дать приложению доступ к рабочему пространству машинного обучения Azure.

1. Перейдите к представлению Logic App Designer и выберите шаблон Blank Logic App. 
    > [!div class="mx-imgBorder"]
    > ![пустой шаблон.](media/how-to-trigger-published-pipeline/blank-template.png)

1. В дизайнер, поиск **капли**. Выберите **при добавлении или изменении капли (только для свойств)** триггера и добавьте этот триггер в logic App.
    > [!div class="mx-imgBorder"]
    > ![Добавление триггера](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Заполните информацию о подключении для учетной записи хранилища Blob, которую вы хотите контролировать для дополнений или модификаций blob. Выберите контейнер для мониторинга. 
 
    Выберите **Интервал** и **частота** для опроса для обновлений, которые работают для вас.  

    > [!NOTE]
    > Этот триггер будет контролировать выбранный контейнер, но не будет контролировать субфолители.

1. Добавьте действие HTTP, которое будет работать при обнаружении новой или измененной капли. Выберите **новый шаг,** затем ищите и выберите действие HTTP.

  > [!div class="mx-imgBorder"]
  > ![Поиск действий HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Используйте следующие настройки для настройки действия:

  | Параметр | Значение | 
  |---|---|
  | Действие HTTP | POST |
  | URI |конечная точка опубликованного конвейера, которую вы нашли в качестве [предпосылки](#prerequisites) |
  | Режим проверки подлинности | Управляемое удостоверение |

1. Назначьте расписание, чтобы установить значение любых [параметров трубопровода DataPath,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) которые у вас могут быть:

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

    Используйте `DataStoreName` добавленное в рабочее пространство в качестве [предварительного условия.](#prerequisites)
     
    > [!div class="mx-imgBorder"]
    > ![Параметры HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Выберите **Сохранить** и ваше расписание теперь готово.