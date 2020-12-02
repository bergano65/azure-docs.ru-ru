---
title: Краткое руководство. Клиентская библиотека QnA Maker для Python
description: В этом кратком руководстве показано, как начать работу с клиентской библиотекой QnA Maker для Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 9746b2a1fdc82aac1e9ea28dd6f7551d61cb76f1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351063"
---
Клиентская библиотека QnA Maker для Python позволяет выполнить следующие задачи.

* Создание базы знаний
* Обновление базы знаний
* Публикация базы знаний
* Получение ключа конечной точки среды выполнения прогнозирования
* Ожидание долго выполняющейся задачи
* Скачивание базы знаний
* Получение ответов
* Удаление базы знаний

[Справочная документация](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Пакет (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Примеры Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* [Python 3.x](https://www.python.org/)
* Получив подписку Azure, создайте [ресурс QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) на портале Azure, чтобы получить ключ разработки и конечную точку. После развертывания ресурса выберите элемент **Перейти к ресурсу**.
    * Для подключения приложения к API QnA Maker потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

После установки Python вы можете установить клиентскую библиотеку с помощью следующей команды:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте файл Python с именем `quickstart-file.py` и импортируйте следующие библиотеки.

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies&highlight=4,5)]

Создайте переменные для конечной точки Azure и ключа ресурса.

> [!IMPORTANT]
> Перейдите на портал Azure и найдите ключ и конечную точку для ресурса QnA Maker, созданного в рамках выполнения предварительных требований. Они находятся на странице ресурса **Ключ и конечная точка** в разделе **Управление ресурсами**.
> Для создания базы знаний необходим весь ключ. Необходимо только имя ресурса из конечной точки. Формат — `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Не забудьте удалить ключ из кода, когда закончите, и никогда не публикуйте его в открытом доступе. Для рабочей среды рекомендуется использовать безопасный способ хранения и доступа к учетным данным. Например, [Azure Key Vault](../../../key-vault/general/overview.md) предоставляет безопасное хранилище ключей.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

## <a name="object-models"></a>Объектные модели

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) использует две различные объектные модели.
* **[QnAMakerClient](#qnamakerclient-object-model)**  — это объект для создания, публикации и скачивания базы знаний, а также управления ею.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)**  — это объект для отправки запроса в базу знаний с помощью API GenerateAnswer и новых предложенных вопросов с помощью API обучения (в рамках [активного обучения](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Объектная модель QnAMakerClient

Клиент разработки QnA Maker — это объект [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, содержащего ваш ключ.

После создания клиента используйте [свойство QnAMakerClient.Knowledgebase](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python) для создания и публикации своей базы знаний, а также управления ею.

Управляйте своей базой знаний, отправляя объект JSON. Для немедленных операций метод обычно возвращает объект JSON, указав статус. Ответ для длительных операций — ИД операции. Вызовите метод [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) с идентификатором операции, чтобы определить [состояние запроса](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python).

### <a name="qnamakerruntimeclient-object-model"></a>Объектная модель QnAMakerRuntimeClient

Клиент прогнозирования QnA Maker — это объект [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest), который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, содержащего ключ среды выполнения прогнозирования, возвращенный из вызова [client.EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python) к клиенту разработки после публикации базы знаний.

Метод [generate_answer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Runtime__ctor_Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerRuntimeClient_#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) используется для получения ответа от среды выполнения запроса.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Проверка подлинности клиента для создания базы знаний

Создайте экземпляр клиента с конечной точкой и ключом. Создайте объект CognitiveServicesCredentials с ключом и используйте его с конечной точкой для создания объекта [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python).

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Создание базы знаний

 Используйте объект клиента, чтобы получить объект [операций базы знаний](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python).

База знаний хранит пары вопросов и ответов для объекта [Класс CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.create_kb_dto) из трех источников:

* Для **редакционного содержимого** используйте объект [Класс QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python).
    * Чтобы использовать метаданные и дальнейшие запросы, используйте редакционный контекст, так как эти данные добавляются на уровне отдельных пар вопросов и ответов.
* Для **файлов** используйте объект [Класс FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.file_dto). FileDTO включает имя файла, а также общедоступный URL-адрес для доступа к этому файлу.
* В качестве значения для параметра **URL-адреса** используйте список строк, представляющих общедоступные URL-адреса.

Вызовите метод[Создать](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python), а затем передайте возвращенный идентификатор операции методу [Operations.getDetails](#get-status-of-an-operation) для опроса состояния.

Последняя строка следующего кода возвращает ИД базы знаний из ответа от MonitorOoperation.

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod&highlight=36,38)]

Чтобы создать базу знаний, добавьте функцию [`_monitor_operation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="update-a-knowledge-base"></a>Обновление базы знаний

Базу знаний можно обновить, передав идентификатор базы знаний и метод [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python), содержащий [добавить](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python), [обновить](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python) и [удалить](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) объекты DTO в метод [обновить](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-). Используйте метод [Operation.getDetail](#get-status-of-an-operation), чтобы определить, успешно ли выполнено обновление.

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod&highlight=68,69)]

Чтобы обновить базу знаний, добавьте функцию [`_monitor_operation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="download-a-knowledge-base"></a>Скачивание базы знаний

Используйте метод [загрузить](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-), чтобы загрузить базу данных в виде списка [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Это _не_ эквивалентно экспорту портала QnA Maker со страницы **настроек**, поскольку результатом этого метода не является файл TSV.

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Опубликуйте базу знаний, используя метод [опубликовать](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-). При этом берется текущая сохраненная и обученная модель, на которую ссылается ИД базы знаний, и публикуется ее в конечной точке.

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB&highlight=2)]

## <a name="get-query-runtime-key"></a>Получение ключа среды выполнения запроса

После публикации базы знаний необходимо получить ключ среды выполнения запроса, чтобы отправить в нее запрос. Этот ключ отличается о того, который использовался для создания исходного объекта клиента.

Используйте метод [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python), чтобы получить класс [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-python).

Для запроса к базе знаний используйте любое свойство ключа, возвращаемое в объекте.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey&highlight=2)]


## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Проверка подлинности среды выполнения для создания ответа

Создайте [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest), чтобы отправить в базу знаний запрос для создания ответа или выполнять обучение по методу активного обучения.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Используйте QnAMakerRuntimeClient, чтобы получить ответ от базы знаний или отправить в нее новые предложенные вопросы для [активного обучения](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Создание ответа из базы знаний

Создайте ответ из опубликованной базы знаний с помощью метода RuntimeClient.runtime.generateAnswer. Этот метод принимает идентификатор базы знаний и QueryDTO. Получите дополнительные свойства QueryDTO, например Top и Context, чтобы использовать их в чат-боте.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer&highlight=5)]

Это простой пример отправки запроса в базу знаний. Чтобы ознакомиться с расширенными сценариями запросов, см. [другие примеры запросов](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

Удалите базу знаний с помощью метода [удалить](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) с параметром идентификатора базы знаний.

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Некоторые методы, такие как "create" и "update", могут занять достаточно времени, чтобы вместо ожидания завершения процесса был возвращен [класс операции](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python). Используйте идентификатор операции из операции для опроса (с логикой повторных попыток), чтобы определить состояние исходного метода.

Вызов _setTimeout_ в следующем блоке кода используется для имитации асинхронного кода. Замените это на логику повторных попыток.

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation&highlight=7)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду python для файла quickstart.

```console
python quickstart-file.py
```

Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).