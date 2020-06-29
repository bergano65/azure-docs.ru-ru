---
title: Краткое руководство. Клиентская библиотека QnA Maker для .NET
description: В этом кратком руководстве показано, как начать работу с клиентской библиотекой QnA Maker для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.  QnA Maker представляет собой службу для отправки вопросов и получения ответов из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов и руководств по продуктам.
ms.topic: quickstart
ms.date: 06/11/2020
ms.openlocfilehash: e487783e506d16006231b07b9a86f93864f51903
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84765151"
---
Клиентская библиотека QnA Maker для .NET:

 * Создание базы знаний
 * Обновление базы знаний
 * Публикация базы знаний, ожидание завершения публикации
 * Получение ключа конечной точки среды выполнения прогнозирования
 * Скачивание базы знаний
 * Удаление базы знаний

[Справочная документация](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [Образцы кода (C#)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [IDE Visual Studio](https://visualstudio.microsoft.com/vs/) или текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Получив подписку Azure, создайте [ресурс QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) на портале Azure, чтобы получить ключ разработки и конечную точку. После развертывания ресурса выберите элемент **Перейти к ресурсу**.
    * Для подключения приложения к API QnA Maker потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка


#### <a name="visual-studio-ide"></a>[Интегрированная среда разработки Visual Studio](#tab/visual-studio)

С помощью Visual Studio создайте приложение .NET Core и установите клиентскую библиотеку, щелкнув правой кнопкой мыши решение в **Обозревателе решений** и выбрав **Управление пакетами NuGet**. В открывшемся диспетчере пакетов выберите **Просмотр**, установите флажок **Включить предварительные версии** и выполните поиск по запросу `(package-name)`. Выберите версию `(version)`, а затем **Установить**.

#### <a name="cli"></a>[CLI](#tab/cli)

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `qna-maker-quickstart`. Эта команда создает простой проект "Hello World" на языке C# с одним файлом исходного кода: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```console
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

В каталоге приложения установите клиентскую библиотеку QnA Maker для .NET с помощью следующей команды:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.1.0
```


---

> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), где размещены примеры кода для этого краткого руководства.

В каталоге проекта откройте файл *program.cs* и с помощью `using` добавьте следующие директивы:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

Чтобы использовать общие задачи из этого краткого руководства, в методе `Main` приложения добавьте переменные и код из следующих разделов.

## <a name="object-models"></a>Объектные модели

QnA Maker использует две различные объектные модели.
* **[QnAMakerClient](#qnamakerclient-object-model)**  — это объект для создания, публикации и скачивания базы знаний, а также управления ею.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)**  — это объект для отправки запроса в базу знаний с помощью API GenerateAnswer и новых предложенных вопросов с помощью API обучения (в рамках [активного обучения](../concepts/active-learning-suggestions.md)).


### <a name="qnamakerclient-object-model"></a>Объектная модель QnAMakerClient

Клиент разработки QnA Maker — это объект [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, содержащего ваш ключ.

После создания клиента используйте [свойство QnAMakerClient.Knowledgebase](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) для создания и публикации своей базы знаний, а также управления ею.

Управляйте своей базой знаний, отправляя объект JSON. Для немедленных операций метод обычно возвращает объект JSON, указав статус. Ответ для длительных операций — ИД операции. Вызовите [метод OperationsExtensions.GetDetailsAsync(IOperations, String, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) с ИД операции, чтобы определить [класс OperationStateType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).

### <a name="qnamakerruntimeclient-object-model"></a>Объектная модель QnAMakerRuntimeClient

Клиент прогнозирования QnA Maker — это объект [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, содержащего ключ среды выполнения прогнозирования, возвращенный из вызова клиента разработки `client.EndpointKeys.GetKeys` после публикации базы знаний.

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки QnA Maker для .NET:

* [Проверка подлинности клиента разработки](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Создание базы знаний](#create-a-knowledge-base)
* [Обновление базы знаний](#update-a-knowledge-base)
* [Скачивание базы знаний](#download-a-knowledge-base)
* [Публикация базы знаний](#publish-a-knowledge-base)
* [Удаление базы знаний](#delete-a-knowledge-base)
* [Получение ключа среды выполнения запроса](#get-query-runtime-key)
* [Получение состояния операции](#get-status-of-an-operation)
* [Проверка подлинности клиента среды выполнения запросов](#authenticate-the-runtime-for-generating-an-answer)
* [Создание ответа из базы знаний](#generate-an-answer-from-the-knowledge-base)

## <a name="using-this-example-knowledge-base"></a>Использование этого примера базы знаний

База знаний, используемая в этом кратком руководстве, начинается с двух пар разговорных вопросов и ответов. Это сделано, чтобы упростить пример и обеспечить строго предсказуемые идентификаторы, используемые в методе Update, который сопоставляет дальнейшие запросы, содержащие вопросы, с новыми парами. Такой подход был запланирован и реализован в определенном порядке для целей данного краткого руководства.

Если вы планируете со временем разработать базу знаний с дальнейшими запросами, которые зависят от имеющихся пар вопросов и ответов, вы можете воспользоваться одним из следующих вариантов.
* Для управления более крупными базами знаний следует воспользоваться текстовым редактором или поддерживающим автоматизацию средством TSV, а затем полностью заменить всю базу знаний обновленной версией.
* Для небольших баз знаний дальнейшими запросами можно полностью управлять на портале QnA Maker.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Проверка подлинности клиента для создания базы знаний

В методе **main** создайте переменную для ключа Azure своего ресурса и задайте имя этого ресурса. В URL-адресах для разработки и прогнозирования имя ресурса используется в качестве поддомена.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


> [!IMPORTANT]
> Перейдите на портал Azure и найдите ключ и конечную точку для ресурса QnA Maker, созданного в рамках выполнения предварительных требований. Они находятся на странице ресурса **Ключ и конечная точка** в разделе **Управление ресурсами**.
> Для создания базы знаний необходим весь ключ. Необходимо только имя ресурса из конечной точки. Формат: ``.
> Не забудьте удалить ключ из кода, когда закончите, и никогда не публикуйте его в открытом доступе. Для рабочей среды рекомендуется использовать безопасный способ хранения и доступа к учетным данным. Например, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) предоставляет безопасное хранилище ключей.

Теперь создайте объект [Класс ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) с помощью ключа и используйте его со своей конечной точкой, чтобы создать объект [Класс QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet).

|переменная|Пример|
|--|--|
|`authoringKey`|Ключ — это строка из 32 символов, доступная на странице **Keys and endpoints** (Ключи и конечные точки) ресурса QnA Maker на портале Azure. Он отличается от ключа среды выполнения прогнозирования.|
|`resourceName`| Имя ресурса используется в формате `https://{resourceName}.cognitiveservices.azure.com`. Это не тот же URL-адрес, который используется для запроса среды выполнения прогнозирования.|
||||

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Создание базы знаний

База знаний хранит пары вопросов и ответов для объекта [Класс CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) из трех источников:

* Для **редакционного содержимого** используйте объект [Класс QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet).
    * Чтобы использовать метаданные и дальнейшие запросы, используйте редакционный контекст, так как эти данные добавляются на уровне отдельных пар вопросов и ответов.
* Для **файлов** используйте объект [Класс FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet).
* Для **URL-адресов** используйте список строк.

Вызовите метод[KnowledgebaseExtensions.CreateAsync(IKnowledgebase, CreateKbDTO, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet), а затем передайте возвращенный ИД операции методу [Get status of an operation](#get-status-of-an-operation) (Получение статуса операции) для опроса состояния.

Последняя строка следующего кода возвращает ИД базы знаний из ответа от MonitorOoperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=37-38)]

Чтобы создать базу знаний, добавьте функцию [`MonitorOperation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="update-a-knowledge-base"></a>Обновление базы знаний

Базу знаний можно обновить, передав ИД базы знаний и объекты DTO [Класс UpdateKbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet), содержащий [класс UpdateKbOperationDTOAdd](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [класс UpdateKbOperationDTOUpdate](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) и [класс UpdateKbOperationDTODelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) в метод [KnowledgebaseExtensions.UpdateAsync(IKnowledgebase, String, UpdateKbOperationDTO, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet). Используйте метод [Get status of an operation](#get-status-of-an-operation) (Получение состояния операции), чтобы определить, успешно ли выполнено обновление.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Чтобы обновить базу знаний, добавьте функцию [`MonitorOperation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="download-a-knowledge-base"></a>Скачивание базы знаний

Используйте метод [KnowledgebaseExtensions.DownloadAsync(IKnowledgebase, String, String, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet), чтобы загрузить базу данных в виде списка [Класс QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Это _не_ эквивалентно экспорту портала QnA Maker со страницы **настроек**, поскольку результатом этого метода не является файл TSV.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3,4)]

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Опубликуйте базу знаний, используя метод [KnowledgebaseExtensions.PublishAsync(IKnowledgebase, String, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet). При этом берется текущая сохраненная и обученная модель, на которую ссылается ИД базы знаний, и публикуется в конечной точке.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]

## <a name="get-query-runtime-key"></a>Получение ключа среды выполнения запроса

После публикации базы знаний необходимо получить ключ среды выполнения запроса, чтобы отправить в нее запрос. Этот ключ отличается о того, который использовался для создания исходного объекта клиента.

Используйте метод [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_), чтобы получить класс [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet).

Для запроса базы знаний используйте любое свойство ключа, возвращаемое в объекте.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Проверка подлинности среды выполнения для создания ответа

Создайте [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet), чтобы отправить в базу знаний запрос для создания ответа или выполнять обучение по методу активного обучения.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Используйте QnAMakerRuntimeClient, чтобы получить ответ от базы знаний или отправить в нее новые предложенные вопросы для [активного обучения](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Создание ответа из базы знаний

Создайте ответ из опубликованной базы знаний с использованием метода [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet). Этот метод принимает идентификатор базы знаний и [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Перейдите к дополнительным свойствам QueryDTO, таким как [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) и [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet), для использования в вашем чат-боте.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

Это простой пример отправки запроса в базу знаний. Чтобы ознакомиться с расширенными сценариями запросов, см. [другие примеры запросов](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

Удалите базу знаний, используя метод [KnowledgebaseExtensions.DeleteAsync(IKnowledgebase, String, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) с параметром ИД базы знаний.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Некоторые методы, такие как "create" и "update", могут занять достаточно времени, чтобы вместо ожидания завершения процесса был возвращен [класс операции](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet). Используйте [свойство Operation.OperationId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) из операции для опроса (с логикой повторных попыток), чтобы определить состояние исходного метода.

_loop_ и _Task.Delay_ в следующем блоке кода используются для имитации логики повторных попыток. Они должны быть заменены собственной логикой повторных попыток.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с помощью команды `dotnet run` из каталога приложения.

Все фрагменты кода из этой статьи [доступны для скачивания](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) и могут выполняться как один файл.

```dotnetcli
dotnet run
```

* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).