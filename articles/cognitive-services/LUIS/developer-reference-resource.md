---
title: Ресурсы для разработчиков — Language Understanding
description: Пакеты SDK, API-интерфейсы RESTFUL, CLI, помогают разрабатывать приложения Language Understanding (LUIS) на языке программирования. Управляйте ресурсами Azure и прогнозами LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/19/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e4e43ead957bb6feca09fc8b2a8c16e37821c9b4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327176"
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

### <a name="rest-specifications"></a>Спецификации RESTFUL

[Спецификации Luis RESTful](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), а также все [спецификации Azure для других](https://github.com/Azure/azure-rest-api-specs)пользователей доступны в GitHub.

### <a name="rest-apis"></a>Интерфейсы REST API

Интерфейсы API для конечных точек создания и прогнозирования доступны в API-интерфейсах:

|Тип|Версия|
|--|--|
|Разработка|[Шаблон](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Предварительный просмотр v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Прогнозирование|[Шаблон](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Том](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Конечные точки RESTFUL

В настоящее время в LUIS есть 2 типа конечных точек:

* **Разработка** в конечной точке обучения
* **Прогнозирование** запросов в конечной точке среды выполнения.

|Назначение|URL-адрес|
|--|--|
|Создание версии 2 для конечной точки обучения|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|3. Разработка для конечной точки обучения|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|Прогнозирование v2 — все прогнозы на конечной точке среды выполнения|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 прогноз — прогнозирование версий на конечной точке времени выполнения|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|3 Прогноз — прогнозирование слотов на конечной точке времени выполнения|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

В следующей таблице описаны параметры, обозначенные фигурными скобками `{}` в предыдущей таблице.

|Параметр|Назначение|
|--|--|
|`your-resource-name`|Имя ресурса Azure|
|`q` либо `query`|Текст фразы, отправляемый из клиентского приложения, такого как чат-бот|
|`version`|имя версии с 10 символами|
|`slot`| `production` либо `staging`|

### <a name="rest-query-string-parameters"></a>Параметры строки запроса RESTFUL

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Схема приложения

[Схема приложения](app-schema-definition.md) импортируется и экспортируется в `.json` формате или `.lu` .

### <a name="language-based-sdks"></a>Пакеты SDK на основе языка

|Язык |Справочная документация|Пакет|Краткие руководства|
|--|--|--|--|
|C#|[Работы](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Прогнозирование](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Создание NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Прогнозирование NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Работы](azure-sdk-quickstart.md?pivots=programming-language-csharp)<br>[Прогнозирование запросов](azure-sdk-quickstart.md?pivots=programming-language-csharp)|
|Go|[Создание и прогнозирование](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Пакет SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Создание и прогнозирование](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Создание Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Прогноз Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Работы](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Прогнозирование](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Создание NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Прогноз NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Работы](azure-sdk-quickstart.md?pivots=programming-language-javascript)<br>[Прогнозирование](azure-sdk-quickstart.md?pivots=programming-language-javascript)|
|Python|[Создание и прогнозирование](azure-sdk-quickstart.md?pivots=programming-language-python)|[PIP](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Работы](azure-sdk-quickstart.md?pivots=programming-language-python)<br>[Прогнозирование](azure-sdk-quickstart.md?pivots=programming-language-python)|


### <a name="containers"></a>Контейнеры

Language Understanding (LUIS) предоставляет [контейнер](luis-container-howto.md) для предоставления локальных и автономных версий приложения.

### <a name="export-and-import-formats"></a>Форматы экспорта и импорта

Language Understanding предоставляет возможность управлять приложением и его моделями в формате JSON, `.LU` формате ([лудовн](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) и сжатом пакете для контейнера Language Understanding.

Импорт и экспорт этих форматов можно получить из API-интерфейсов и с портала LUIS. Портал предоставляет импорт и экспорт как часть списка приложений и версий.

## <a name="workshops"></a>Семинары

* GitHub: (семинар) [общение — AI: НЛУ с помощью Luis](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>средства непрерывной интеграции;

* GitHub: (Предварительная версия) [Разработка приложения Luis с использованием методик DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [НЛУ. DevOps](https://github.com/microsoft/NLU.DevOps) — средства, поддерживающие непрерывную интеграцию и развертывание для служб НЛУ.

## <a name="bot-framework-tools"></a>Инструменты платформы Bot

Платформа Bot доступна в виде [пакета SDK](https://github.com/Microsoft/botframework) на различных языках и в качестве службы с помощью [службы Azure Bot](https://dev.botframework.com/).

Платформа Bot предоставляет [несколько средств](https://github.com/microsoft/botbuilder-tools) , помогающих Language Understanding, в том числе:

* [Лудовн](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) . Создание Luis языка с помощью файлов Markdown
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) . создание приложений Luis.AI и управление ими
* [Диспетчеризация](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)— Управление родительскими и дочерними приложениями
* [Луисжен](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) — автоматическое создание резервных копий классов/типескрипт C# для целей Luis и сущностей.
* [Эмулятор Bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) — классическое приложение, позволяющее Bot-разработчикам тестировать и отлаживать программы-роботы, созданные с помощью пакета SDK для Bot Framework.
* [Композитор Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) — интегрированное средство разработки для разработчиков и групп могут, предназначенное для создания программы-роботы и общения с помощью платформы Microsoft Bot Framework
* [Примеры для платформы Bot](https://github.com/microsoft/botbuilder-samples) в #C, JavaScript, TypeScript и Python
## <a name="next-steps"></a>Дальнейшие действия

* Сведения об общих [кодах ошибок HTTP](luis-reference-response-codes.md)
* [Справочная документация](https://docs.microsoft.com/azure/index) по всем API и пакетам SDK
* [Платформа Bot](https://github.com/Microsoft/botbuilder-dotnet) и [служба Azure Bot](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Восприятные контейнеры](../cognitive-services-container-support.md)
