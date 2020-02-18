---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: e4bba00d6506684a3baf345806bdaa340f6970ca
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909201"
---
С помощью клиентской библиотеки для разработки Распознавания речи (LUIS) для Node.js можно выполнить приведенные ниже задачи.

* Создавать приложения.
* Добавлять намерения, сущности и примеры речевых фрагментов.
* Добавлять компоненты, такие как список фраз.
* Обучать и публиковать приложения.
* Удаление приложения

[Справочная документация](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [Пакет для разработки (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [Пакет среды выполнения (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Примеры](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Предварительные требования

* Ресурс для создания Распознавания речи: [Создание с помощью портала Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Настройка

### <a name="get-your-language-understanding-luis-starter-key"></a>Получение начального ключа LUIS

Получите [ключ для начала работы](../luis-how-to-azure-subscription.md#starter-key), создав ресурс разработки LUIS. Сохраните ключ и конечную точку ключа для следующего шага.

### <a name="create-an-environment-variable"></a>Создание переменной среды

Используя ключ и регион для ключа, создайте две переменные среды для проверки подлинности:

* `LUIS_AUTHORING_KEY` — ключ ресурса для проверки подлинности запросов.
* `LUIS_AUTHORING_ENDPOINT` — конечная точка, связанная с ключом.

Используйте инструкции для своей операционной системы.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Добавив переменную среды, перезапустите окно консоли.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

После добавления переменной среды запустите `source ~/.bashrc` из окна консоли, чтобы применить изменения.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Измените `.bash_profile` и добавьте переменную среды:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

После добавления переменной среды запустите `source .bash_profile` из окна консоли, чтобы применить изменения.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Установка библиотеки NPM для создания LUIS

В каталоге приложения установите зависимости с помощью следующей команды:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Объектная модель

Клиент для разработки LUIS является объектом [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest), который проходит проверку подлинности в Azure и содержит ваш ключ разработки.

После создания используйте клиент для доступа к функциям, включая:

* Приложения — [Добавить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [Удалить](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [Опубликовать](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Примеры речевых фрагментов — [добавление по пакетам](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [удаление по идентификатору](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-).
* Компоненты — управление [списками фраз](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-).
* Модель — управление [намерениями](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) и сущностями.
* Шаблоны — управление [шаблонами](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-).
* Обучение — [обучение](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) приложения и опрос [состояния обучения](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-).
* [Версии](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) — управление с помощью клонирования, экспорта и удаления.


## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки для разработки LUIS для Node.js:

* [Создание приложения](#create-a-luis-app)
* [Добавление сущностей](#create-entities-for-the-app)
* [Добавление намерений](#create-intent-for-the-app)
* [Добавление примеров фрагментов речи](#add-example-utterance-to-intent)
* [Обучение приложения](#train-the-app)
* [Публикация приложения](#publish-a-language-understanding-app)
* [Удаление приложения](#delete-a-language-understanding-app)
* [Список приложений](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

Создайте текстовый файл в предпочитаемой интегрированной среде разработки или редакторе, с именем `luis_authoring_quickstart.js`. Затем добавьте следующие зависимости.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Создайте переменные для конечной точки Azure и ключа ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Теперь создайте объект [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) с помощью ключа и используйте его со своей конечной точкой, чтобы создать объект [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest).

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Создание приложения LUIS

1. Создайте приложение LUIS, которое будет содержать модель обработки естественного языка (NLP), содержащую намерения, сущности и примеры речевых фрагментов.

1. Создайте метод [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) добавления [объекта](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest), чтобы создать приложение. Название, язык и региональные параметры являются обязательными свойствами.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Создание намерения для приложения
Основным объектом в модели приложения LUIS является намерение. Намерение совпадает с группировкой _намерений_ пользовательских речевых фрагментов. Пользователь может задать вопрос или сделать инструкцию в поисках конкретного _намеренного_ ответа от бота (или другого клиентского приложения). Примерами намерений являются бронирование рейса, запрос о погоде в городе назначения и запрос контактной информации для обслуживания клиентов.

Используйте метод [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) с именем уникального намерения, затем передайте идентификатор приложения, идентификатор версии и новое имя намерения.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Создание сущностей для приложения

Хотя сущности не требуются, они встречаются в большинстве приложений. Сущность извлекает информацию из речевого фрагмента пользователя, необходимую для выполнения намерения пользователя. Существует несколько типов [готовых](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) и пользовательских объектов, каждый из которых имеет собственные модели объекта преобразования данных (DTO).  Обычные готовые сущности, добавляемые в ваше приложение, включают в себя [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) и [ordinal](../luis-reference-prebuilt-ordinal.md).

Этот метод **add_entities** создал `Location`простую сущность с двумя ролями, `Class`простую сущность, `Flight` сложную сущность и добавил несколько готовых сущностей.

Важно знать, что объекты не отмечены намерением. Они могут и обычно относятся ко многим намерениям. Только примеры пользовательских речевых фрагментов помечены для определенного единственного намерения.

Методы создания сущностей являются частью класса [Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest). Каждый тип сущности имеет собственную модель объекта преобразования данных (DTO).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Добавление примера высказывания в намерение

Чтобы определить намерение высказывания и извлечь сущности, приложению нужны примеры речевых фрагментов. Примеры должны быть нацелены на конкретное единственное намерение и должны отмечать все пользовательские объекты. Готовые объекты не нужно отмечать.

Добавьте примеры речевых фрагментов, создав список объектов [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest), по одному объекту для каждого примера высказывания. Каждый пример должен пометить все сущности словарем пар "имя — значение" имени и значения сущности. Значение сущности должно быть точно таким, как оно указано в тексте примера высказывания.

Вызовите [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) с идентификатором приложения, идентификатором версии и списком примеров. Вызов реагирует на список результатов. Вам нужно проверить результат каждого примера, чтобы убедиться, что он успешно добавлен в модель.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Обучение приложения

После создания модели приложение LUIS необходимо обучить этой версии модели. Обученную модель можно использовать в [контейнере](../luis-container-howto.md) или [опубликовать](../luis-how-to-publish-app.md) в промежуточных или рабочих слотах.

Методу [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) требуется идентификатор приложения и идентификатор версии.

Очень маленькая модель, такая как в этом кратком руководстве, будет обучаться очень быстро. Обучение приложений рабочего уровня должно включать в себя вызов опроса с помощью метода [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-), чтобы определить, прошло ли обучение успешно. Ответ представляет собой список объектов [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) с отдельным состоянием для каждого объекта. Все объекты должны быть успешно обучены, чтобы обучение считалось завершенным.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Изучение всех моделей занимает время. Используйте operationResult для проверки состояния обучения.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Публикация приложения LUIS

Опубликуйте приложение LUIS с помощью метода [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-). При этом публикуется текущая обученная версия в указанный слот в конечной точке. Ваше клиентское приложение использует эту конечную точку для отправки пользовательских высказываний для прогнозирования намерения и извлечения сущности.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Удаление приложения Распознавания речи

Удалите приложение LUIS с помощью метода [app.deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-). Это приведет к удалению текущего приложения.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Список приложений Распознавания речи

Получение списка приложений, связанных с ключом Распознавания речи

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `node luis_authoring_quickstart.js` для файла quickstart.

```console
node luis_authoring_quickstart.js
```

Выходные данные командной строки приложения:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
