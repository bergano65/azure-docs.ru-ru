---
title: Справочник разработчика Java по Функциям Azure | Документация Майкрософт
description: Информация о разработке функций на языке Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, webhooks, dynamic compute, serverless architecture, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: acd873cd19cafb785f968fd3d8671640bcfafed8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163702"
---
# <a name="azure-functions-java-developer-guide"></a>Руководство разработчика Java по Функциям Azure

Среда выполнения функций Azure поддерживает [Java SE 8 LTS (zulu8.31.0.2 jre8.0.181 win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Это руководство содержит сведения о сложностях написание функций Azure на языке Java.

Функция Java является `public` метод, декорированные с аннотацией `@FunctionName`. Этот метод определяет запись для функции Java и должно быть уникальным в определенный пакет. 

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Также следует выполнить быстрый запуск функций, чтобы создать свою первую функцию с помощью [Visual Studio Code](functions-create-first-function-vs-code.md) или [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Модель программирования 

Понятия [триггеров и привязок](functions-triggers-bindings.md) играют решающую роль в Функциях Azure. Триггеры запускают выполнение вашего кода. Привязки предоставляют возможность передавать данные, а также возвращать их из функции без необходимости написания кода доступа к ним.

## <a name="folder-structure"></a>Структура папок

Ниже представлена структура папок проекта Java для функций Azure.

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Можно использовать общий [host.json](functions-host-json.md) файл для настройки приложения-функции. У каждой функции есть собственный файл кода (.java) и файл конфигурации привязки (function.json).

Вы можете добавить несколько функций в проект. Не добавляйте функции в отдельные JAR-файлы. `FunctionApp` В целевой каталог — что развернут на свое приложение-функцию в Azure.

## <a name="triggers-and-annotations"></a>Триггеры и заметки

 Функции вызываются с помощью триггера, например HTTP-запроса, таймер или обновления данных. Функция должна обработать такой триггер и все остальные входные данные, чтобы создать один или несколько выходов.

Используйте заметки Java, включенные в пакет [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation), чтобы привязать входные и выходные данные к своим методам. Дополнительные сведения см. в разделе [Java справочные документы](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Необходимо настроить учетную запись хранилища Azure в вашей [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) для локального запуска хранилища BLOB-объектов Azure, хранилище очередей Azure или триггеров хранилища таблиц Azure.

Пример:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Ниже приведен соответствующий созданный `function.json` по [azure — функции maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Обеспечения доступности и предоставления поддержки времени выполнения пакета JDK 

Для локальной разработки приложений-функций Java, скачайте и используйте [Azul Zulu Enterprise для Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK из [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Функции Azure используют среду выполнения Azul Java 8 JDK, если вы развертываете приложения-функции в облако.

[Поддержка Azure](https://azure.microsoft.com/support/) для устранения проблем с JDK и функцию приложения входит в состав [план технической поддержки](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Настройка виртуальной машины Java

Функции предоставляют возможность настройки виртуальной машины Java (JVM) позволяет запускать функции Java. [Следующие параметры виртуальной машины Java](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) используется по умолчанию:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Можно указать дополнительные аргументы в параметре приложения `JAVA_OPTS`. Можно добавить параметры приложения для приложения-функции развертывания в Azure на портале Azure или Azure CLI.

### <a name="azure-portal"></a>Портал Azure

В [портала Azure](https://portal.azure.com), использовать [вкладка "Параметры приложения"](functions-how-to-use-azure-function-app-settings.md#settings) добавление `JAVA_OPTS` параметр.

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Можно использовать [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) команду, чтобы задать `JAVA_OPTS`, как показано в следующем примере:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
В этом примере включается режим автоматического входа. Замените `<APP_NAME>` с именем приложения-функции и `<RESOURCE_GROUP>` с группой ресурсов.

> [!WARNING]  
> В [план потребления](functions-scale.md#consumption-plan), необходимо добавить `WEBSITE_USE_PLACEHOLDER` параметр со значением `0`.  
Этот параметр увеличивает время холодного запуска для функций Java.

## <a name="third-party-libraries"></a>Сторонние библиотеки 

Служба "Функции Azure" поддерживает использование сторонних библиотек. По умолчанию все зависимости, определенные в проекте `pom.xml` файл автоматически объединены во время [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) цели. Зависимости библиотек, не указанных как зависимости в файле `pom.xml`, поместите в каталог `lib` в корневой папке функции. Зависимости помещаются в `lib` directory добавляются загрузчик классов системы во время выполнения.

`com.microsoft.azure.functions:azure-functions-java-library` Зависимостей предоставляется на пути к классам, по умолчанию и не должно быть включено в `lib` каталога. Кроме того [azure функции java-worker](https://github.com/Azure/azure-functions-java-worker) добавляет перечисленные зависимости [здесь](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) в путь к классам.

## <a name="data-type-support"></a>Поддержка типов данных

Можно использовать обычные старые объекты Pojo Java, типы, определенные в `azure-functions-java-library`, или типы-примитивы, такие как строки и целое число для привязки входных или выходных привязках.

### <a name="pojos"></a>Элементов Pojo

Для преобразования входных данных в POJO, [azure функции java-worker](https://github.com/Azure/azure-functions-java-worker) использует [gson](https://github.com/google/gson) библиотеки. Типам POJO, используемые как входные данные для функций, необходим такой же модификатор доступа `public`.

### <a name="binary-data"></a>Двоичные данные

Привязать двоичные входные и выходные данные `byte[]`, установив `dataType` в файле function.json для `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Если ожидается, что значения null, используйте `Optional<T>`.

## <a name="bindings"></a>Привязки

Входные и выходные привязки реализуют декларативный способ подключения к данным из кода. У функции может быть несколько входных и выходных привязок.

### <a name="input-binding-example"></a>Пример входной привязки

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Можно вызвать эту функцию с помощью HTTP-запроса. 
- Полезные данные запроса HTTP передается в качестве `String` для аргумента `inputReq`.
- Одна запись извлекается из хранилища таблиц и передается в качестве `TestInputData` аргументу `inputData`.

Для получения пакета из входных данных, можно привязать к `String[]`, `POJO[]`, `List<String>`, или `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Эта функция запускается всякий раз, когда в настроенном концентраторе событий появляются новые данные. Так как `cardinality` присваивается `MANY`, функция получает пакет сообщений из концентратора событий. `EventData` из события концентратора преобразуются в нумерованные списки `TestEventData` для выполнения функции.

### <a name="output-binding-example"></a>Пример выходной привязки

Можно применить выходную привязку к возвращаемому значению с помощью `$return`. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

При наличии нескольких выходных привязок используйте возвращаемое значение только для одной из них.

Чтобы отправить несколько выходных значений, используйте тип `OutputBinding<T>`, который определен в пакете `azure-functions-java-library`. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Можно вызвать эту функцию на объекты HttpRequest. Несколько значений записываются в хранилище очередей.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage и HttpResponseMessage

 Они определяются в `azure-functions-java-library`. Они являются вспомогательные типы для работы с функциями HttpTrigger.

| Особый тип      |       Цель        | Типичное применение                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Триггер HTTP     | Возвращает метод, заголовки и запросы |
| `HttpResponseMessage` | Привязка к выходным данным HTTP | Возвращает состояние, отличное от 200   |

## <a name="metadata"></a>Метаданные

Несколько триггеров отправляют [метаданные триггеров](/azure/azure-functions/functions-triggers-bindings) вместе с входными данными. Можно использовать заметки `@BindingName` для привязки к метаданные триггеров.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
В приведенном выше примере `queryValue` привязан к параметру строки запроса `name` в URL-АДРЕСЕ запроса http, `http://{example.host}/api/metadata?name=test`. Вот еще один пример, показывающий, как выполнить привязку к `Id` из метаданные триггера очереди.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Имя, указанное в аннотации должен для сопоставления свойства метаданных.

## <a name="execution-context"></a>Контекст выполнения

`ExecutionContext`, определенный в `azure-functions-java-library`, содержит вспомогательные методы для взаимодействия со средой выполнения функций.

### <a name="logger"></a>Средство ведения журнала

Используйте `getLogger`, определенный в `ExecutionContext`для записи журналов из кода функции.

Пример:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Просмотр журналов и трассировки

Можно использовать Azure CLI для потока Java stdout и stderr ведения журнала, а также ведение журналов для других приложений. 

Вот как настроить свое приложение-функцию для записи ведение журнала приложений с помощью Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Потоковую передачу выходных данных ведения журнала для приложения-функции с помощью Azure CLI, откройте новую командную строку, Bash или сеанс терминала и введите следующую команду:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log) команда имеет параметры для фильтрации выходных данных с помощью `--provider` параметр. 

Чтобы скачать файлы журналов в один ZIP-файл с помощью интерфейса командной строки Azure, откройте новую командную строку, Bash или сеанс терминала и введите следующую команду:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Необходимо включить ведение журнала на портале Azure или Azure CLI, перед выполнением этой команды файловой системы.

## <a name="environment-variables"></a>Переменные среды

В Функциях [параметры приложения](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Эти параметры доступны с помощью, `System.getenv("AzureWebJobsStorage")`.

Например, можно добавить [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings), с именем `testAppSetting` и значение `testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке приложений Java для функций Azure см. следующие ресурсы:

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* Локальная разработка и отладка в [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), и [Eclipse](functions-create-maven-eclipse.md)
* [Remote Debug Java Azure Functions with Visual Studio Code (Удаленная отладка функций Azure на языке Java с помощью Visual Studio Code)](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Подключаемый модуль maven для функций Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Упрощает создание функции через `azure-functions:add` цели и подготовьте промежуточного каталога для [развертывания ZIP-файла](deployment-zip-push.md).
