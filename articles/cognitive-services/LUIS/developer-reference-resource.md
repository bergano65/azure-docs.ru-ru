---
title: Ресурсы для разработчиков — Language Understanding
titleSuffix: Azure Cognitive Services
description: У разработчиков есть как интерфейсы API, так и пакеты SDK для Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 8accac7fe6068007180403fdab27013da161b28c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437188"
---
# <a name="developer-resources-for-language-understanding"></a>Ресурсы для разработчиков Language Understanding

Разработчики могут использовать интерфейсы API и пакеты SDK для Language Understanding. 

## <a name="azure-resource-management"></a>Управление ресурсами Azure

Используйте уровень управления Cognitive Services Azure, чтобы создать, изменить, перечислить и удалить ресурс службы Language Understanding или "перекрытие".

Найдите справочную документацию на основе средства:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding создания и прогнозирования запросов

Доступ к Language Understandingной службе осуществляется из ресурса Azure, который необходимо создать. Существует два ресурса: ресурсы конечной точки создания и прогнозирования. Оба этих ресурса позволяют управлять ресурсами LUIS. 

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

### <a name="rest-apis"></a>API-интерфейсы REST

Интерфейсы API для конечных точек создания и прогнозирования доступны в API-интерфейсах:

* [Справочная документация по](https://go.microsoft.com/fwlink/?linkid=2092087) разработке
* [Справочная документация по](https://go.microsoft.com/fwlink/?linkid=2092356) прогнозной среде выполнения

### <a name="language-based-sdks"></a>Пакеты SDK на основе языка

|Язык |Справочная документация|Package|Примеры|Краткие руководства|
|--|--|--|--|--|
|C#|[Разработка](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Прогнозирование](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Создание NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Прогнозирование NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Примеры пакета SDK для .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Создание приложения и управление им](sdk-csharp-quickstart-authoring-app.md)<br>[Конечная точка прогнозирования запросов](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[Создание и прогнозирование](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Пакет SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Разработка с помощью функции "ОСТАВШАЯся"](luis-get-started-go-add-utterance.md)<br>[Прогнозирование с помощью функции RESTFUL](luis-get-started-go-get-intent.md)|
|Java:|[Создание и прогнозирование](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Создание Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Прогноз Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Разработка](luis-get-started-java-add-utterance.md)<br>[Прогнозирование](luis-get-started-java-get-intent.md)
|Node.js|[Разработка](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Прогнозирование](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Создание NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[Прогноз NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Разработка с помощью функции "ОСТАВШАЯся"](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[Прогнозирование с помощью функции RESTFUL](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Создание и прогнозирование](sdk-python-quickstart-authoring-app.md)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Разработка](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Разработка](sdk-python-quickstart-authoring-app.md)<br>[Прогнозирование с помощью функции RESTFUL](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>Другие средства и пакеты SDK

Платформа Bot доступна в виде [пакета SDK](https://github.com/Microsoft/botframework) на различных языках и в качестве службы с помощью [службы Azure Bot](https://dev.botframework.com/). 

Платформа Bot предоставляет [несколько средств](https://github.com/microsoft/botbuilder-tools) , помогающих Language Understanding, в том числе:

* [Лудовн](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) . Создание Luis языка с помощью файлов Markdown
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) . создание приложений Luis.AI и управление ими
* [Диспетчеризация](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)— Управление родительскими и дочерними приложениями
* [Луисжен](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) — автоматическое создание резервных C#классов/типескрипт для целей и сущностей Luis.
* [Эмулятор Bot](https://github.com/Microsoft/BotFramework-Emulator/releases) — классическое приложение, позволяющее Bot-разработчикам тестировать и отлаживать программы-роботы, созданные с помощью пакета SDK для Bot Framework.


## <a name="next-steps"></a>Дальнейшие действия

Сведения об общих [кодах ошибок HTTP](luis-reference-response-codes.md)