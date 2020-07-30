---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: aa55a3550fbb4fcdcd97c26fca425b11ba589fcc
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369462"
---
С помощью клиентской библиотеки для разработки Распознавания речи (LUIS) для Python можно выполнять следующие задачи.

* Создавать приложения.
* Добавлять намерения, сущности и примеры речевых фрагментов.
* Добавлять компоненты, такие как список фраз.
* Обучать и публиковать приложения.

[Справочная документация](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Пакет для разработки (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Образцы кода](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Python 3.x](https://www.python.org/).
* Получив подписку Azure, [создайте ресурс LUIS](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) на портале Azure, чтобы получить ключ и конечную точку. Дождитесь, пока закончится развертывание, и нажмите кнопку **Перейти к ресурсу**.
    * Для подключения приложения к LUIS для разработки потребуется ключ и конечная точка для [созданного](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве. Вы можете использовать ценовую категорию "Бесплатный" (`F0`), чтобы поработать со службой.

## <a name="setting-up"></a>Настройка

### <a name="install-the-python-library-for-luis"></a>Установка библиотеки Python для LUIS

В каталоге приложения установите клиентскую библиотеку для разработки Распознавания речи (LUIS) для Python с помощью следующей команды.

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Объектная модель

Клиент для разработки LUIS является объектом [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python), который проходит проверку подлинности в Azure и содержит ваш ключ разработки.

После создания используйте клиент для доступа к функциям, включая:

* Приложения — [создание](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [удаление](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [публикация](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-).
* Примеры речевых фрагментов — [добавление по пакетам](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [удаление по идентификатору](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-).
* Компоненты — управление [списками фраз](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-).
* Модель — управление [намерениями](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) и сущностями.
* Шаблоны — управление [шаблонами](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-).
* Обучение — [обучение](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) приложения и опрос [состояния обучения](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-).
* [Версии](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) — управление с помощью клонирования, экспорта и удаления.


## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки для разработки Распознавания речи (LUIS) для Python.

* [Создание приложения](#create-a-luis-app)
* [Добавление сущностей](#create-entities-for-the-app)
* [Добавление намерений](#create-intent-for-the-app)
* [Добавление примеров фрагментов речи](#add-example-utterance-to-intent)
* [Обучение приложения](#train-the-app)
* [Публикация приложения](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте приложение Python в предпочитаемом редакторе или среде интегрированной разработки. Затем импортируйте приведенные ниже библиотеки.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=Dependencies)]

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Теперь создайте объект [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) с помощью ключа и используйте его со своей конечной точкой, чтобы создать объект [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python).

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Создание приложения LUIS

1. Создайте приложение LUIS, которое будет содержать модель обработки естественного языка (NLP), содержащую намерения, сущности и примеры речевых фрагментов.

1. Создайте метод [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) добавления [объекта](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), чтобы создать приложение. Название, язык и региональные параметры являются обязательными свойствами.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Создание намерения для приложения
Основным объектом в модели приложения LUIS является намерение. Намерение совпадает с группировкой _намерений_ пользовательских речевых фрагментов. Пользователь может задать вопрос или сделать инструкцию в поисках конкретного _намеренного_ ответа от бота (или другого клиентского приложения). Примерами намерений являются бронирование рейса, запрос о погоде в городе назначения и запрос контактной информации для обслуживания клиентов.

Используйте метод [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) с именем уникального намерения, затем передайте идентификатор приложения, идентификатор версии и новое имя намерения.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Создание сущностей для приложения

Хотя сущности не требуются, они встречаются в большинстве приложений. Сущность извлекает информацию из речевого фрагмента пользователя, необходимую для выполнения намерения пользователя. Существует несколько типов [готовых](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) и пользовательских объектов, каждый из которых имеет собственные модели объекта преобразования данных (DTO).  Обычные готовые сущности, добавляемые в ваше приложение, включают в себя [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) и [ordinal](../luis-reference-prebuilt-ordinal.md).

Этот метод **add_entities** создал `Location`простую сущность с двумя ролями, `Class`простую сущность, `Flight` сложную сущность и добавил несколько готовых сущностей.

Важно знать, что объекты не отмечены намерением. Они могут и обычно относятся ко многим намерениям. Только примеры пользовательских речевых фрагментов помечены для определенного единственного намерения.

Методы создания сущностей являются частью класса [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python). Каждый тип сущности имеет собственную модель объекта преобразования данных (DTO).

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Добавление примера высказывания в намерение

Чтобы определить намерение высказывания и извлечь сущности, приложению нужны примеры речевых фрагментов. Примеры должны быть нацелены на конкретное единственное намерение и должны отмечать все пользовательские объекты. Готовые объекты не нужно отмечать.

Добавьте примеры речевых фрагментов, создав список объектов [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python), по одному объекту для каждого примера высказывания. Каждый пример должен пометить все сущности словарем пар "имя — значение" имени и значения сущности. Значение сущности должно быть точно таким, как оно указано в тексте примера высказывания.

Вызовите [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) с идентификатором приложения, идентификатором версии и списком примеров. Вызов реагирует на список результатов. Вам нужно проверить результат каждого примера, чтобы убедиться, что он успешно добавлен в модель.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>Обучение приложения

После создания модели приложение LUIS необходимо обучить этой версии модели. Обученную модель можно использовать в [контейнере](../luis-container-howto.md) или [опубликовать](../luis-how-to-publish-app.md) в промежуточных или рабочих слотах.

Методу [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) требуется идентификатор приложения и идентификатор версии.

Очень маленькая модель, такая как в этом кратком руководстве, будет обучаться очень быстро. Обучение приложений рабочего уровня должно включать в себя вызов опроса с помощью метода [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-), чтобы определить, прошло ли обучение успешно. Ответ представляет собой список объектов [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) с отдельным состоянием для каждого объекта. Все объекты должны быть успешно обучены, чтобы обучение считалось завершенным.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Публикация приложения LUIS

Опубликуйте приложение LUIS с помощью метода [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-). При этом публикуется текущая обученная версия в указанный слот в конечной точке. Ваше клиентское приложение использует эту конечную точку для отправки пользовательских высказываний для прогнозирования намерения и извлечения сущности.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `python` для файла quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с прогнозированием очистите результаты, полученные при выполнении этого краткого руководства, удалив файл и все подкаталоги.