---
title: Краткое руководство. Клиентская библиотека QnA Maker для .NET
description: В этом кратком руководстве показано, как начать работу с клиентской библиотекой QnA Maker для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.  QnA Maker представляет собой службу для отправки вопросов и получения ответов из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов и руководств по продуктам.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 0dfabfa1357543e4011412c0a9f63061e8856019
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507386"
---
Клиентская библиотека QnA Maker для .NET:

 * Создание базы знаний
 * Обновление базы знаний
 * Публикация базы знаний
 * Получение ключа конечной точки среды выполнения прогнозирования
 * Ожидание долго выполняющейся задачи
 * Скачивание базы знаний
 * Получение ответов
 * Удаление базы знаний

[Справочная документация](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [Образцы кода (C#)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [IDE Visual Studio](https://visualstudio.microsoft.com/vs/) или текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Получив подписку Azure, создайте [ресурс QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) на портале Azure, чтобы получить ключ разработки и имя ресурса. После развертывания ресурса выберите элемент **Перейти к ресурсу**.
    * Для подключения приложения к API QnA Maker потребуется ключ и имя созданного ресурса. Ключ и имя ресурса вы вставите в приведенный ниже код на следующих шагах в рамках этого краткого руководства.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

#### <a name="visual-studio-ide"></a>[Интегрированная среда разработки Visual Studio](#tab/visual-studio)

С помощью Visual Studio создайте приложение .NET Core и установите клиентскую библиотеку, щелкнув правой кнопкой мыши решение в **Обозревателе решений** и выбрав **Управление пакетами NuGet**. В открывшемся диспетчере пакетов выберите **Просмотр**, установите флажок **Включить предварительные версии** и выполните поиск по запросу `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`. Выберите версию `2.0.0-preview.1`, а затем **Установить**.

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
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.0-preview.1
```


---

> [!TIP]
> Хотите просмотреть готовый файл с кодом для этого краткого руководства? Его можно найти [на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), где размещены примеры кода для этого краткого руководства.

В каталоге проекта откройте файл *program.cs* и с помощью `using` добавьте следующие директивы:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

Чтобы использовать общие задачи из этого краткого руководства, в методе `Main` приложения добавьте переменные и код из приведенного ниже раздела.

> [!IMPORTANT]
> Перейдите на портал Azure и найдите ключ и конечную точку для ресурса QnA Maker, созданного в рамках выполнения предварительных требований. Они находятся на странице ресурса **Ключ и конечная точка** в разделе **Управление ресурсами**.
> Для создания базы знаний необходим весь ключ. Необходимо только имя ресурса из конечной точки. Формат — `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Не забудьте удалить ключ из кода, когда закончите, и никогда не публикуйте его в открытом доступе. Для рабочей среды рекомендуется использовать безопасный способ хранения и доступа к учетным данным. Например, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) предоставляет безопасное хранилище ключей.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


## <a name="object-models"></a>Объектные модели

QnA Maker использует две различные объектные модели.
* **[QnAMakerClient](#qnamakerclient-object-model)**  — это объект для создания, публикации и скачивания базы знаний, а также управления ею.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)**  — это объект для отправки запроса в базу знаний с помощью API GenerateAnswer и новых предложенных вопросов с помощью API обучения (в рамках [активного обучения](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Объектная модель QnAMakerClient

Клиент разработки QnA Maker — это объект [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, содержащего ваш ключ.

После создания клиента используйте [свойство QnAMakerClient.Knowledgebase](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) для создания и публикации своей базы знаний, а также управления ею.

Управляйте своей базой знаний, отправляя объект JSON. Для немедленных операций метод обычно возвращает объект JSON, указав статус. Ответ для длительных операций — ИД операции. Вызовите [метод OperationsExtensions.GetDetailsAsync(IOperations, String, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) с ИД операции, чтобы определить [класс OperationStateType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).

### <a name="qnamakerruntimeclient-object-model"></a>Объектная модель QnAMakerRuntimeClient

Клиент прогнозирования QnA Maker — это объект [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, содержащего ключ среды выполнения прогнозирования, возвращенный из вызова клиента разработки `client.EndpointKeys.GetKeys` после публикации базы знаний.

Метод [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) используется для получения ответа от среды выполнения запроса.

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



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Проверка подлинности клиента для создания базы знаний

Создайте экземпляр клиентского объекта с использованием ключа. С помощью этого экземпляра и ресурса сформируйте конечную точку. Применяя эту конечную точку и ключ, создайте [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet). Создайте объект [ServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet).

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Создание базы знаний

База знаний хранит пары вопросов и ответов для объекта [Класс CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) из трех источников:

* Для **редакционного содержимого** используйте объект [Класс QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet).
    * Чтобы использовать метаданные и дальнейшие запросы, используйте редакционный контекст, так как эти данные добавляются на уровне отдельных пар вопросов и ответов.
* Для **файлов** используйте объект [Класс FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet). FileDTO включает имя файла, а также общедоступный URL-адрес для доступа к этому файлу.
* В качестве значения для параметра **URL-адреса** используйте список строк, представляющих общедоступные URL-адреса.

На этапе создания также задаются свойства базы знаний:
* `defaultAnswerUsedForExtraction` — то, что возвращается, если ответ не найден;
* `enableHierarchicalExtraction` — автоматическое создание связей запросов между извлеченными парами вопросов и ответов;
* `language` — при создании первой базы данных ресурса задайте язык для использования в индексе Поиска Azure.

Вызовите метод[KnowledgebaseExtensions.CreateAsync(IKnowledgebase, CreateKbDTO, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet), а затем передайте возвращенный ИД операции методу [Get status of an operation](#get-status-of-an-operation) (Получение статуса операции) для опроса состояния.

Последняя строка следующего кода возвращает ИД базы знаний из ответа от MonitorOoperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=31)]

Чтобы создать базу знаний, добавьте функцию [`MonitorOperation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="update-a-knowledge-base"></a>Обновление базы знаний

Базу знаний можно обновить, передав ИД базы знаний и объекты DTO [Класс UpdateKbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet), содержащий [класс UpdateKbOperationDTOAdd](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [класс UpdateKbOperationDTOUpdate](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) и [класс UpdateKbOperationDTODelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) в метод [KnowledgebaseExtensions.UpdateAsync(IKnowledgebase, String, UpdateKbOperationDTO, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet). Используйте метод [Get status of an operation](#get-status-of-an-operation) (Получение состояния операции), чтобы определить, успешно ли выполнено обновление.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Чтобы обновить базу знаний, добавьте функцию [`MonitorOperation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="download-a-knowledge-base"></a>Скачивание базы знаний

Используйте метод [KnowledgebaseExtensions.DownloadAsync(IKnowledgebase, String, String, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet), чтобы загрузить базу данных в виде списка [Класс QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Это _не_ эквивалентно экспорту портала QnA Maker со страницы **настроек**, так как результатом этого метода не является файл.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3)]

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Опубликуйте базу знаний, используя метод [KnowledgebaseExtensions.PublishAsync(IKnowledgebase, String, CancellationToken)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet). При этом берется текущая сохраненная и обученная модель, на которую ссылается ИД базы знаний, и публикуется в конечной точке. Это необходимо для подачи запросов к базе знаний.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]



## <a name="get-query-runtime-key"></a>Получение ключа среды выполнения запроса

После публикации базы знаний необходимо получить ключ среды выполнения запроса, чтобы отправить в нее запрос. Этот ключ отличается о того, который использовался для создания исходного объекта клиента.

Используйте метод [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_), чтобы получить класс [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet).

Для запроса базы знаний используйте любое свойство ключа, возвращаемое в объекте.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

Ключ среды выполнения требуется для отправки запросов к базе знаний.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Проверка подлинности среды выполнения для создания ответа

Создайте [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet), чтобы отправить в базу знаний запрос для создания ответа или выполнять обучение по методу активного обучения.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

QnAMakerRuntimeClient позволяет:
* получать ответы из базы знаний;
* отправлять новые предложенные вопросы в базу знаний для [активного обучения](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Создание ответа из базы знаний

Создайте ответ от опубликованной базы знаний с использованием метода [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet). Этот метод принимает идентификатор базы знаний и [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Перейдите к дополнительным свойствам QueryDTO, таким как [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) и [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet), для использования в вашем чат-боте.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

Это простой пример отправки запроса в базу знаний. Чтобы ознакомиться с расширенными сценариями запросов, см. [другие примеры запросов](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

Удалите базу знаний, используя метод [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) с параметром ИД базы знаний.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Некоторые методы, такие как "create" и "update", могут занять достаточно времени, чтобы вместо ожидания завершения процесса был возвращен [класс операции](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet). Используйте [свойство Operation.OperationId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) из операции для опроса (с логикой повторных попыток), чтобы определить состояние исходного метода.

_loop_ и _Task.Delay_ в следующем блоке кода используются для имитации логики повторных попыток. Они должны быть заменены собственной логикой повторных попыток.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с помощью команды `dotnet run` из каталога приложения.

```dotnetcli
dotnet run
```

Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).
