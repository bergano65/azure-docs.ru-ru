---
title: Краткое руководство. Клиентская библиотека QnA Maker для Go
description: В этом кратком руководстве показано, как начать работу с клиентской библиотекой QnA Maker для Go.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 1ca800574f4a1c370d953c90ea5abad1a64f47b2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351045"
---
Клиентская библиотека QnA Maker для Go позволяет выполнить указанные ниже задачи.

* Создание базы знаний
* Обновление базы знаний
* Публикация базы знаний
* Получение ключа конечной точки среды выполнения прогнозирования
* Ожидание долго выполняющейся задачи
* Скачивание базы знаний
* Получение ответов
* Удаление базы знаний

[Справочник (разработка)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker) | [Справочник (среда выполнения)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime) | [Исходный код библиотеки (разработка)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamaker) | [Исходный код библиотеки (среда выполнения)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamakerruntime) | [Примеры](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/QnAMaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* [GO](https://golang.org/)
* Получив подписку Azure, создайте [ресурс QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) на портале Azure, чтобы получить ключ разработки и конечную точку. После развертывания ресурса выберите элемент **Перейти к ресурсу**.
    * Для подключения приложения к API QnA Maker потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-go-application"></a>Создание приложения Go

Создайте файл с именем `kb_sample.go` и импортируйте указанные ниже библиотеки.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="dependencies":::

Создайте переменные для конечной точки Azure и ключа ресурса.

> [!IMPORTANT]
> Перейдите на портал Azure и найдите ключ и конечную точку для ресурса QnA Maker, созданного в рамках выполнения предварительных требований. Они находятся на странице ресурса **Ключ и конечная точка** в разделе **Управление ресурсами**.
> Для создания базы знаний необходим весь ключ. Необходимо только имя ресурса из конечной точки. Формат — `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Не забудьте удалить ключ из кода, когда закончите, и никогда не публикуйте его в открытом доступе. Для рабочей среды рекомендуется использовать безопасный способ хранения и доступа к учетным данным. Например, [Azure Key Vault](../../../key-vault/general/overview.md) предоставляет безопасное хранилище ключей.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="environment":::

## <a name="object-models"></a>Объектные модели

QnA Maker использует две различные объектные модели.
* **[QnAMakerClient](#qnamakerclient-object-model)**  — это объект для создания, публикации и скачивания базы знаний, а также управления ею.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)**  — это объект для отправки запроса в базу знаний с помощью API GenerateAnswer и новых предложенных вопросов с помощью API обучения (в рамках [активного обучения](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Объектная модель QnAMakerClient

Используйте [KnowledgebaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient) для создания, обновления, публикации, загрузки и удаления базы знаний. Эти операции описаны в разделах ниже.

Используйте [OperationsClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#OperationsClient), чтобы проверить состояние выполнения длительных операций, таких как создание и обновление баз знаний.

### <a name="qnamakerruntimeclient-object-model"></a>Объектная модель QnAMakerRuntimeClient

После публикации базы знаний используйте [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient), чтобы отправить к ней запрос. Эта операция рассматривается в разделе [Отправка запроса в базу знаний](#query-the-knowledge-base).

## <a name="create-a-knowledge-base"></a>Создание базы знаний

База знаний хранит пары вопросов и ответов для объекта [Класс CreateKbDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#CreateKbDTO) из трех источников:

* Для **редакционного содержимого** используйте объект [Класс QnADTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADTO).
    * Чтобы использовать метаданные и дальнейшие запросы, используйте редакционный контекст, так как эти данные добавляются на уровне отдельных пар вопросов и ответов.
* Для **файлов** используйте объект [Класс FileDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#FileDTO). FileDTO включает имя файла, а также общедоступный URL-адрес для доступа к этому файлу.
* В качестве значения для параметра **URL-адреса** используйте список строк, представляющих общедоступные URL-адреса.

Вызовите метод [create](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Create), затем передайте свойство `operationId` возвращенной операции в метод [getDetails](#get-status-of-an-operation) для опроса состояния.

Последняя строка указанного ниже кода возвращает идентификатор базы знаний.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="create_kb":::

## <a name="update-a-knowledge-base"></a>Обновление базы знаний

Вы можете обновить базу знаний, вызвав метод [update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Update), а затем передав ее идентификатор и объект [UpdateKbOperationDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTO). Этот объект, в свою очередь, может содержать следующие методы:
- [add](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOAdd)
- [update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOUpdate)
- [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTODelete)

Передайте свойство `operationId` возвращенной операции в метод [getDetails](#get-status-of-an-operation) для опроса состояния.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="update_kb":::

## <a name="download-a-knowledge-base"></a>Скачивание базы знаний

Используйте метод [загрузить](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Download), чтобы загрузить базу данных в виде списка [QnADocumentsDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADocumentsDTO). Это _не_ эквивалентно экспорту портала QnA Maker со страницы **настроек**, поскольку результатом этого метода не является файл TSV.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="download_kb":::

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Опубликуйте базу знаний, используя метод [опубликовать](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Publish). При этом берется текущая сохраненная и обученная модель, на которую ссылается ИД базы знаний, и публикуется ее в конечной точке.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="publish_kb":::

## <a name="query-the-knowledge-base"></a>Отправка запроса в базу знаний

После публикации базы знаний необходимо получить ключ конечной точки среды выполнения, чтобы отправить в нее запрос. Он отличается от ключа подписки, используемого для создания клиента разработки.

Создайте [EndpointKeysClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient) и используйте метод [getKeys](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient.GetKeys), чтобы получить объект [EndpointKeysDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysDTO).

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="get_runtime_endpoint_key":::

Создайте [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) и вызовите метод [GenerateAnswer](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient.GenerateAnswer), чтобы выполнить запрос в базу знаний.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="query_kb":::

Это простой пример отправки запроса в базу знаний. Чтобы ознакомиться с расширенными сценариями запросов, см. [другие примеры запросов](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

Удалите базу знаний с помощью метода [удалить](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Delete) с параметром идентификатора базы знаний.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="delete_kb":::

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Некоторые методы, такие как "create" и "update", могут занять достаточно времени, чтобы вместо ожидания завершения процесса был возвращен [класс операции](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#Operation). Используйте идентификатор операции из операции для опроса (с логикой повторных попыток), чтобы определить состояние исходного метода.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="wait_for_operation":::

## <a name="handle-errors"></a>Обработка ошибок

В следующем коде показано, как обрабатывать ошибки, которые могут возвращаться функциями пакета SDK.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="error_handling":::

## <a name="run-the-application"></a>Выполнение приложения

Ниже приведен метод Main для приложения.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="main":::

Запустите приложение, выполнив команду GO для файла quickstart.

```console
go run kb_sample.go
```

Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/QnAMaker/sdk/kb_sample.go)