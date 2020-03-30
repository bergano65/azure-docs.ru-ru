---
title: Пользовательские обработчики функций Azure (предварительный просмотр)
description: Научитесь использовать функции Azure с любым языком или версией времени выполнения.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479258"
---
# <a name="azure-functions-custom-handlers-preview"></a>Пользовательские обработчики функций Azure (предварительный просмотр)

Каждое приложение Функции выполняется обработчиком, специфичным для языка. Хотя Функции Azure поддерживают множество [обработчиков языков](./supported-languages.md) по умолчанию, в некоторых случаях может потребоваться дополнительный контроль над средой выполнения приложения. Пользовательские обработчики дают вам этот дополнительный контроль.

Пользовательские обработчики — это легкие веб-серверы, которые получают события от хоста Функции. Любой язык, поддерживающий примитивы HTTP, может реализовать пользовательский обработчик.

Пользовательские обработчики лучше всего подходят для ситуаций, когда вы хотите:

- Внедрение приложения Функции на языке, выходящих за рамки официально поддерживаемых языков
- Реализация приложения Функции в языковой версии или время выполнения, не поддерживаемое по умолчанию
- Иметь детальный контроль над средой выполнения приложения

С пользовательскими обработчиками все [триггеры и входные и выходные привязки](./functions-triggers-bindings.md) поддерживаются через [пакеты расширения.](./functions-bindings-register.md)

## <a name="overview"></a>Обзор

На следующей диаграмме показана взаимосвязь между хостом Функции и веб-сервером, реализованным в виде пользовательского обработчика.

