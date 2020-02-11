---
title: Ресурсы для разработчиков — Language Understanding
description: Пакеты SDK, API-интерфейсы RESTFUL, CLI, помогают разрабатывать приложения Language Understanding (LUIS) на языке программирования. Управляйте ресурсами Azure и прогнозами LUIS.
ms.topic: reference
ms.date: 02/09/2020
ms.openlocfilehash: ed869b7022e43b8ecf8c1f05bb3c6f0919076818
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119976"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Ресурсы для разработчиков SDK, RESTFUL и CLI для Language Understanding (LUIS)

Пакеты SDK, API-интерфейсы RESTFUL, CLI, помогают разрабатывать приложения Language Understanding (LUIS) на языке программирования. Управляйте ресурсами Azure и прогнозами LUIS. 

## <a name="azure-resource-management"></a>Управление ресурсами Azure

Используйте уровень управления Cognitive Services Azure, чтобы создать, изменить, перечислить и удалить ресурс службы Language Understanding или "перекрытие".

Найдите справочную документацию на основе средства:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding создания и прогнозирования запросов

Доступ к Language Understandingной службе осуществляется из ресурса Azure, который необходимо создать. Существует два ресурса:

* Используйте ресурс **разработки** для обучения, чтобы создавать, изменять, обучать и публиковать их.
* Используйте **Прогноз** для среды выполнения для отправки текста пользователя и получения прогноза.

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

Используйте [Cognitive Services пример кода](https://github.com/Azure-Samples/cognitive-services-quickstart-code) для изучения и использования наиболее распространенных задач.

### <a name="rest-apis"></a>Интерфейсы REST API

Интерфейсы API для конечных точек создания и прогнозирования доступны в API-интерфейсах:

|Тип|Версия|
|--|--|
|Разработка|[Шаблон](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Предварительный просмотр v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Прогнозирование|[Шаблон](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Том](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>Пакеты SDK на основе языка

|Язык |Справочная документация|Пакет|Примеры|Краткие руководства|
|--|--|--|--|--|
|C#|[Разработка](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Прогнозирование](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Создание NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Прогнозирование NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Примеры пакета SDK для .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Создание приложения и управление им](sdk-authoring.md?pivots=programming-language-csharp)<br>[Конечная точка прогнозирования запросов](sdk-query-prediction-endpoint.md)|
|Go|[Создание и прогнозирование](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Пакет SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Создание и прогнозирование с помощью функции "ОСТАВШАЯся"](luis-get-started-get-intent-from-rest.md)|
|Java|[Создание и прогнозирование](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Создание Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Прогноз Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Создание и прогнозирование](luis-get-started-get-intent-from-rest.md)
|Node.js|[Разработка](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Прогнозирование](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Создание NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Прогноз NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Разработка](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Прогнозирование](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Создание и прогнозирование с помощью функции "ОСТАВШАЯся"](luis-get-started-get-intent-from-rest.md)|
|Python|[Создание и прогнозирование](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Разработка](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Разработка](sdk-authoring.md?pivots=programming-language-python)<br>[Прогнозирование с помощью функции RESTFUL](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Контейнеры

Language Understanding (LUIS) предоставляет [контейнер](luis-container-howto.md) для предоставления локальных и автономных версий приложения.

### <a name="export-and-import-formats"></a>Форматы экспорта и импорта

Language Understanding предоставляет возможность управлять приложением и его моделями в формате JSON, форматом `.LU` ([лудовн](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) и сжатым пакетом для контейнера Language Understanding.

Импорт и экспорт этих форматов можно получить из API-интерфейсов и с портала LUIS. Портал предоставляет импорт и экспорт как часть списка приложений и версий.

## <a name="other-tools-and-sdks"></a>Другие средства и пакеты SDK

Платформа Bot доступна в виде [пакета SDK](https://github.com/Microsoft/botframework) на различных языках и в качестве службы с помощью [службы Azure Bot](https://dev.botframework.com/).

Платформа Bot предоставляет [несколько средств](https://github.com/microsoft/botbuilder-tools) , помогающих Language Understanding, в том числе:

* [Лудовн](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) . Создание Luis языка с помощью файлов Markdown
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) . создание приложений Luis.AI и управление ими
* [Диспетчеризация](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)— Управление родительскими и дочерними приложениями
* [Луисжен](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) — автоматическое создание резервных C#классов/типескрипт для целей и сущностей Luis.
* [Эмулятор Bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) — классическое приложение, позволяющее Bot-разработчикам тестировать и отлаживать программы-роботы, созданные с помощью пакета SDK для Bot Framework.


## <a name="next-steps"></a>Дальнейшие действия

* Сведения об общих [кодах ошибок HTTP](luis-reference-response-codes.md)
* [Справочная документация](https://docs.microsoft.com/azure/index#pivot=sdkstools) по всем API и пакетам SDK
* [Платформа Bot](https://github.com/Microsoft/botbuilder-dotnet) и [служба Azure Bot](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Восприятные контейнеры](../cognitive-services-container-support.md)
