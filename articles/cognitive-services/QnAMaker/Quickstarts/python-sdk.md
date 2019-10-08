---
title: Краткое руководство. Клиентская библиотека QnA Maker для Python
titleSuffix: Azure Cognitive Services
description: Приступая к созданию решений с помощью клиентской библиотеки QnA Maker для Python. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.  QnA Maker представляет собой службу для отправки вопросов и получения ответов из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов и руководств по продуктам.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 81e5ac5e5017330d2c5ea8bf5b269c4ce70b495b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802835"
---
# <a name="quickstart-qna-maker-client-library-for-python"></a>Краткое руководство. Клиентская библиотека QnA Maker для Python

Приступая к созданию решений с помощью клиентской библиотеки QnA Maker для Python. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.  QnA Maker представляет собой службу для отправки вопросов и получения ответов из частично структурированного содержимого, например документов с часто задаваемыми вопросами, URL-адресов и руководств по продуктам. 

Клиентская библиотека QnA Maker для Python позволяет выполнить следующие задачи.

* Создание базы знаний 
* Управление базой знаний
* Публикация базы знаний

[Справочная документация](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Пакет (pypi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Примеры Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Настройка

### <a name="create-a-qna-maker-azure-resource"></a>Создание ресурса QnA Maker Azure

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс с помощью [Create a Cognitive Services account using the Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)(создания учетной записи Cognitive Services с помощью портала Azure) или [Create a Cognitive Services account using the Azure Command-Line Interface(CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) (создания учетной записи Cognitive Services с помощью интерфейса командной строки Azure (CLI)) на локальном компьютере. 

После получения ключа от своего ресурса [создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ресурса с именами `QNAMAKER_KEY` и `QNAMAKER_HOST`. Используйте значения ключа и конечной точки, найденные на портале Azure.

### <a name="install-the-python-library-for-qna-maker"></a>Установка библиотеки Python для QnA Maker

После установки Python вы можете установить клиентскую библиотеку с помощью следующей команды:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>Объектная модель

Создайте объект [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) с ключом и используйте его с конечной точкой для создания объекта [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python).

После создания клиента используйте [Базу знаний](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) для создания, управления и публикации своей базы знаний. 

Для немедленных операций метод обычно возвращает объект JSON, указав статус. Ответ для длительных операций — ИД операции. Вызовите метод [client.Operations.getDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.operations%28class%29?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) с идентификатором операции, чтобы определить [состояние запроса](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python). 

 
## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с клиентской библиотекой QnA Maker для Python:

* [Создание базы знаний](#create-a-knowledge-base)
* [Обновление базы знаний](#update-a-knowledge-base)
* [Публикация базы знаний](#publish-a-knowledge-base)
* [Скачивание базы знаний](#download-a-knowledge-base)
* [Удаление базы знаний](#delete-a-knowledge-base)
* [Получение состояния операции](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте приложение Python в предпочитаемом редакторе или среде интегрированной разработки. Затем импортируйте приведенные ниже библиотеки.

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

Создайте переменные для конечной точки Azure и ключа ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.


|Переменная среды|переменная Python|Пример|
|--|--|--|
|`QNAMAKER_KEY`|`subscription_key`|32-символьный GUID|
|`QNAMAKER_HOST`|`host`|`https://your-resource-name.api.cognitive.microsoft.com` — замените `your-resource-name` собственным именем ресурса.|
||||

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Затем создайте объект CognitiveServicesCredentials со своим ключом и используйте его с конечной точкой, чтобы создать объект [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python).


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>Создание базы знаний

 Используйте объект клиента, чтобы получить объект [операций базы знаний](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python).

База знаний хранит пары вопросов и ответов для объекта [Класс CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) из трех источников:

* Для **редакционного содержимого** используйте объект [Класс QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python).
* Для **файлов** используйте объект [Класс FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python). 
* Для **URL-адресов** используйте список строк.

Вызовите метод[Создать](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-), а затем передайте возвращенный идентификатор операции методу [Operations.getDetails](#get-status-of-an-operation) для опроса состояния. 

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]

Чтобы создать базу знаний, добавьте функцию [`_monitor_operation`](#get-status-of-an-operation) из предыдущего примера кода. 

## <a name="update-a-knowledge-base"></a>Обновление базы знаний

Базу знаний можно обновить, передав идентификатор базы знаний и метод [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python), содержащий [добавить](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python), [обновить](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python) и [удалить](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) объекты DTO в метод [обновить](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-). Используйте метод [Operation.getDetail](#get-status-of-an-operation), чтобы определить, успешно ли выполнено обновление.

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

Чтобы обновить базу знаний, добавьте функцию [`_monitor_operation`](#get-status-of-an-operation) из предыдущего примера кода. 

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Опубликуйте базу знаний, используя метод [опубликовать](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-). При этом берется текущая сохраненная и обученная модель, на которую ссылается ИД базы знаний, и публикуется ее в конечной точке. 

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>Скачивание базы знаний

Используйте метод [загрузить](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-), чтобы загрузить базу данных в виде списка [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Это _не_ эквивалентно экспорту портала QnA Maker со страницы **настроек**, поскольку результатом этого метода не является файл TSV.

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Удаление базы знаний

Удалите базу знаний с помощью метода [удалить](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) с параметром идентификатора базы знаний. 

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>Получение состояния операции

Некоторые методы, такие как "create" и "update", могут занять достаточно времени, чтобы вместо ожидания завершения процесса был возвращен [класс операции](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation%28class%29?view=azure-python). Используйте идентификатор операции из операции для опроса (с логикой повторных попыток), чтобы определить состояние исходного метода. 

Вызов _setTimeout_ в следующем блоке кода используется для имитации асинхронного кода. Замените это на логику повторных попыток. 

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с командой `python knowledgebase_quickstart.py` из каталога приложения.

Все фрагменты кода из этой статьи [доступны для скачивания](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) и могут выполняться как один файл. 

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Руководство. Создание базы знаний и ответ на вопрос](../tutorials/create-publish-query-in-portal.md)

* [Что такое QnA Maker?](../Overview/overview.md)
* [Редактирование базы знаний](../how-to/edit-knowledge-base.md)
* [Get analytics on your knowledge base](../how-to/get-analytics-knowledge-base.md) (Получение аналитики по своей базе знаний)