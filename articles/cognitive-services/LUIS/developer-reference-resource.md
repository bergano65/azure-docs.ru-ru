---
title: Ресурсы разработчиков - Понимание языка
description: SDK, REST AIS, CLI помогают вам разрабатывать приложения Language Understanding (LUIS) на вашем языке программирования. Управление ресурсами Azure и прогнозами LUIS.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457990"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Ресурсы разработчиков SDK, REST и CLI для понимания языка (LUIS)

SDK, REST AIS, CLI помогают вам разрабатывать приложения Language Understanding (LUIS) на вашем языке программирования. Управление ресурсами Azure и прогнозами LUIS.

## <a name="azure-resource-management"></a>Управление ресурсами Azure

Используйте слой управления когнитивными службами Azure для создания, отправления, списка и удаления ресурса Language Understanding или Cognitive Service.

Найти справочную документацию на основе инструмента:

* [Лазурный CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Запросы на авторство и прогнозирование понимания языка

Служба понимания языка доступна из ресурса Azure, который необходимо создать. Есть два ресурса:

* Используйте **ресурс авторов** для обучения для создания, отсвачения, обучения и публикации.
* Используйте **прогноз** для времени выполнения, чтобы отправить текст пользователя и получить прогноз.

Узнайте о [конечной точке прогнозирования V3.](luis-migration-api-v3.md)

Используйте [образец кода Cognitive Services](https://github.com/Azure-Samples/cognitive-services-quickstart-code) для изучения и использования наиболее распространенных задач.

### <a name="rest-specifications"></a>Спецификации REST

[Спецификации LUIS REST,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)а также все [спецификации Azure REST,](https://github.com/Azure/azure-rest-api-specs)доступны на GitHub.

### <a name="rest-apis"></a>Интерфейсы REST API

Как автор, так и конечная точка прогнозирования APIS доступны в аПОТ REST:

|Тип|Версия|
|--|--|
|Разработка|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[предварительный просмотр V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Прогнозирование|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST Endpoints

LUIS в настоящее время имеет 2 типа конечных точек:

* авторство на тренировочной точке
* прогнозирование запроса на точке запорного времени выполнения.

|Назначение|URL-адрес|
|--|--|
|Авторство на прыге кадров|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 Runtime - все прогнозы на конечную точку выполнения|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 Runtime - прогноз версий на точке запуска|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 Runtime - прогноз слота на точке запуска|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

В следующей таблице описаны параметры, обозначенные фигурными скобками `{}` в предыдущей таблице.

|Параметр|Назначение|
|--|--|
|`your-resource-name`|Имя ресурса Azure|
|`q` либо `query`|Текст фразы, отправляемый из клиентского приложения, такого как чат-бот|
|`version`|10 имя версии персонажа|
|`slot`| `production` либо `staging`|

### <a name="language-based-sdks"></a>Языковые SDK

|Язык |Справочная документация|Пакет|Примеры|Краткие руководства|
|--|--|--|--|--|
|C#|[Разработка](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Прогнозирование](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Авторство NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Прогнозирование NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[образцы SDK .Net](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Создание приложения и управление им](sdk-authoring.md?pivots=programming-language-csharp)<br>[Запрос конечной точки прогнозирования](sdk-query-prediction-endpoint.md)|
|Go|[Авторство и прогнозирование](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Пакет SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Авторство и прогнозирование с помощью REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Авторство и прогнозирование](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven авторство](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Предсказание Мавена](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Авторство и прогнозирование](luis-get-started-get-intent-from-rest.md)
|Node.js|[Разработка](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Прогнозирование](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Авторство NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Прогноз NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Авторство и прогнозирование с помощью REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Авторство и прогнозирование](sdk-authoring.md?pivots=programming-language-python)|[PIP](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Разработка](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Разработка](sdk-authoring.md?pivots=programming-language-python)<br>[Прогноз с помощью REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Контейнеры

Language Understanding (LUIS) предоставляет [контейнер](luis-container-howto.md) для предоставления на месте и содержащихся версий вашего приложения.

### <a name="export-and-import-formats"></a>Экспортные и импортные форматы

Language Understanding обеспечивает возможность управления приложением и его моделями в формате JSON, формате `.LU` ([LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)и сжатом пакете для контейнера Language Understanding.

Импорт и экспорт этих форматов можно получить на AA и на портале LUIS. Портал предоставляет импорт и экспорт в рамках списка приложений и версий.

## <a name="other-tools-and-sdks"></a>Другие инструменты и SDK

Платформа бота доступна как [SDK](https://github.com/Microsoft/botframework) на различных языках, так и как сервис с помощью [службы Azure Bot.](https://dev.botframework.com/)

Платформа Bot предоставляет [несколько инструментов,](https://github.com/microsoft/botbuilder-tools) помогая с пониманием языка, в том числе:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - Создайте модели понимания языка LUIS с помощью файлов разметки
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - Создание и управление приложениями LUIS.ai
* [Диспетчер -](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)управление родительскими и детскими приложениями
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - Авто генерирует резервные классы с итипскрипта для ваших намерений и сущностей LUIS.
* [Эмулятор Bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) - настольное приложение, которое позволяет разработчикам ботов тестировать и отлаживать ботов, построенных с помощью Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - интегрированный инструмент разработки для разработчиков и междисциплинарных команд для создания ботов и разговорного взаимодействия с Microsoft Bot Framework
* [microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) - Инструменты, поддерживающие непрерывную интеграцию и развертывание для служб NLU.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте об [общих кодах ошибок HTTP](luis-reference-response-codes.md)
* [Справочная документация](https://docs.microsoft.com/azure/index) для всех AA и SDK
* [Платформа Бота](https://github.com/Microsoft/botbuilder-dotnet) и [служба Лазурного Бота](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Когнитивные контейнеры](../cognitive-services-container-support.md)