![Обзор пользовательского обработчика функций Azure](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- События вызывают запрос, отправленный хостам Функции. Событие несет в себе либо необработанную полезную нагрузку HTTP (для функций HTTP-спусков без привязок), либо полезную нагрузку, которая содержит данные связывания ввода для функции.
- Затем хост Функции запрашивает запрос на веб-сервер, выдавая [полезную нагрузку запроса.](#request-payload)
- Веб-сервер выполняет индивидуальную функцию и возвращает [полезную нагрузку ответа](#response-payload) хостам функций.
- Функции хост прокси ответ как выход связывания полезной нагрузки к цели.

Приложение Azure Functions, реализованное в качестве пользовательского обработчика, должно настроить файлы *host.json* и *function.json* в соответствии с несколькими конвенциями.

## <a name="application-structure"></a>Структура приложений

Для реализации пользовательского обработчика требуется следующие аспекты приложения:

- Файл *host.json* в корне приложения
- Файл *function.json* для каждой функции (внутри папки, которая соответствует названию функции)
- Команда, скрипт или исполняемый, который запускает веб-сервер

Следующая диаграмма показывает, как эти файлы выглядят в файловой системе для функции под названием "заказ".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Параметр Configuration

Приложение настроено через файл *host.json.* Этот файл сообщает хоузу функций, куда отправлять запросы, указывая на веб-сервер, способный обрабатывать события HTTP.

Пользовательский обработчик определяется путем настройки файла *host.json* с подробной информацией о том, как запустить веб-сервер через `httpWorker` раздел.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

Раздел `httpWorker` указывает на цель, определяемую `defaultExecutablePath`. Целью выполнения может быть команда, исполняемая или файловая, в которой реализован веб-сервер.

Для приложений `defaultExecutablePath` со сценарием указывает на время `defaultWorkerPath` выполнения языка скрипта и указывает на местоположение файла скрипта. Ниже приводится следующий пример, как приложение JavaScript в Node.js настроено как пользовательский обработчик.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Вы также можете передать `arguments` аргументы с помощью массива:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Аргументы необходимы для многих отладок установки. Более подробную информацию можно узнать в разделе [«Отладка».](#debugging)

> [!NOTE]
> Файл *host.json* должен быть на том же уровне в структуре каталога, что и запущенный веб-сервер. Некоторые языки и цепочки инструментов не могут по умолчанию поместить этот файл в корне приложения.

#### <a name="bindings-support"></a>Поддержка связей

Стандартные триггеры наряду с входными и выходными привязками доступны путем ссылки [пакетов расширения](./functions-bindings-register.md) в файле *host.json.*

### <a name="function-metadata"></a>Метаданные функций

При использовании с пользовательским обработчиком содержимое *function.json* ничем не отличается от того, как вы бы определили функцию в любом другом контексте. Единственным требованием является то, что файлы *function.json* должны быть в папке, названной в соответствии с именем функции.

### <a name="request-payload"></a>Запрос полезной нагрузки

Полезная нагрузка запроса для чистых функций HTTP является необработанной полезной нагрузкой запроса HTTP. Функции Pure HTTP определяются как функции без входных или выходных привязок, которые возвращают ответ HTTP.

Любой другой тип функции, который включает в себя ввод, выходные привязки или срабатывает через источник событий, кроме HTTP, имеет пользовательскую полезную нагрузку запроса.

Следующий код представляет собой полезную нагрузку запроса образца. Полезная нагрузка включает в себя `Data` структуру JSON с двумя членами: и `Metadata`.

В `Data` состав участника входят ключи, которые соответствуют именам ввода и вызывают имена, как это определено в массиве переплетов в файле *function.json.*

В `Metadata` состав участника входят [метаданные, генерируемые из источника событий.](./functions-bindings-expressions-patterns.md#trigger-metadata)

С учетом привязок, определенных в следующем файле *function.json:*

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Возвращается полезная нагрузка запроса, аналогичная данному примеру:

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>Полезная нагрузка реагирования

По конвенции ответы функций отформатированы как пары ключей/значений. Поддерживаемые клавиши включают в себя:

| <nobr>Ключ полезной нагрузки</nobr>   | Тип данных | Remarks                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Сохраняет значения отклика, `bindings` определяемые массивом *файла function.json.*<br /><br />Например, если функция настроена с привязкой выхода на склад `Outputs` ыплю под `blob`названием "blob", то содержит ключ с именем, который устанавливается на значение капли. |
| `Logs`        | массиве     | Сообщения отображаются в журналах вызовов функций.<br /><br />При запуске в Azure сообщения отображаются в приложениях Insights. |
| `ReturnValue` | строка    | Используется для предоставления ответа при настройке вывода, как `$return` в файле *function.json.* |

Смотрите [пример для примера полезной нагрузки.](#bindings-implementation)

## <a name="examples"></a>Примеры

Пользовательские обработчики могут быть реализованы на любом языке, поддерживающем события HTTP. В то время как Функции Azure [полностью поддерживают JavaScript и Node.js,](./functions-reference-node.md)следующие примеры показывают, как реализовать пользовательский обработчик с помощью JavaScript в Node.js для целей обучения.

> [!TIP]
> Будучи руководством для обучения реализации пользовательского обработчика на других языках, приведенные здесь примеры на основе Node.js также могут быть полезны, если вы хотите запустить приложение Функции в неподдерживаемой версии Node.js.

## <a name="http-only-function"></a>Функция только http

В следующем примере показано, как настроить функцию HTTP-триггера без каких-либо дополнительных привязок или выходов. Сценарий, реализованный в этом `http` примере, `GET` `POST` имеет функцию, названную, которая принимает или .

Следующий фрагмент представляет, как состоит запрос на функцию.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Реализация

В папке с именем *http*файл *function.json* настраивает функцию HTTP-триггера.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Функция настроена для приема `GET` `POST` как запросов, так и запросов, а значение результата обеспечивается с помощью аргумента с именем `res`.

В корне приложения настроен файл *host.json* для запуска Node.js и `server.js` настройки файла.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Файл file *server.js* реализует веб-сервер и функцию HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

В этом примере Express используется для создания веб-сервера для обработки `FUNCTIONS_HTTPWORKER_PORT`событий HTTP и настроен на прослушивание запросов через .

Функция определяется на пути `/hello`. `GET`запросы обрабатываются путем возврата простого `POST` объекта JSON, а `req.body`запросы имеют доступ к органу запроса через .

Маршрут для функции заказа `/hello` здесь, а не `/api/hello` потому, что хост функций прокси запрос на пользовательском обработчик.

>[!NOTE]
>Это `FUNCTIONS_HTTPWORKER_PORT` не общественный порт, используемый для вызова функции. Этот порт используется хостом Функций для вызова пользовательского обработчика.

## <a name="function-with-bindings"></a>Функция с привязками

Сценарий, реализованный в этом `order` примере, `POST` имеет функцию, названную, которая принимает полезную нагрузку, представляющую заказ продукта. При размещении заказа в функции создается сообщение хранения очереди и возвращается ответ HTTP.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Реализация

В папке с именем *заказа*файл *function.json* настраивает функцию HTTP-triggered.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

Эта функция определяется как [функция http,](./functions-bindings-http-webhook-trigger.md) которая возвращает [ответ HTTP](./functions-bindings-http-webhook-output.md) и выводит сообщение [хранения очереди.](./functions-bindings-storage-queue-output.md)

В корне приложения настроен файл *host.json* для запуска Node.js и `server.js` настройки файла.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Файл file *server.js* реализует веб-сервер и функцию HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

В этом примере Express используется для создания веб-сервера для обработки `FUNCTIONS_HTTPWORKER_PORT`событий HTTP и настроен на прослушивание запросов через .

Функция определяется на пути `/order` .  Маршрут для функции заказа `/order` здесь, а не `/api/order` потому, что хост функций прокси запрос на пользовательском обработчик.

По `POST` мере отправки запросов на эту функцию данные раздаются через несколько пунктов:

- Тело запроса доступно через`req.body`
- Данные, размещенные в функции, доступны через`req.body.Data.req.Body`

Ответ функции отформатирован в пару ключей/значений, где `Outputs` участник содержит значение JSON, где ключи соответствуют выводам, определенным в файле *function.json.*

Установив `message` равнозначно сообщению, пришедшего из `res` запроса, и ожидаемому ответу HTTP, эта функция выводит сообщение в емкость хранения и возвращает ответ HTTP.

## <a name="debugging"></a>Отладка

Чтобы отладить пользовательское обработчик приложения функций, необходимо добавить аргументы, подходящие для языка и времени выполнения, чтобы включить отладку.

Например, для отладки приложения Node.js `--inspect` флаг передается в качестве аргумента в файле *host.json.*

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> Конфигурация отладки является частью файла *host.json,* что означает, что вам может потребоваться удалить некоторые аргументы перед развертыванием в производство.

С помощью этой конфигурации можно запустить процесс узла функции, используя следующую команду:

```bash
func host start
```

Как только процесс запущен, вы можете прикрепить отладчик и поразить точки разрыва.

### <a name="visual-studio-code"></a>Visual Studio Code

Следующим примером является пример конфигурации, которая демонстрирует, как можно настроить файл *launch.json* для подключения приложения к отладчику Visual Studio.

Этот пример предназначен для Node.js, поэтому, возможно, вам придется изменить этот пример для других языков или времени выполнения.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Развертывание

Пользовательский обработчик может быть развернут почти во всех вариантах хостинга Azure Functions [(см. ограничения).](#restrictions) Если обработчику требуются пользовательские зависимости (например, время выполнения языка), возможно, потребуется использовать [пользовательский контейнер.](./functions-create-function-linux-custom-image.md)

## <a name="restrictions"></a>Ограничения

- Пользовательские обработчики не поддерживаются в планах потребления Linux.
- Веб-сервер должен начаться в течение 60 секунд.

## <a name="samples"></a>Примеры

Обратитесь к [пользовательским пробам обработчика GitHub repo](https://github.com/Azure-Samples/functions-custom-handlers) для примеров того, как реализовать функции на различных языках.
