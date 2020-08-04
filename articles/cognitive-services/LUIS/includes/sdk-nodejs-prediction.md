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
ms.openlocfilehash: 19da911cbc662f47e7bbd16aaddf8803d4109d6a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369295"
---
С помощью клиентской библиотеки LUIS для Node.js можно выполнить приведенные ниже задачи.

* Прогнозирование по слоту
* Прогнозирование по версии

[Справочная документация](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Пакет среды выполнения (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Образцы кода](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js)

## <a name="prerequisites"></a>Предварительные требования

* Ресурс среды выполнения LUIS: [Создание с помощью портала Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Идентификатор приложения LUIS. Используйте общедоступный идентификатор приложения для Интернета вещей `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. Пользовательский запрос, используемый в коде из этого краткого руководства, относится только к этому приложению.

## <a name="setting-up"></a>Настройка

### <a name="get-your-language-understanding-luis-runtime-key"></a>Получение ключа среды выполнения LUIS

[Ключ среды выполнения](../luis-how-to-azure-subscription.md) можно получить, создав ресурс среды выполнения LUIS. Сохраните ключ и конечную точку ключа для следующего шага.

### <a name="create-a-new-javascript-nodejs-file"></a>Создание файла JavaScript (Node.js)

Создайте файл JavaScript с именем `luis_prediction.js` в предпочитаемой интегрированной среде разработки или редакторе.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Установка библиотеки NPM для среды выполнения LUIS

В каталоге приложения установите зависимости с помощью следующей команды:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Объектная модель

Клиент для разработки LUIS является объектом [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest), который проходит проверку подлинности в Azure и содержит ваш ключ разработки.

После создания используйте клиент для доступа к функциям, включая:

* [Прогнозирование](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) по слоту `staging` или `production`
* [Прогнозирование по версии](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки для среды выполнения прогнозирования LUIS:

* [Прогнозирование по слоту](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Добавление зависимостей

В каталоге проекта откройте файл `luis_prediction.js` в предпочитаемом редакторе или интегрированной среде разработки. Добавьте следующие зависимости.

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

1. Создайте переменные для собственных обязательных сведений LUIS:

    Добавьте переменные для управления ключом прогнозирования и ключом конечной точки. 
    
    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Variables)]

1. Создайте переменную `LUIS_APP_ID` для идентификатора приложения. Задайте для нее общедоступное приложение Интернета вещей, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Создайте переменную, чтобы задать опубликованный слот `production`.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=OtherVariables)]


1. Создайте объект msRest.ApiKeyCredentials с помощью ключа и используйте его со своей конечной точкой, чтобы создать объект [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest).

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Получение прогноза из среды выполнения

Добавьте следующий метод, который создает запрос к среде выполнения прогнозирования.

Речевой фрагмент пользователя является частью объекта [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest).

Метод **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** принимает для выполнения запроса несколько обязательных параметров, в том числе идентификатор приложения, имя слота и объект запроса прогнозирования. Есть еще несколько необязательных параметров, например для режимов подробного протоколирования, отображения всех намерений и ведения журнала.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Основной код для прогнозирования

Используйте следующий метод Main, чтобы скомбинировать переменные и методы для получения прогноза.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с помощью команды `node luis_prediction.js` из каталога приложения.

```console
node luis_prediction.js
```

Результат прогноза возвращает объект JSON:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с прогнозированием очистите результаты, полученные при выполнении этого краткого руководства, удалив файл и все подкаталоги.
