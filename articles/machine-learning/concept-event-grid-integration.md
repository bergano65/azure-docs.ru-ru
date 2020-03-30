---
title: Потребление событий машинного обучения Azure
titleSuffix: Azure Machine Learning
description: В этой статье узнайте, как использовать Azure Event Grid для подписки, реакции и отписаться от событий, генерируемых Машинным обучением Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139051"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Потребляйте события машинного обучения Azure (Предварительный просмотр)

Azure Machine Learning управляет всем жизненным циклом процесса машинного обучения, включая обучение модели, развертывание моделей и мониторинг. События Azure Machine Learning позволяют приложениям реагировать на события в течение жизненного цикла машинного обучения, такие как завершение обучающих забегов, регистрация и развертывание моделей, а также обнаружение дрейфа данных, используя современные без сервера Архитектуры. 

Эти события публикуются через [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Используя портал Azure, Powershell или Azure CLI, клиенты могут легко подписаться на [события, указав один или несколько типов событий и уточнив условия.](/azure/event-grid/event-filtering) Клиенты также имеют выбор для создания широкого спектра обработчиков событий, таких как Функции Azure, приложения логики Azure или общие webhooks. Azure Machine Learning, наряду с Azure Event Grid, предоставляет высокодоступную, надежную и терпимую к ошибке платформу доставки событий для создания приложений, управляемых событиями.

Для получения информации об использовании машинного обучения Azure с помощью Event Grid [см.](how-to-use-event-grid.md)

## <a name="the-event-model"></a>Модель мероприятия 

Azure Event Grid читает события из источников, таких как Машинное обучение Azure и другие службы Azure. Затем эти события отправляются обработчикам событий, таким как концентраторы событий Azure, функции Azure, логические приложения и другие. Следующая диаграмма показывает, как Event Grid соединяет источники и обработчики, но не является полным списком поддерживаемых интеграций.

![Функциональная модель Сетки событий Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Для получения дополнительной информации об источниках событий и обработчиках событий [см.](/azure/event-grid/overview)

## <a name="azure-machine-learning-event-types"></a>Типы событий машинного обучения Azure

Azure Machine Learning предоставляет события в различных точках жизненного цикла машинного обучения: 

| Тип события | Описание |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Поднятый при завершении эксперимента по машинном обучению |
| `Microsoft.MachineLearningServices.ModelRegistered` | Поднято, когда модель машинного обучения зарегистрирована в рабочем пространстве |
| `Microsoft.MachineLearningServices.ModelDeployed` | Поднято при завершении развертывания службы выводов с одной или нескольких моделями |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Поднято при завершении выполнения задания обнаружения дрейфа данных для двух наборов данных |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Поднятый при изменении статуса выполнения, в настоящее время поднятый только при «неудаве» статусе выполнения |

## <a name="subscribe-to-machine-learning-events"></a>Подписаться на мероприятия машинного обучения

Подписка на события машинного обучения Azure защищена управлением доступом на основе ролей (RBAC). Только [автор или владелец](how-to-assign-roles.md#default-roles) рабочего пространства может создавать, обновлять и удалять подписки на события.

Подписка на события может быть отфильтрована в зависимости от различных условий. Фильтры могут применяться к подпискам на события во время [создания](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) подписки на событие или [позже](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Фильтрация по типу события
Подписка на событие может указать один или несколько типов событий Машинного обучения Azure.

### <a name="filter-by-event-subject"></a>Фильтр по объекту события
Azure Event Grid поддерживает тематические фильтры, основанные на __матчах__ и __заканчивающихся,__ так что события с соответствующей темой доставляются абоненту. Различные события машинного обучения имеют разный формат предмета.

| Тип события | Тематический формат | Пример предмета |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Расширенная фильтрация

Azure Event Grid также поддерживает расширенную фильтрацию на основе опубликованной схемы событий. Подробности событий Azure Machine Learning можно найти в [схеме событий Azure Event Grid для машинного обучения Azure.](../event-grid/event-schema-machine-learning.md)

Некоторые образцы расширенных фильтраций, которые вы можете выполнить, включают в себя:

* Для `Microsoft.MachineLearningServices.ModelRegistered` события отфильтруй значение тегов модели:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Чтобы узнать больше о том, как применять фильтры, [см.](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)

## <a name="consume-machine-learning-events"></a>Потребление событий машинного обучения

Приложения, обрабатывающие события машинного обучения, должны следовать нескольким рекомендуемым методам:

> [!div class="checklist"]
> * Поскольку несколько подписок могут быть настроены для маршрутизатики в один и тот же обработчик событий, важно не предполагать, что события происходят из определенного источника, а проверить тему сообщения, чтобы убедиться, что оно исходит от ожидаемого рабочего пространства машинного обучения.
> * Подобным образом убедитесь, что вы готовы к обработке этого типа событий (eventType), а также не следует предполагать, что все получаемые события будут иметь ожидаемый тип.
> * Так как сообщения могут поступать не по порядку и после некоторой задержки, используйте поля ETag, чтобы понять, являются ли сведения об объектах актуальными.  Используйте поля sequencer для понимания последовательности событий для каждого отдельного объекта.
> * Игнорируйте поля, которые вам непонятны. Такой подход поможет обеспечить устойчивость к новым функциям, которые могут быть добавлены в будущем.
> * Неудачные или отмененные операции машинного обучения Azure не вызывают событие. Например, если развертывание модели не удается Microsoft.MachineLearningServices.ModelDeployed не будет срабатывать. Рассмотрим такой режим сбоя при проектировании приложений. Вы всегда можете использовать Azure Machine Learning SDK, CLI или портал, чтобы проверить состояние операции и понять подробные причины сбоя.

Azure Event Grid позволяет клиентам создавать обработчики сообщений, которые могут быть вызваны событиями машинного обучения Azure. Некоторые примечательные примеры обработчиков сообщений:
* Проверка
* Azure Logic Apps
* Центры событий Azure
* Конвейер фабрики данных Azure
* Общие веб-крючки, которые могут быть размещены на платформе Azure или в другом месте

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о Event Grid и попробуйте события Azure Machine Learning:

- [An introduction to Azure Event Grid](../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure")
- [Схема событий Azure Event Grid для машинного обучения Azure](../event-grid/event-schema-machine-learning.md)
- [Создание рабочих процессов, управляемых событиями, с помощью машинного обучения Azure](how-to-use-event-grid.md)
