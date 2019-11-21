---
title: JavaScript developer reference for Azure Functions
description: Узнайте, как разрабатывать функции с помощью JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: b6b7db4c5f13a264b76dcab02dba51c464297307
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226706"
---
# <a name="azure-functions-javascript-developer-guide"></a>Руководство разработчика JavaScript для Функций Azure

Это руководство содержит сведения о сложностях написания Функций Azure на языке JavaScript.

Функция JavaScript — это экспортированная `функция` , которая `function` выполняется при активации ([триггеры настраиваются в файле function.json](functions-triggers-bindings.md)). The first argument passed to every function is a `context` object, which is used for receiving and sending binding data, logging, and communicating with the runtime.

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Complete the Functions quickstart to create your first function, using [Visual Studio Code](functions-create-first-function-vs-code.md) or [in the portal](functions-create-first-azure-function.md).

This article also supports [TypeScript app development](#typescript).

## <a name="folder-structure"></a>Структура папок

Необходимая структура папок для проекта JavaScript выглядит следующим образом. Это значение по умолчанию можно изменить. Дополнительные сведения см. в разделе о [scriptFile](#using-scriptfile) ниже.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

В корневой папке проекта существует общий файл [host.json](functions-host-json.md), который может использоваться для настройки приложения-функции. У каждой функции есть папка с собственным файлом кода (JS) и файлом конфигурации привязки (function.json). Имя родительского каталога файла `function.json` всегда является именем этой функции.

Расширения привязки, необходимые в [версии 2.x](functions-versions.md) среды выполнения Функций, определены в файле `extensions.csproj` с фактическими файлами библиотеки в папке `bin`. При локальной разработке необходимо [зарегистрировать расширения привязки](./functions-bindings-register.md#extension-bundles). При разработке функций на портале Azure эта регистрация выполняется автоматически.

## <a name="exporting-a-function"></a>Экспорт функции

Функции JavaScript должны экспортироваться через [ `module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (или [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Экспортированная функция должна быть функцией JavaScript, которая выполняется при активации.

По умолчанию среда выполнения Функций ищет функцию в файле `index.js`, где `index.js` использует тот же родительский каталог, что и соответствующий файл `function.json`. В стандартном случае экспортированная функция должна быть единственным экземпляром экспорта из соответствующего файла (экспорта с именем `run` или `index`). Чтобы настроить расположение файла и имя экспорта функции, ознакомьтесь с разделом ниже о [настройке точки входа функции](functions-reference-node.md#configure-function-entry-point).

Экспортированной функции передается число аргументов при выполнении. Первый аргумент, который она всегда принимает, — это объект `context`. If your function is synchronous (doesn't return a Promise), you must pass the `context` object, as calling `context.done` is required for correct use.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Экспорт асинхронной функции
При использовании объявления[`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) или простых [обещаний](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) JavaScript в версии 2.х среды выполнения Функций вам не нужно явно вызывать [`context.done`](#contextdone-method), чтобы сообщить, что функция завершена. Ваша функция завершается при завершении экспортированной асинхронной функции или обещания. Для функций, предназначенных для среды выполнения версии 1.x, по-прежнему необходимо вызвать [`context.done`](#contextdone-method) после выполнения кода.

Следующий пример — это простая функция, которая записывает в журнал, что она была запущена, и немедленно завершает выполнение.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

При экспорте асинхронной функции вы также можете настроить выходные привязки, чтобы принять значение `return`. Мы рекомендуем выполнять этот шаг, если существует только одна выходная привязка.

Чтобы назначить выходные данные с помощью `return`, измените свойство `name` на `$return` в `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

В этом случае функция должна выглядеть следующим образом:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Привязки 
В JavaScript [привязки](functions-triggers-bindings.md) настраиваются и определяются в файле function.json функции. Функции взаимодействуют с привязками несколькими способами.

### <a name="inputs"></a>Входные данные
Входные данные в Функциях Azure делятся на две категории: входные данные от триггера и дополнительные входные данных. Привязки триггера и другие привязки для ввода (привязки `direction === "in"`) могут считываться функцией тремя способами.
 - **_[Рекомендуется.]_ Как параметры, передаваемые функции.** Они передаются в функцию в том же порядке, в каком они определены в файле *function.json*. The `name` property defined in *function.json* does not need to match the name of your parameter, although it should.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Как элементы объекта [`context.bindings`](#contextbindings-property).** Каждый элемент назван по свойству `name`, определенному в файле *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **В качестве входных данных при использовании объекта JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx).** Этот способ по сути идентичен передаче входных данных в качестве параметров, но позволяет динамически обрабатывать входные данные.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>outputs
Выходные данные (привязки `direction === "out"`) могут быть записаны в функцию несколькими способами. Во всех случаях свойство `name` привязки, определенное в файле *function.json*, соответствует имени элемента объекта, записанного в функции. 

You can assign data to output bindings in one of the following ways (don't combine these methods):

- **_[Рекомендуется, если существует несколько экземпляров выходных данных.]_ Возврат объекта.** If you are using an async/Promise returning function, you can return an object with assigned output data. В приведенном ниже примере выходные привязки в файле *function.json* называются httpResponse и queueOutput.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Если вы используете синхронную функцию, вы можете вернуть этот объект с помощью [`context.done`](#contextdone-method) (см. пример).
- **_[Рекомендуется для одного экземпляра выходных данных.]_ Возвращает значение напрямую и с использованием имени привязки $return.** Этот метод работает для функций возврата (асинхронной или функции обещаний). См. пример в разделе [Экспорт асинхронной функции](#exporting-an-async-function). 
- **Присвоение значений для свойства `context.bindings`** . Вы можете присвоить значения непосредственно для context.bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Тип привязки данных

Чтобы определить тип данных для входной привязки, используйте свойство `dataType` в определении привязки. Например, чтобы прочитать содержимое HTTP-запроса в двоичном формате, используйте тип `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Параметры для `dataType` — это `binary`, `stream` и `string`.

## <a name="context-object"></a>Объект context
Среда выполнения использует объект `context` для передачи данных в функцию и из нее, а также для взаимодействия со средой выполнения. Объект контекста может использоваться для чтения и настройки данных из привязок, записи журналов и применения обратного вызова `context.done`, если экспортированная функция является синхронной.

Объект `context` всегда является первым параметром функции. Он должен быть включен, так как содержит важные методы, такие как `context.done` и `context.log`. Для этого объекта можно указать любое имя (например, `ctx` или `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Свойство context.bindings

```js
context.bindings
```

Returns a named object that is used to read or assign binding data. Input and trigger binding data can be accessed by reading properties on `context.bindings`. Output binding data can be assigned by adding data to `context.bindings`

Например, следующие определения привязки в function.json позволяют вам получить доступ к содержимому очереди из `context.bindings.myInput` и назначить выходные данные очереди, используя `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Вы можете определить выходные данные привязки с помощью метода `context.done` вместо объекта `context.binding` (см. ниже).

### <a name="contextbindingdata-property"></a>Свойство context.bindingData

```js
context.bindingData
```

Это свойство возвращает именованный объект, содержащий метаданные триггера и данные вызова функции (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Пример метаданных триггера см. [по этой ссылке с примером для концентраторов событий](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Метод context.done

```js
context.done([err],[propertyBag])
```

Позволяет среде выполнения узнать, что ваш код выполнен. Если функция использует объявление [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function), метод `context.done()` не требуется. Обратный вызов `context.done` выполняется неявным образом. Асинхронные функции доступны в Node 8 или более поздней версии, для которой требуется среда выполнения Функций версии 2.x.

Если функция не является асинхронной, **необходимо вызвать метод**  `context.done`, чтобы сообщить среде выполнения, что функция выполнена. Если этот метод отсутствует, истекает время ожидания выполнения.

Метод `context.done` позволяет передавать в среду выполнения и определяемые пользователем сообщения об ошибке, и объект JSON, содержащий выходные данные привязки. Свойства, переданные в `context.done`, перезапишут любые значения, заданные для объекта `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Метод context.log  

```js
context.log(message)
```

Этот метод позволяет делать записи в потоковые журналы функций на уровне трассировки по умолчанию. В `context.log` доступны дополнительные методы ведения журнала, позволяющие выполнять запись в журналы функций на других уровнях трассировки:


| Метод                 | Описание                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Записывает сообщение в журнал на уровне ошибок или более низком.   |
| **warn(_message_)**    | Записывает сообщение в журнал на уровне предупреждений или более низком. |
| **info(_message_)**    | Записывает сообщение в журнал на уровне сведений или более низком.    |
| **verbose(_message_)** | Записывает сообщение в журнал на уровне детализации.           |

Следующий пример записывает в журнал на уровне трассировки "предупреждения":

```javascript
context.log.warn("Something has happened."); 
```

Вы можете [настроить пороговое значение уровня трассировки для ведения журнала](#configure-the-trace-level-for-console-logging) в файле host.json. Дополнительные сведения о записи журналов см. в разделе о [записях выходных данных трассировки](#writing-trace-output-to-the-console) ниже.

Дополнительные сведения о просмотре журналов функций и обращении к ним см. в статье [мониторинг Функций Azure](functions-monitoring.md).

## <a name="writing-trace-output-to-the-console"></a>Вывод выходных данных трассировки на консоль 

В Azure Functions воспользуйтесь методами `context.log` для записи выходных данных трассировки в консоль. В Функциях версии 2.x трассировка выходных данных с помощью `console.log` регистрируется на уровне приложения-функции. This means that outputs from `console.log` are not tied to a specific function invocation and aren't displayed in a specific function's logs. Тем не менее они распространяются в Application Insights. В Функциях версии 1.x для записи в консоль нельзя использовать метод `console.log`.

При вызове `context.log()` ваше сообщение записывается в консоль на уровне трассировки по умолчанию — уровень _сведений_. Следующий код записывает на консоль на уровне трассировки "сведения":

```javascript
context.log({hello: 'world'});  
```

Этот код эквивалентен приведенному выше коду:

```javascript
context.log.info({hello: 'world'});  
```

Этот код записывает в консоль на уровне трассировки "ошибки":

```javascript
context.log.error("An error has occurred.");  
```

Так как _уровень ошибок_ является наивысшим уровнем трасировки, эта трассировка записывается в выходные данные на всех уровнях трассировки при условии, что ведение журнала включено.

Все методы `context.log` поддерживают тот же формат параметров, что и метод Node.js [util.format](https://nodejs.org/api/util.html#util_util_format_format). Просмотрите следующий код, который выполняет запись в журналы функций, используя уровень трассировки по умолчанию:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Этот же код можно записать в таком формате:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Настройка уровня трассировки для ведения журнала консоли

В Функциях Azure 1.x можно определить пороговое значение уровня трассировки для записи в консоль, чтобы легко контролировать, как трассировки записываются в консоль из ваших функций. Чтобы задать пороговое значение для всех трассировок, которые записываются в консоль, используйте свойство `tracing.consoleLevel` в файле host.json. Этот параметр применяется ко всем функциям в приложении-функции. В следующем примере задается пороговое значение трассировки, чтобы включить подробное ведение журнала:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Значения **consoleLevel** соответствуют именам методов в `context.log`. Чтобы отключить ведение журнала трассировки в консоли, задайте для параметра **consoleLevel** значение _off_. Дополнительные сведения см. в [справочной статье о host.json](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Триггеры и привязки HTTP

Триггеры HTTP и webhook, а также привязки вывода HTTP используют объекты запроса и ответа для обмена сообщениями HTTP.  

### <a name="request-object"></a>Объект запроса

У объекта (запроса) `context.req` есть следующие свойства:

| Свойство      | Описание                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Объект, содержащий текст запроса.               |
| _headers_     | Объект, содержащий заголовок запроса.                   |
| _method_      | Метод HTTP, используемый для запроса.                                |
| _originalUrl_ | URL-адрес запроса.                                        |
| _params_      | Объект, содержащий параметры маршрутизации запроса. |
| _query_       | Объект, содержащий параметры запроса.                  |
| _rawBody_     | Текст сообщения в виде строки.                           |


### <a name="response-object"></a>Объект ответа

У объекта (ответа) `context.res` есть следующие свойства:

| Свойство  | Описание                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Объект, содержащий текст ответа.         |
| _headers_ | Объект, содержащий заголовок ответа.             |
| _isRaw_   | Указывает, что форматирование пропускается для ответа.    |
| _состояние_  | Код состояния HTTP ответа.                     |

### <a name="accessing-the-request-and-response"></a>Доступ к запросу и ответу 

При работе с триггерами HTTP вы можете получить доступ к объектам запроса и ответа HTTP одним из нескольких способов.

+ **От свойств `req` и `res` объекта `context`.** В этом случае можно использовать обычный шаблон доступа к данным HTTP из объекта context вместо полного шаблона `context.bindings.name`. Следующий пример демонстрирует доступ к объектам `req` и `res` в `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **С помощью именованных входных и выходных привязок.** В этом случае триггер и привязки HTTP работают как любая другая привязка. В следующем примере объект ответа задается с помощью именованной привязки `response`: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Только ответ.]_ Путем вызова `context.res.send(body?: any)`.** HTTP-ответ создается с входными данными `body` в качестве текста ответа. `context.done()` вызывается неявным образом.

+ **_[Только ответ.]_ Путем вызова `context.done()`.** A special type of HTTP binding returns the response that is passed to the `context.done()` method. Следующая привязка вывода HTTP определяет параметр вывода `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Версия узла

В следующей таблице показана версия Node.js, используемая каждой основной версией среды выполнения службы "Функции".

| Версия службы "Функции" | Версия Node.js | 
|---|---|
| 1.x | 6.11.2 (заблокировано средой выполнения) |
| 2.x  | _Active LTS_ and _Maintenance LTS_ Node.js versions (~10 recommended). Target the version in Azure by setting the WEBSITE_NODE_DEFAULT_VERSION [app setting](functions-how-to-use-azure-function-app-settings.md#settings) to `~10`.|

Введя `process.version` из любой функции или путем проверки параметра приложения, указанного выше, можно увидеть текущую версию, которую использует среда выполнения.

## <a name="dependency-management"></a>Управление зависимостями
Чтобы использовать библиотеки сообщества в коде JavaScript, как показано в следующем примере, необходимо убедиться, что установлены все зависимости приложения-функции в Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Следует определить файл `package.json` в корне вашего приложения-функции. После этого все функции в приложении будут совместно использовать одни и те же кэшированные пакеты, что обеспечивает наилучшую производительность. При возникновении конфликтов версий их можно разрешить, добавив файл `package.json` в папку определенной функции.  

При развертывании приложения-функции из системы управления версиями любой файл `package.json`, присутствующий в вашем репозитории, вызовет `npm install` в своей папке во время развертывания. Но при развертывании с помощью портала или интерфейса командной строки вам придется вручную устанавливать пакеты.

Существует два способа установки пакетов в приложение-функцию. 

### <a name="deploying-with-dependencies"></a>Развертывание с зависимостями
1. Установите все необходимые пакеты в локальной среде, запустив `npm install`.

2. Разверните свой код и убедитесь, что папка `node_modules` включена в развертывание. 


### <a name="using-kudu"></a>С помощью Kudu
1. Перейдите на сайт `https://<function_app_name>.scm.azurewebsites.net`.

2. Щелкните **Debug Console** (Консоль отладки)  > **CMD**.

3. Перейдите к `D:\home\site\wwwroot`, а затем перетащите файл package.json в папку **wwwroot** в верхней части страницы.  
    Существуют другие способы передачи файлов в приложение-функцию. Дополнительные сведения см. в разделе [Как обновить файлы приложения-функции](functions-reference.md#fileupdate). 

4. После загрузки файла package.json запустите команду `npm install` в **консоли удаленного выполнения Kudu**.  
    В результате этого действия будут загружены пакеты, указанные в файле package.json, и перезапущено приложение-функция.

## <a name="environment-variables"></a>Переменные среды

В Функциях [параметры приложения](functions-app-settings.md), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. You can access these settings using `process.env`, as shown here in the second and third calls to `context.log()` where we log the `AzureWebJobsStorage` and `WEBSITE_SITE_NAME` environment variables:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

При локальном запуске приложения параметры считываются из файла проекта [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="configure-function-entry-point"></a>Настройка точки входа функции

Свойства `function.json`, а именно `scriptFile` и `entryPoint`, позволяют настроить расположение и имя экспортированной функции. Эти свойства важны, если JavaScript является транскомпилированным.

### <a name="using-scriptfile"></a>Использование `scriptFile`

По умолчанию функция JavaScript выполняется из файла `index.js`, который расположен в том же родительском каталоге, что и соответствующий файл `function.json`.

`scriptFile` можно использовать для получения структуры папок, которая выглядит следующим образом:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Файл `function.json` для `myNodeFunction` должен включать свойство `scriptFile`, указывающее на файл с экспортированной функцией, которую нужно выполнить.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Использование `entryPoint`

В `scriptFile` (или `index.js`) функции должны экспортироваться с использованием `module.exports`, чтобы их можно было найти и запустить. По умолчанию функция, которая выполняется при запуске, является единственным экземпляром экспорта (с именем `run` или `index`) из этого файла.

Такой экспорт можно настроить, используя `entryPoint` в файле `function.json`, как показано в следующем примере:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

В среде Функций версии 2.x, которая поддерживает параметр `this` в пользовательских функциях, код этой самой функции может выглядеть следующим образом:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

In this example, it is important to note that although an object is being exported, there are no guarantees for preserving state between executions.

## <a name="local-debugging"></a>Local Debugging

When started with the `--inspect` parameter, a Node.js process listens for a debugging client on the specified port. In Azure Functions 2.x, you can specify arguments to pass into the Node.js process that runs your code by adding the environment variable or App Setting `languageWorkers:node:arguments = <args>`. 

To debug locally, add `"languageWorkers:node:arguments": "--inspect=5858"` under `Values` in your [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) file and attach a debugger to port 5858.

When debugging using VS Code, the `--inspect` parameter is automatically added using the `port` value in the project's launch.json file.

In version 1.x, setting `languageWorkers:node:arguments` will not work. The debug port can be selected with the [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parameter on Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

When you target version 2.x of the Functions runtime, both [Azure Functions for Visual Studio Code](functions-create-first-function-vs-code.md) and the [Azure Functions Core Tools](functions-run-local.md) let you create function apps using a template that support TypeScript function app projects. The template generates `package.json` and `tsconfig.json` project files that make it easier to transpile, run, and publish JavaScript functions from TypeScript code with these tools.

A generated `.funcignore` file is used to indicate which files are excluded when a project is published to Azure.  

TypeScript files (.ts) are transpiled into JavaScript files (.js) in the `dist` output directory. TypeScript templates use the [`scriptFile` parameter](#using-scriptfile) in `function.json` to indicate the location of the corresponding .js file in the `dist` folder. The output location is set by the template by using `outDir` parameter in the `tsconfig.json` file. If you change this setting or the name of the folder, the runtime is not able to find the code to run.

> [!NOTE]
> Experimental support for TypeScript exists version 1.x of the Functions runtime. The experimental version transpiles TypeScript files into JavaScript files when the function is invoked. In version 2.x, this experimental support has been superseded by the tool-driven method that does transpilation before the host is initialized and during the deployment process.

The way that you locally develop and deploy from a TypeScript project depends on your development tool.

### <a name="visual-studio-code"></a>Код Visual Studio

The [Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extension lets you develop your functions using TypeScript. The Core Tools is a requirement of the Azure Functions extension.

To create a TypeScript function app in Visual Studio Code, choose `TypeScript` as your language when you create a function app.

When you press **F5** to run the app locally, transpilation is done before the host (func.exe) is initialized. 

When you deploy your function app to Azure using the **Deploy to function app...** button, the Azure Functions extension first generates a production-ready build of JavaScript files from the TypeScript source files.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

There are several ways in which a TypeScript project differs from a JavaScript project when using the Core Tools.

#### <a name="create-project"></a>Создание проекта

To create a TypeScript function app project using Core Tools, you must specify the TypeScript language option when you create your function app. You can do this in one of the following ways:

- Run the `func init` command, select `node` as your language stack, and then select `typescript`.

- Выполните команду `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Run local

To run your function app code locally using Core Tools, use the following commands instead of `func host start`: 

```command
npm install
npm start
```

The `npm start` command is equivalent to the following commands:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Публикация в Azure

Before you use the [`func azure functionapp publish`] command to deploy to Azure, you create a production-ready build of JavaScript files from the TypeScript source files. 

The following commands prepare and publish your TypeScript project using Core Tools: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

In this command, replace `<APP_NAME>` with the name of your function app.

## <a name="considerations-for-javascript-functions"></a>Рекомендации для функций JavaScript

При работе с функциями JavaScript следует помнить о рекомендациях, приведенных в следующих разделах.

### <a name="choose-single-vcpu-app-service-plans"></a>Выбор планов службы приложений для конфигурации с одним виртуальным ЦП

При создании приложения-функции, которое использует план службы приложения, мы советуем выбирать план для конфигурации с одним виртуальным ЦП вместо плана для нескольких виртуальных ЦП. Сейчас служба "Функции Azure" намного эффективнее выполняет функции JavaScript на виртуальных машинах с одним ЦП. Использование более крупных виртуальных машин не приводит к ожидаемому улучшению производительности. When necessary, you can manually scale out by adding more single-vCPU VM instances, or you can enable autoscale. Дополнительные сведения см. в статье [Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Холодный запуск

С разработкой Функций Azure в виде бессерверной модели размещения холодные запуски стали реальностью. *Холодный запуск* обозначает, что когда ваше приложение-функция запускается в первый раз после периода отсутствия активности, запуск займет больше времени. Для функций JavaScript, в частности с большими деревьями зависимостей, холодный запуск может оказаться достаточно длительным. Чтобы ускорить процесс холодного запуска, по возможности [выполняйте функции в виде файла пакета](run-functions-from-deployment-package.md). Многие методы развертывания используют эту модель по умолчанию, но если возникают большие временные задержки при холодном запуске и он не выполняется из файла пакета, запуск в виде файла пакета может значительно ускорить процесс.

### <a name="connection-limits"></a>Connection Limits

When you use a service-specific client in an Azure Functions application, don't create a new client with every function invocation. Instead, create a single, static client in the global scope. For more information, see [managing connections in Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Use `async` and `await`

When writing Azure Functions in JavaScript, you should write code using the `async` and `await` keywords. Writing code using `async` and `await` instead of callbacks or `.then` and `.catch` with Promises helps avoid two common problems:
 - Throwing uncaught exceptions that [crash the Node.js process](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potentially affecting the execution of other functions.
 - Unexpected behavior, such as missing logs from context.log, caused by asynchronous calls that are not properly awaited.

In the example below, the asynchronous method `fs.readFile` is invoked with an error-first callback function as its second parameter. This code causes both of the issues mentioned above. An exception that is not explicitly caught in the correct scope crashed the entire process (issue #1). Calling `context.done()` outside of the scope of the callback function means that the function invocation may end before the file is read (issue #2). In this example, calling `context.done()` too early results in missing log entries starting with `Data from file:`.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Using the `async` and `await` keywords helps avoid both of these errors. You should use the Node.js utility function [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) to turn error-first callback-style functions into awaitable functions.

In the example below, any unhandled exceptions thrown during the function execution only fail the individual invocation that raised an exception. The `await` keyword means that steps following `readFileAsync` only execute after `readFile` is complete. With `async` and `await`, you also don't need to call the `context.done()` callback.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

+ [Рекомендации по функциям Azure](functions-best-practices.md)
+ [Справочник разработчика по функциям Azure](functions-reference.md)
+ [Триггеры и привязки в функциях Azure](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
