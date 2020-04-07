---
title: Ссылка Java-разработчика для функций Azure
description: Информация о разработке функций на языке Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4b1f39ff4fd48a3ed99b34391e9cc6efdad86a5d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673003"
---
# <a name="azure-functions-java-developer-guide"></a>Руководство разработчика Java по Функциям Azure

Время выполнения функций Azure поддерживает [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64).](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/) В этом руководстве содержится информация о тонкостях написания функций Azure с Java.

Как это происходит с другими языками, функция App может иметь одну или несколько функций. Функция Java — `public` это метод, украшенный `@FunctionName`аннотацией. Этот метод определяет запись для функции Java и должен быть уникальным в определенном пакете. Одно функциональное приложение, написанное на Java, может иметь `@FunctionName`несколько классов с несколькими общедоступными методами, аннотированными.

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Вы также должны завершить функции quickstart для создания первой функции, с помощью [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) или [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Модель программирования 

Понятия [триггеров и привязок](functions-triggers-bindings.md) играют решающую роль в Функциях Azure. Триггеры запускают выполнение вашего кода. Привязки предоставляют возможность передавать данные, а также возвращать их из функции без необходимости написания кода доступа к ним.

## <a name="create-java-functions"></a>Создание функций Java

Чтобы облегчить создание java-функций, есть инструментарий и архетипы на основе Maven, которые используют заранее определенные шаблоны Java, чтобы помочь вам создавать проекты с конкретным триггером функции.    

### <a name="maven-based-tooling"></a>Инструментарий на основе Maven

Следующие среды разработчиков оснащены инструментарием Azure Functions, который позволяет создавать функциональные проекты Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Ссылки на статьи выше показать вам, как создать свои первые функции с помощью IDE выбора. 

### <a name="project-scaffolding"></a>Проект Леса

Если вы предпочитаете разработку командной строки из Терминала, самый простой способ использовать `Apache Maven` проекты функций на основе Java — использовать архетипы. Архетип Java Maven для функций Azure публикуется под следующей _группойId_:_artifactId_: [com.microsoft.azure:azure-functions-archetype.](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/) 

Следующая команда генерирует новый проект функции Java с помощью этого архетипа:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Чтобы начать использовать этот архетип, [см.](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java) 

## <a name="create-kotlin-functions-preview"></a>Создание функций Kotlin (предварительный просмотр)

Существует также архетип Maven для генерации функций Kotlin. Этот архетип, в настоящее время в предварительном просмотре, публикуется в соответствии со следующей _группойId_:_artifactId_: [com.microsoft.azure:Azure-функции-котлин-архетип](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

Следующая команда генерирует новый проект функции Java с помощью этого архетипа:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

Чтобы начать использовать этот архетип, [см.](functions-create-first-kotlin-maven.md)

## <a name="folder-structure"></a>Структура папок

Вот структура папок проекта Azure Functions Java:

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

_Проект Котлин выглядит очень похоже, так как он по-прежнему Maven_

Для настройки приложения функции можно использовать общий файл [host.json.](functions-host-json.md) У каждой функции есть собственный файл кода (.java) и файл конфигурации привязки (function.json).

Вы можете добавить несколько функций в проект. Не добавляйте функции в отдельные JAR-файлы. В `FunctionApp` целевом каталоге используется то, что развертывается в приложении функции в Azure.

## <a name="triggers-and-annotations"></a>Триггеры и заметки

 Функции вызываются триггером, таким как запрос HTTP, таймер или обновление данных. Функция должна обрабатывать этот триггер и любые другие входные данные, чтобы произвести один или несколько выходов.

Используйте заметки Java, включенные в пакет [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation), чтобы привязать входные и выходные данные к своим методам. Для получения дополнительной [информации](/java/api/com.microsoft.azure.functions.annotation)см.

> [!IMPORTANT] 
> Необходимо настроить учетную запись хранилища Azure в [локальном.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) для запуска хранилища Azure Blob, хранения очереди Azure или локальных триггеров таблицы Azure Table.

Пример

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Вот генерируется `function.json` соответствующие [лазурные функции-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

Для локальной разработки приложений java-функций загрузите и используйте [предприятие Azul зулу для Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK от [Azul Systems.](https://www.azul.com/downloads/azure-only/zulu/) Функции Azure используют среду выполнения Azul Java 8 JDK, если вы развертываете приложения-функции в облако.

[Поддержка Azure](https://azure.microsoft.com/support/) для проблем с JDK и функциональными приложениями доступна с [помощью квалифицированного плана поддержки.](https://azure.microsoft.com/support/plans/)

## <a name="customize-jvm"></a>Настройка JVM

Функции позволяют настроить виртуальную машину Java (JVM), используемую для выполнения java-функций. [Следующие параметры JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) используются по умолчанию:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Дополнительные аргументы можно предоставить `JAVA_OPTS`в настройке приложения под названием. Настройки приложения можно добавить в функциональное приложение, развернутое в Azure на портале Azure или в ClI Azure.

### <a name="azure-portal"></a>Портал Azure

На [портале Azure](https://portal.azure.com)используйте [вкладку «Настройки приложений»](functions-how-to-use-azure-function-app-settings.md#settings) для добавления настройки. `JAVA_OPTS`

### <a name="azure-cli"></a>Azure CLI

Вы можете использовать набор `JAVA_OPTS` [настройки настройки настройки настройки настройки настройки настройки настройки приложения для конфигурации az functionapp,](/cli/azure/functionapp/config/appsettings) как в следующем примере:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Этот пример позволяет безголовый режим. Замените `<APP_NAME>` название приложения `<RESOURCE_GROUP>` функции и группы ресурсов.

> [!WARNING]  
> В [плане потребления](functions-scale.md#consumption-plan)необходимо добавить настройку `WEBSITE_USE_PLACEHOLDER` `0`со значением.  
Эта настройка увеличивает время начала разработки Java-функций.

## <a name="third-party-libraries"></a>Сторонние библиотеки 

Служба "Функции Azure" поддерживает использование сторонних библиотек. По умолчанию все зависимости, `pom.xml` указанные в файле [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) проекта, автоматически упаковываются во время цели. Зависимости библиотек, не указанных как зависимости в файле `pom.xml`, поместите в каталог `lib` в корневой папке функции. Зависимости, помещенные в `lib` каталог, добавляются в погрузчик системного класса во время выполнения.

Зависимость `com.microsoft.azure.functions:azure-functions-java-library` предоставляется на classpath по умолчанию и не должна быть `lib` включена в каталог. Кроме того, [azure-функции-java-работник](https://github.com/Azure/azure-functions-java-worker) добавляет в классный путь зависимости, перечисленные [здесь.](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)

## <a name="data-type-support"></a>Поддержка типов данных

Можно использовать старые java-объекты Plain (POJ), типы, определенные в `azure-functions-java-library`типах данных, такие как String и Integer, для привязки к входным или выходным привязкам.

### <a name="pojos"></a>POJ

Для преобразования входных данных в POJO [лазурные функции-java-работник](https://github.com/Azure/azure-functions-java-worker) использует библиотеку [gson.](https://github.com/google/gson) Типам POJO, используемые как входные данные для функций, необходим такой же модификатор доступа `public`.

### <a name="binary-data"></a>Двоичные данные

Связать двоичные входы `byte[]`или выходы, установив `dataType` поле в `binary`функции.json:

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

Если вы ожидаете нулевые значения, используйте `Optional<T>`.

## <a name="bindings"></a>Привязки

Входные и выходные привязки реализуют декларативный способ подключения к данным из кода. У функции может быть несколько входных и выходных привязок.

### <a name="input-binding-example"></a>Пример связывания ввода

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

Вы вызываете эту функцию с помощью запроса HTTP. 
- Полезная нагрузка запроса `String` HTTP `inputReq`передается в качестве аргумента.
- Одна запись извлекается из хранилища таблицы и передается в `TestInputData` аргумент. `inputData`

Для получения партии входов, вы `String[]`можете `POJO[]` `List<String>`привязать `List<POJO>`к , , или .

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

Эта функция запускается всякий раз, когда в настроенном концентраторе событий появляются новые данные. Поскольку `cardinality` функция `MANY`настроена на то, что функция получает пакет сообщений из концентратора событий. `EventData`из концентратора событий преобразуется `TestEventData` в для выполнения функции.

### <a name="output-binding-example"></a>Пример выходной привязки

Вы можете связать выходную привязку `$return`к значению возврата с помощью . 

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

Вы ссылаетесь на эту функцию на HttpRequest. Он записывает несколько значений для хранения очереди.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage и HttpResponseMessage

 Они определяются `azure-functions-java-library`в . Они являются типами помощников для работы с функциями HttpTrigger.

| Специализированный тип      |       Назначение        | Типичное использование                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Триггер HTTP     | Получает метод, заголовки или запросы |
| `HttpResponseMessage` | Привязка к выходным данным HTTP | Статус возврата, кроме 200   |

## <a name="metadata"></a>Метаданные

Несколько триггеров отправляют [метаданные триггеров](/azure/azure-functions/functions-triggers-bindings) вместе с входными данными. Аннотация `@BindingName` может быть связана, чтобы вызвать метаданные.


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
В предыдущем примере `queryValue` параметр строки запроса привязан к `name` `http://{example.host}/api/metadata?name=test`параметру строки запроса в URL-адресе запроса HTTP, Вот еще один пример, показывающий, как связываться `Id` с метаданными триггера очереди.

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
> Имя, приведенное в аннотации, должно соответствовать свойству метаданных.

## <a name="execution-context"></a>Контекст выполнения

`ExecutionContext`, определяется `azure-functions-java-library`в , содержит помощник методы для связи с функциями времени выполнения.

### <a name="logger"></a>Средство ведения журнала

Используйте, `getLogger` `ExecutionContext`определенные в , для записи журналов из кода функции.

Пример

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

Вы можете использовать Azure CLI для потоковой передачи регистрации Java stdout и stderr, а также для других журналов приложений. 

Вот как настроить приложение функции для записи регистрации приложений с помощью Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Чтобы поток вывода журнала для приложения функции С помощью приложения Azure CLI, откройте новый запрос команды, сеанс Bash или Terminal и введите следующую команду:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Команда [хвоста журнала webapp имеет](/cli/azure/webapp/log) варианты `--provider` фильтрации вывода с помощью опции. 

Чтобы загрузить файлы журнала в виде единого файла с помощью файла Azure CLI, откройте новый запрос команды, сеанс Bash или Terminal и введите следующую команду:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Перед запуском этой команды необходимо завести регистрацию файловой системы на портале Azure или в CLI Azure.

## <a name="environment-variables"></a>Переменные среды

В Функциях [параметры приложения](functions-app-settings.md), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Вы можете получить доступ к `System.getenv("AzureWebJobsStorage")`этим настройкам с помощью, .

Следующий пример получает [настройки приложения,](functions-how-to-use-azure-function-app-settings.md#settings)с ключом с именем: `myAppSetting`

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о разработке Azure Functions Java см.

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Триггеры и привязки функций Azure](functions-triggers-bindings.md)
* Местная разработка и отладка с [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)и [Eclipse](functions-create-maven-eclipse.md)
* [Remote Debug Java Azure Functions with Visual Studio Code (Удаленная отладка функций Azure на языке Java с помощью Visual Studio Code)](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Плагин Maven для функций Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Оптимизация создания функции `azure-functions:add` через цель и подготовка постановочный каталог для развертывания [файла.](deployment-zip-push.md)
