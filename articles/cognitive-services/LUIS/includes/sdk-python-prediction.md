---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: ff4c33aea3d3ce604f44c38e6e3856242388b0e9
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372390"
---
С помощью клиентской библиотеки прогнозирования для Python в службе "Распознавание речи" (LUIS) можно выполнить следующие задачи.

* Получение прогноза по слоту
* Получение прогноза по версии

[Справочная документация](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Пакет для прогнозирования (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Образцы кода (C#)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Предварительные требования

* Учетную запись на портале LUIS [можно создать бесплатно](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Настройка

### <a name="get-your-language-understanding-luis-runtime-key"></a>Получение ключа среды выполнения LUIS

[Ключ среды выполнения](../luis-how-to-azure-subscription.md) можно получить, создав ресурс среды выполнения LUIS. Сохраните ключ и конечную точку ключа для следующего шага.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Создание файла Python

Создайте файл Python в предпочитаемой интегрированной среде разработки или редакторе, с именем `prediction_quickstart.py`.

### <a name="install-the-sdk"></a>Установка пакета SDK

В каталоге приложения установите клиентскую библиотеку для среды выполнения прогнозирования LUIS для Python с помощью следующей команды:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Объектная модель

Клиент для среды выполнения прогнозирования LUIS представляет собой объект [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python), который проходит проверку подлинности в Azure и содержит ваш ключ ресурса.

После создания используйте клиент для доступа к функциям, включая:

* Прогнозирование по [слоту промежуточного хранения или рабочему слоту](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Прогнозирование по [версии](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки для среды выполнения прогнозирования LUIS для Python:

* [Прогнозирование по слоту](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Добавление зависимостей

В каталоге проекта откройте файл `prediction_quickstart.py` в предпочитаемом редакторе или интегрированной среде разработки. Добавьте следующие зависимости.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

1. Создайте переменные для собственных обязательных сведений LUIS:

    Добавьте переменные для управления вашим ключом прогнозирования, извлеченным из переменной среды с именем `LUIS_RUNTIME_KEY`. Если вы создали переменную среды после запуска приложения, то для доступа к переменной следует закрыть и перезагрузить редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена. Метод будет создано позже.

    Создайте переменную для хранения имени ресурса `LUIS_RUNTIME_ENDPOINT`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Создайте переменную среды с именем `LUIS_APP_ID` для идентификатора приложения. Задайте для переменной среды значение публичного приложения Интернета вещей, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Создайте переменную, чтобы задать опубликованный слот `production`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Создайте объект с помощью ключа и используйте его со своей конечной точкой, чтобы создать объект [LUISRuntimeClientConfiguration]https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python().

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Получение прогноза из среды выполнения

Добавьте следующий метод, который создает запрос к среде выполнения прогнозирования.

Речевой фрагмент пользователя является частью объекта [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python).

Метод **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** принимает для выполнения запроса несколько обязательных параметров, в том числе идентификатор приложения, имя слота и объект запроса прогнозирования. Есть еще несколько необязательных параметров, например для режимов подробного протоколирования, отображения всех намерений и ведения журнала. Запрос возвратит объект [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python).

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Основной код для прогнозирования

Используйте следующий метод Main, чтобы скомбинировать переменные и методы для получения прогноза.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с помощью команды `python prediction_quickstart.py` из каталога приложения.

```console
python prediction_quickstart.py
```

В консоли быстрого запуска отображаются выходные данные:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с прогнозированием очистите результаты, полученные при выполнении этого краткого руководства, удалив файл и все подкаталоги.
