---
title: Краткое руководство. Получение ответа из базы знаний в QnA Maker с помощью REST (Java)
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве по Java REST описывается, как получить ответ из базы знаний программными средствами.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 6d49ce71959f80a96731046475c3de5737bf7d46
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796195"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Получение ответов на вопрос из базы знаний с помощью Java

В этом кратком руководстве объясняется, как получить ответ из базы знаний QnA Maker программными средствами. База знаний содержит вопросы и ответы из [источника данных](../Concepts/data-sources-supported.md), например часто задаваемые вопросы. [Вопросы](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) отправляются в службу QnA Maker. Результат содержит прогнозируемый [ответ](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties). 

## <a name="prerequisites"></a>Предварительные требования

* [JDK SE](https://aka.ms/azure-jdks) (комплект разработчика Java, выпуск "Стандартный");
* В этом примере используется [HTTP-клиент](https://hc.apache.org/httpcomponents-client-ga/) Apache от HTTP Components. Вам нужно добавить в проект следующие библиотеки для HTTP-клиента Apache: 
    * httpclient-4.5.3.jar;
    * httpcore-4.4.6.jar;
    * commons-logging-1.2.jar.
* [Visual Studio Code](https://code.visualstudio.com/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить ключ, выберите **Ключи** в разделе **Управление ресурсами** на панели мониторинга ресурса QnA Maker на портале Azure. 
* Параметры страницы **Публикация**. Если у вас нет опубликованной базы знаний, создайте новую, импортируйте ее на странице **Параметры**, а затем опубликуйте ее. Можно скачать и использовать [эту простую базу знаний](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    На странице параметров публикации представлены значения Host, EndpointKey и маршрута POST. 

    ![Параметры публикации](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Код для этого краткого руководства доступен в репозитории [https://github.com/Azure-Samples/cognitive-services-qnamaker-java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-java-file"></a>Создание файла Java

Откройте VSCode, создайте файл с именем `GetAnswer.java` и добавьте следующий класс:

```Java
public class GetAnswer {

    public static void main(String[] args) 
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

В этом кратком руководстве используются классы Apache для HTTP-запросов. Над классом GetAnswer в верхней части файла `GetAnswer.java` добавьте необходимые зависимости в проект.

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Добавление необходимых констант

В верхней части класса `GetAnswer.java` добавьте необходимые константы для доступа к QnA Maker. Эти значения отобразятся на странице **публикации** после публикации базы знаний.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Добавление запроса POST для отправки вопроса

Следующий код создает HTTPS-запрос к API службы QnA Maker, чтобы отправить вопрос в базу знаний и получить ответ.

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

Значение заголовка `Authorization` содержит строку `EndpointKey`. 

Дополнительные сведения о [запросе](../how-to/metadata-generateanswer-usage.md#generateanswer-request) и [ответе](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Сборка и запуск проекта

Выполните сборку и запуск программы из командной строки. Она автоматически отправит запрос к API службы QnA Maker, а полученный ответ отобразится в окне консоли.

1. Выполните сборку файла:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Запустите файл.

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://go.microsoft.com/fwlink/?linkid=2092179) (Справочник по API REST QnA Maker (V4))
