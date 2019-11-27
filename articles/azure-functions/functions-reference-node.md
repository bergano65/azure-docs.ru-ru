---
title: Справочник разработчика JavaScript по функциям Azure
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

Функция JavaScript — это экспортированная `функция` , которая выполняется при активации (`function`триггеры настраиваются в файле function.json[). Первый аргумент, передаваемый каждой функции, является объектом `context`, который используется для получения и отправки данных привязки, ведения журнала и взаимодействия со средой выполнения.

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Выполните инструкции в кратком руководстве по функциям, чтобы создать свою первую функцию с помощью [Visual Studio Code](functions-create-first-function-vs-code.md) или [на портале](functions-create-first-azure-function.md).

Эта статья также поддерживает [разработку приложений TypeScript](#typescript).

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

Расширения привязки, необходимые в [версии 2.x](functions-versions.md) среды выполнения функций, определены в файле `extensions.csproj` с фактическими файлами библиотеки в папке `bin`. При локальной разработке необходимо [зарегистрировать расширения привязки](./functions-bindings-register.md#extension-bundles). При разработке функций на портале Azure эта регистрация выполняется автоматически.

## <a name="exporting-a-function"></a>Экспорт функции

Функции JavaScript должны экспортироваться через [ `module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (или [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Экспортированная функция должна быть функцией JavaScript, которая выполняется при активации.

По умолчанию среда выполнения Функций ищет функцию в файле `index.js`, где `index.js` использует тот же родительский каталог, что и соответствующий файл `function.json`. В стандартном случае экспортированная функция должна быть единственным экземпляром экспорта из соответствующего файла (экспорта с именем `run` или `index`). Чтобы настроить расположение файла и имя экспорта функции, ознакомьтесь с разделом ниже о [настройке точки входа функции](functions-reference-node.md#configure-function-entry-point).

Экспортированной функции передается число аргументов при выполнении. Первый аргумент, который она всегда принимает, — это объект `context`. Если функция является синхронной (не возвращает обещание), необходимо передать `context` объект, так как для правильного использования требуется вызов `context.done`.

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
 - **_[Рекомендуется.]_ Как параметры, передаваемые функции.** Они передаются в функцию в том же порядке, в каком они определены в файле *function.json*. Свойство `name`, определенное в *Function. JSON* , не обязано совпадать с именем параметра, хотя оно должно.
 
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

### <a name="outputs"></a>Выходные данные
Выходные данные (привязки `direction === "out"`) могут быть записаны в функцию несколькими способами. Во всех случаях свойство `name` привязки, определенное в файле *function.json*, соответствует имени элемента объекта, записанного в функции. 

Вы можете назначить данные выходным привязкам одним из следующих способов (не объединяя эти методы):

- **_[Рекомендуется, если существует несколько экземпляров выходных данных.]_ Возврат объекта.** При использовании функции возврата Async/Promise можно вернуть объект с назначенными выходными данными. В приведенном ниже примере выходные привязки в файле *function.json* называются httpResponse и queueOutput.

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

Чтобы определить тип данных для входной привязки, используйте свойство `dataType` в определении привязки. Например, чтобы считать содержимое HTTP-запроса в двоичном формате, используйте тип `binary`:

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

Возвращает именованный объект, используемый для чтения или назначения данных привязки. Доступ к данным входных и триггеров можно получить, читая свойства `context.bindings`. Данные выходной привязки могут быть назначены путем добавления данных в `context.bindings`

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


| Метод                 | ОПИСАНИЕ                                |
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

В Azure Functions воспользуйтесь методами `context.log` для записи выходных данных трассировки в консоль. В Функциях версии 2.x трассировка выходных данных с помощью `console.log` регистрируется на уровне приложения-функции. Это означает, что выходные данные `console.log` не привязаны к конкретному вызову функции и не отображаются в журналах конкретной функции. Тем не менее они распространяются в Application Insights. В Функциях версии 1.x для записи в консоль нельзя использовать метод `console.log`.

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

| Свойство      | ОПИСАНИЕ                                                    |
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

| Свойство  | ОПИСАНИЕ                                               |
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

+ **_[Только ответ.]_ Путем вызова `context.done()`.** Специальный тип привязки HTTP возвращает ответ, который передается методу `context.done()`. Следующая привязка вывода HTTP определяет параметр вывода `$return`:

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
| 2.x  | _Active LTS_ и _обслуживание LTS_ версии Node. js (рекомендуется ~ 10). Нацеливание на версию в Azure, задав для [параметра приложения](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION значение `~10`.|

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

В Функциях [параметры приложения](functions-app-settings.md), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Вы можете получить доступ к этим параметрам с помощью `process.env`, как показано здесь во втором и третьем вызовах `context.log()`, где мы заносим в журнал переменные среды `AzureWebJobsStorage` и `WEBSITE_SITE_NAME`:

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

В этом примере важно отметить, что хотя экспортируемый объект не гарантирует сохранения состояния между выполнениями.

## <a name="local-debugging"></a>Локальная отладка

При запуске с параметром `--inspect` процесс Node. js прослушивает клиент отладки на указанном порту. В функциях Azure 2. x можно указать аргументы для передачи в процесс Node. js, который выполняет код, добавив переменную среды или параметр приложения `languageWorkers:node:arguments = <args>`. 

Чтобы выполнить отладку локально, добавьте `"languageWorkers:node:arguments": "--inspect=5858"` в разделе `Values` в файле [Local. Settings. JSON](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) и Подключите отладчик к порту 5858.

При отладке с помощью VS Code параметр `--inspect` автоматически добавляется с использованием значения `port` в файле Launch. JSON проекта.

В версии 1. x установка `languageWorkers:node:arguments` не будет работать. Порт отладки можно выбрать с помощью параметра [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) в Azure functions Core Tools.

## <a name="typescript"></a>TypeScript

Если вы используете версию 2. x среды выполнения функций, [функции Azure для Visual Studio Code](functions-create-first-function-vs-code.md) и [Azure functions Core Tools](functions-run-local.md) позволяют создавать приложения-функции с помощью шаблона, поддерживающего проекты приложения-функции TypeScript. Шаблон создает `package.json` и `tsconfig.json` файлы проекта, которые облегчают возможность пересчета, выполнения и публикации функций JavaScript из кода TypeScript с помощью этих средств.

Созданный файл `.funcignore` используется для указания, какие файлы исключаются при публикации проекта в Azure.  

Файлы TypeScript (. TS) преобразуются в файлы JavaScript (JS) в `dist` выходном каталоге. Шаблоны TypeScript используют [параметр`scriptFile`](#using-scriptfile) в `function.json`, чтобы указать расположение соответствующего JS файла в папке `dist`. Расположение выходных данных задается шаблоном с помощью параметра `outDir` в файле `tsconfig.json`. Если изменить этот параметр или имя папки, среда выполнения не сможет найти код для выполнения.

> [!NOTE]
> Экспериментальная поддержка TypeScript существует версии 1. x среды выполнения функций. Экспериментальная версия преобразует файлы TypeScript в файлы JavaScript при вызове функции. В версии 2. x эта экспериментальная поддержка была заменена управляемым средством методом, который выполняет транспилирования до инициализации узла и во время процесса развертывания.

Способ, которым локально разрабатывается и развертывается в проекте TypeScript, зависит от вашего средства разработки.

### <a name="visual-studio-code"></a>Visual Studio Code.

[Функция Azure для расширения Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) позволяет разрабатывать функции с помощью TypeScript. Основные средства являются обязательным требованием к расширению функций Azure.

Чтобы создать приложение-функцию TypeScript в Visual Studio Code, выберите `TypeScript` в качестве языка при создании приложения-функции.

При нажатии клавиши **F5** для локального запуска приложения транспилирования выполняется перед инициализацией узла (Func. exe). 

При развертывании приложения-функции в Azure с помощью кнопки **развернуть в приложении-функцию.** расширение функций Azure сначала создает готовую к работе сборку файлов JavaScript из исходных файлов TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Существует несколько способов, которым проект TypeScript отличается от проекта JavaScript при использовании основных средств.

#### <a name="create-project"></a>Создание проекта

Чтобы создать проект приложения-функции TypeScript с помощью основных средств, необходимо указать параметр языка TypeScript при создании приложения-функции. Это можно сделать одним из следующих способов:

- Выполните команду `func init`, выберите `node` в качестве стека языка, а затем выберите `typescript`.

- Выполните команду `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Запустить локально

Чтобы запустить код приложения функции локально с помощью основных средств, используйте следующие команды вместо `func host start`. 

```command
npm install
npm start
```

Команда `npm start` эквивалентна следующим командам:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Публикация в Azure

Прежде чем использовать команду [`func azure functionapp publish`] для развертывания в Azure, создайте готовую к работе сборку файлов JavaScript из исходных файлов TypeScript. 

Следующие команды подготавливают и публикуют проект TypeScript с помощью основных средств: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

В этой команде замените `<APP_NAME>` именем приложения функции.

## <a name="considerations-for-javascript-functions"></a>Рекомендации для функций JavaScript

При работе с функциями JavaScript следует помнить о рекомендациях, приведенных в следующих разделах.

### <a name="choose-single-vcpu-app-service-plans"></a>Выбор планов службы приложений для конфигурации с одним виртуальным ЦП

При создании приложения-функции, которое использует план службы приложения, мы советуем выбирать план для конфигурации с одним виртуальным ЦП вместо плана для нескольких виртуальных ЦП. Сейчас служба "Функции Azure" намного эффективнее выполняет функции JavaScript на виртуальных машинах с одним ЦП. Использование более крупных виртуальных машин не приводит к ожидаемому улучшению производительности. При необходимости можно вручную развернуть, добавив несколько экземпляров одновиртуальных цпных виртуальных машин или включив Автомасштабирование. Дополнительные сведения см. в статье [Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Холодный запуск

С разработкой Функций Azure в виде бессерверной модели размещения холодные запуски стали реальностью. *Холодный запуск* обозначает, что когда ваше приложение-функция запускается в первый раз после периода отсутствия активности, запуск займет больше времени. Для функций JavaScript, в частности с большими деревьями зависимостей, холодный запуск может оказаться достаточно длительным. Чтобы ускорить процесс холодного запуска, по возможности [выполняйте функции в виде файла пакета](run-functions-from-deployment-package.md). Многие методы развертывания используют эту модель по умолчанию, но если возникают большие временные задержки при холодном запуске и он не выполняется из файла пакета, запуск в виде файла пакета может значительно ускорить процесс.

### <a name="connection-limits"></a>Ограничения подключений

При использовании зависящего от службы клиента в приложении "функции Azure" не создавайте новый клиент при каждом вызове функции. Вместо этого создайте в глобальной области один статический клиент. Дополнительные сведения см. [в статье Управление подключениями в функциях Azure](manage-connections.md).

### <a name="use-async-and-await"></a>Использование `async` и `await`

При написании функций Azure в JavaScript следует писать код, используя ключевые слова `async` и `await`. Написание кода с помощью `async` и `await` вместо обратных вызовов или `.then` и `.catch` с помощью обещания помогает избежать двух распространенных проблем:
 - Создание неперехваченных исключений, которые приводили [к сбою процесса Node. js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), потенциально влияя на выполнение других функций.
 - Непредвиденное поведение, например отсутствующие журналы из context. log, вызванные асинхронными вызовами, которые не ожидаются должным образом.

В приведенном ниже примере асинхронный метод `fs.readFile` вызывается с функцией обратного вызова с ошибками в качестве второго параметра. Этот код вызывает обе проблемы, упомянутые выше. Исключение, которое не было явно перехвачено в правильной области, завершило работу со сбоем всего процесса (#1 проблемы). Вызов `context.done()` вне области действия функции обратного вызова означает, что вызов функции может завершиться до того, как файл будет прочитан (вы#2). В этом примере вызов `context.done()` слишком ранних результатов в отсутствие записей журнала, начинающихся с `Data from file:`.

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

Использование ключевых слов `async` и `await` помогает избежать обеих этих ошибок. Используйте функцию служебной программы Node. js, [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) , чтобы включить функции в стиле обратного вызова с ошибками в ожидающие функции.

В приведенном ниже примере все необработанные исключения, вызванные во время выполнения функции, не вызывают ошибку при отдельном вызове, вызвавшем исключение. Ключевое слово `await` означает, что шаги, следующие `readFileAsync`, выполняются только после завершения `readFile`. При использовании `async` и `await`вам также не нужно вызывать обратный вызов `context.done()`.

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

## <a name="next-steps"></a>Дополнительная информация

Для получения дополнительных сведений см. следующие ресурсы:

+ [Рекомендации по Функциям Azure](functions-best-practices.md)
+ [Справочник разработчика по функциям Azure](functions-reference.md)
+ [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)

[' Func Azure functionapp Publish ']: functions-run-local.md#project-file-deployment
