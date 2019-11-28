---
title: Подключение функции Java к службе хранилища Azure
description: Узнайте, как подключить функцию Java, активируемую HTTP, к службе хранилища Azure с помощью выходной привязки хранилища очередей.
ms.date: 10/14/2019
ms.topic: quickstart
ms.openlocfilehash: f9c3445efbe14c27cad9de62f5b46f50965e3145
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231209"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Подключение функции Java к службе хранилища Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

В этой статье приведены сведения об интеграции функции, которая была создана в [предыдущей статье](functions-create-first-java-maven.md), с помощью очереди службы хранилища Azure. Выходная привязка, которая была добавлена в эту функцию, записывает в сообщение очереди данные HTTP-запроса.

Большинство привязок требуют сохраненную строку подключения, которая будет использоваться Функциями Azure для доступа к привязанным службам. Чтобы упростить это подключение, вам следует использовать учетную запись хранения, созданную в приложении-функции. Подключение к этой учетной записи уже хранится в параметрах приложения под названием `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этой статьей следует завершить все шаги из [части 1 краткого руководства по Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Загрузка параметров приложения-функции

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Включение пакетов расширений

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Теперь вы можете добавить выходную привязку службы хранилища в проект.

## <a name="add-an-output-binding"></a>Добавление выходной привязки

В проекте Java привязки определяются как заметки привязки для метода функции. Затем на основе этих заметок автоматически создается файл *function.json*.

Найдите расположение вашего кода функции в _src/main/java_, откройте файл проекта *Function.java* и добавьте следующий параметр в `run` определение метода:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Параметр `msg` имеет тип [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), представляющий собой набор строк, записываемых в виде сообщений в выходную привязку после завершения выполнения функции. В этом случае выходные данные представляют собой очередь хранилища с именем `outqueue`. Строка подключения для учетной записи хранения задается методом `connection`. Вместо самой строки подключения передается параметр приложения, содержащий строку подключения к учетной записи хранилища.

Определение метода `run` теперь должно выглядеть следующим образом:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Добавление кода, который использует выходную привязку

Теперь для записи в выходную привязку из кода функции вы можете использовать новый параметр `msg`. Добавьте следующую строку кода перед успешным ответом, чтобы добавить значение `name` к выходной привязке `msg`.

```java
msg.setValue(name);
```

После использования выходной привязки вам для проверки подлинности, получения ссылки на очередь или записи данных больше не потребуется код пакета SDK службы хранилища Azure. Вместо вас эти задачи будут выполнены выходной привязкой очереди и средой выполнения функции.

Теперь метод `run` должен выглядеть следующим образом:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>Обновление тестов

Поскольку архетип также создает набор тестов, необходимо обновить эти тесты для обработки нового параметра `msg` в сигнатуре метода `run`.  

Найдите местоположение тестового кода в _src/test/java_, откройте файл проекта *Function.java* и замените строку кода в `//Invoke` следующим кодом.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Теперь новую выходную привязку можно испытать локально.

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Как и прежде, чтобы построить проект и запустить среду выполнения функций локально, используйте следующую команду.

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Так как пакеты расширений включены в файл host.json, во время запуска вместе с другими расширениями привязки Майкрософт было также загружено и установлено [расширение привязки службы хранилища](functions-bindings-storage-blob.md#packages---functions-2x).

Как и прежде, запустите функцию из командной строки, используя cURL в новом окне терминала:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

На этот раз в хранилище очередей выходная привязка также создает очередь с именем `outqueue` и с помощью этой же строки добавляет сообщение.

Затем Azure CLI следует использовать для просмотра новой очереди и проверки добавленного сообщения. Кроме того, очередь можно просматривать с помощью [Обозревателя службы хранилища Microsoft Azure][Azure Storage Explorer] или на [портале Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Повторное развертывание проекта 

Чтобы обновить опубликованное приложение, выполните следующую команду еще раз:  

```azurecli
mvn azure-functions:deploy
```

Чтобы проверить развернутую функцию, можно снова использовать cURL. Как и ранее, передайте значение `AzureFunctions` в тексте запроса POST к URL-адресу, как показано в следующем примере:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Чтобы убедиться, что выходная привязка снова создает новое сообщение в очереди, как и ожидалось, можно [проверить сообщение очереди службы хранилища](#query-the-storage-queue).

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы обновили функцию, активируемую HTTP, которую теперь можно использовать для записи данных в очередь службы хранилища. Чтобы получить дополнительные сведения о разработке Функций Azure с помощью Java, просмотрите статьи [Руководство разработчика Java по Функциям Azure](functions-reference-java.md) и [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md). Примеры завершенных проектов Функций в Java см. в [этой статье](/samples/browse/?products=azure-functions&languages=Java). 

Затем для мониторинга приложения-функции следует включить Application Insights.

> [!div class="nextstepaction"]
> [Включение интеграции с Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/