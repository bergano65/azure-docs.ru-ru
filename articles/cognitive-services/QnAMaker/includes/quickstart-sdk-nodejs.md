---
title: Краткое руководство. Клиентская библиотека QnA Maker для Node.js
description: В этом кратком руководстве показано, как начать работу с клиентской библиотекой QnA Maker для Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9eb79223e91d31c76631d17263f6f33632824266
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351072"
---
Клиентская библиотека QnA Maker для Node.js:

* Создание базы знаний
* Обновление базы знаний
* Публикация базы знаний
* Получение ключа конечной точки среды выполнения прогнозирования
* Ожидание долго выполняющейся задачи
* Скачивание базы знаний
* Получение ответов
* Удаление базы знаний

[Справочная документация](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Пакет (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Примеры Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Текущая версия [Node.js](https://nodejs.org).
* Получив подписку Azure, создайте [ресурс QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) на портале Azure, чтобы получить ключ разработки и ресурс. После развертывания ресурса выберите элемент **Перейти к ресурсу**.
    * Для подключения приложения к API QnA Maker потребуется ключ и имя созданного ресурса. Ключ и имя ресурса вы вставите в приведенный ниже код на следующих шагах в рамках этого краткого руководства.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Выполните команду `npm init -y`, чтобы создать приложение узла с помощью файла `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите следующие пакеты npm:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

Файл `package.json` приложения обновлен с учетом зависимостей.

Создайте файл с именем index.js, откройте его и импортируйте следующие библиотеки:

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

Создайте переменную для ключа Azure и имени ресурса. В URL-адресах для разработки и прогнозирования имя ресурса используется в качестве поддомена.

> [!IMPORTANT]
> Перейдите на портал Azure и найдите ключ и конечную точку для ресурса QnA Maker, созданного в рамках выполнения предварительных требований. Они находятся на странице ресурса **Ключ и конечная точка** в разделе **Управление ресурсами**.
> Для создания базы знаний необходим весь ключ. Необходимо только имя ресурса из конечной точки. Формат — `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Не забудьте удалить ключ из кода, когда закончите, и никогда не публикуйте его в открытом доступе. Для рабочей среды рекомендуется использовать безопасный способ хранения и доступа к учетным данным. Например, [Azure Key Vault](../../../key-vault/general/overview.md) предоставляет безопасное хранилище ключей.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

## <a name="object-models"></a>Объектные модели

QnA Maker использует две различные объектные модели.
* **[QnAMakerClient](#qnamakerclient-object-model)**  — это объект для создания, публикации и скачивания базы знаний, а также управления ею.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)**  — это объект для отправки запроса в базу знаний с помощью API GenerateAnswer и новых предложенных вопросов с помощью API обучения (в рамках [активного обучения](../concepts/active-learning-suggestions.md)).


### <a name="qnamakerclient-object-model"></a>Объектная модель QnAMakerClient

Клиент разработки QnA Maker — это объект [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest), который проходит проверку подлинности в Azure с учетными данными, включающими ваш ключ.

После создания клиента используйте [Базу знаний](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) для создания, управления и публикации своей базы знаний.

Управляйте своей базой знаний, отправляя объект JSON. Для немедленных операций метод обычно возвращает объект JSON, указав статус. Ответ для длительных операций — ИД операции. Вызовите метод [client.operations.getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) с идентификатором операции, чтобы определить [состояние запроса](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest).

### <a name="qnamakerruntimeclient-object-model"></a>Объектная модель QnAMakerRuntimeClient

Клиент прогнозирования QnA Maker — это объект QnAMakerRuntimeClient, который проходит проверку подлинности в Azure с помощью объекта Microsoft.Rest.ServiceClientCredentials, содержащего ключ среды выполнения прогнозирования, полученный из вызова [client.EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) к клиенту разработки после публикации базы знаний.


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

Создайте экземпляр клиента с конечной точкой и ключом. Создайте объект ServiceClientCredentials с использованием вашего ключа и примените его с конечной точкой, чтобы создать объект [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest).

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Создание базы знаний

База знаний хранит пары вопросов и ответов для объекта [Класс CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) из трех источников:

* Для **редакционного содержимого** используйте объект [Класс QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest).
    * Чтобы использовать метаданные и дальнейшие запросы, используйте редакционный контекст, так как эти данные добавляются на уровне отдельных пар вопросов и ответов.
* Для **файлов** используйте объект [Класс FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest). FileDTO включает имя файла, а также общедоступный URL-адрес для доступа к этому файлу.
* В качестве значения для параметра **URL-адреса** используйте список строк, представляющих общедоступные URL-адреса.

На этапе создания также задаются свойства базы знаний:
* `defaultAnswerUsedForExtraction` — то, что возвращается, если ответ не найден;
* `enableHierarchicalExtraction` — автоматическое создание связей запросов между извлеченными парами вопросов и ответов;
* `language` — при создании первой базы данных ресурса задайте язык для использования в индексе Поиска Azure.

Вызовите метод ["Создать"](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) со сведениями о базе знаний. Сведения о базе знаний по сути представляют собой объект JSON.

После возвращения метода "Создать" передайте возвращенный идентификатор операции методу [wait_for_operation](#get-status-of-an-operation) для опроса состояния. Метод "wait_for_operation" возвращает значение после завершения операции. Проанализируйте значение заголовка `resourceLocation` возвращенной операции, чтобы получить новый идентификатор базы знаний.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod&highlight=39,46)]

Чтобы создать базу знаний, добавьте функцию [`wait_for_operation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="update-a-knowledge-base"></a>Обновление базы знаний

Базу знаний можно обновить, передав идентификатор базы знаний и метод [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest), содержащий [добавить](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [обновить](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) и [удалить](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) объекты DTO в метод [обновить](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-). DTO также являются объектами JSON. Используйте метод [wait_for_operation](#get-status-of-an-operation), чтобы определить, успешно ли выполнено обновление.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod&highlight=74,81)]

Чтобы обновить базу знаний, добавьте функцию [`wait_for_operation`](#get-status-of-an-operation) из предыдущего примера кода.

## <a name="download-a-knowledge-base"></a>Скачивание базы знаний

Используйте метод [загрузить](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-), чтобы загрузить базу данных в виде списка [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest). Это _не_ эквивалентно экспорту портала QnA Maker со страницы **настроек**, поскольку результатом этого метода не является файл TSV.

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB&highlight=2)]


## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Опубликуйте базу знаний, используя метод [опубликовать](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-). При этом берется текущая сохраненная и обученная модель, на которую ссылается ИД базы знаний, и публикуется ее в конечной точке. Проверьте код ответа HTTP, чтобы убедиться, что публикация прошла удачно.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB&highlight=3)]


## <a name="get-query-runtime-key"></a>Получение ключа среды выполнения запроса

После публикации базы знаний необходимо получить ключ среды выполнения запроса, чтобы отправить в нее запрос. Этот ключ отличается о того, который использовался для создания исходного объекта клиента.

Используйте метод [EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest), чтобы получить класс [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest).

Для запроса к базе знаний используйте любое свойство ключа, возвращаемое в объекте.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey&highlight=4)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Проверка подлинности среды выполнения для создания ответа

Создайте QnAMakerRuntimeClient, чтобы отправить в базу знаний запрос для создания ответа или обучения по методу активного обучения.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Используйте QnAMakerRuntimeClient, чтобы получить ответ от базы знаний или отправить в нее новые предложенные вопросы для [активного обучения](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Создание ответа из базы знаний

Создайте ответ из опубликованной базы знаний с помощью метода RuntimeClient.runtime.generateAnswer. Этот метод принимает идентификатор базы знаний и QueryDTO. Получите дополнительные свойства QueryDTO, например Top и Context, чтобы использовать их в чат-боте.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer&highlight=3)]

Это простой пример отправки запроса в базу знаний. Чтобы ознакомиться с расширенными сценариями запросов, см. [другие примеры запросов](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

Удалите базу знаний с помощью метода [удалить](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) с параметром идентификатора базы знаний.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Некоторые методы, такие как "create" и "update", могут занять достаточно времени, чтобы вместо ожидания завершения процесса был возвращен [класс операции](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest). Используйте [свойство Operation.OperationId](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) из операции для опроса (с логикой повторных попыток), чтобы определить состояние исходного метода.

Вызов _delayTimer_ в следующем блоке кода используется для имитации логики повторных попыток. Замените его на собственную логику повторных попыток.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation&highlight=8)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с командой `node index.js` из каталога приложения.

```console
node index.js
```

Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).