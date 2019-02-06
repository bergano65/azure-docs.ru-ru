---
title: Справочник разработчика JavaScript для Функций Azure | Документация Майкрософт
description: Узнайте, как разрабатывать функции с помощью JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 10/26/2018
ms.author: glenga
ms.openlocfilehash: a91778f1646807a092a3c8cda66bd3bd104ff8b5
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55301889"
---
# <a name="azure-functions-javascript-developer-guide"></a>Руководство разработчика JavaScript для Функций Azure

Это руководство содержит сведения о сложностях написания Функций Azure на языке JavaScript.

Функция JavaScript — это экспортированная `функция` , которая `function` выполняется при активации ([триггеры настраиваются в файле function.json](functions-triggers-bindings.md)). Первый аргумент каждой функции передается объекту `context`, который используется для получения и отправки данных привязки, ведения журналов и взаимодействия со средой выполнения.

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Следует также выполнить действия в кратком руководстве по Функциям, чтобы создать первую функцию с помощью [Visual Studio Code](functions-create-first-function-vs-code.md) или [портала](functions-create-first-azure-function.md).

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
 | - bin
```

В корневой папке проекта существует общий файл [host.json](functions-host-json.md), который может использоваться для настройки приложения-функции. У каждой функции есть папка с собственным файлом кода (JS) и файлом конфигурации привязки (function.json). Имя родительского каталога файла `function.json` всегда является именем этой функции.

Расширения привязки, необходимые в [версии 2.x](functions-versions.md) среды выполнения функций, определены в файле `extensions.csproj` с фактическими файлами библиотеки в папке `bin`. При локальной разработке необходимо [зарегистрировать расширения привязки](functions-triggers-bindings.md#local-development-azure-functions-core-tools). При разработке функций на портале Azure эта регистрация выполняется автоматически.

## <a name="exporting-a-function"></a>Экспорт функции

Функции JavaScript должны экспортироваться через [ `module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (или [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Экспортированная функция должна быть функцией JavaScript, которая выполняется при активации.

По умолчанию среда выполнения Функций ищет функцию в файле `index.js`, где `index.js` использует тот же родительский каталог, что и соответствующий файл `function.json`. В стандартном случае экспортированная функция должна быть единственным экземпляром экспорта из соответствующего файла (экспорта с именем `run` или `index`). Чтобы настроить расположение файла и имя экспорта функции, ознакомьтесь с разделом ниже о [настройке точки входа функции](functions-reference-node.md#configure-function-entry-point).

Экспортированной функции передается число аргументов при выполнении. Первый аргумент, который она всегда принимает, — это объект `context`. Если функция является синхронной (не возвращает обещание), необходимо передать объект `context`, так как для корректного использования требуется вызов `context.done`.

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
 - **_[Рекомендуется.]_ Как параметры, передаваемые функции.** Они передаются в функцию в том же порядке, в каком они определены в файле *function.json*. Обратите внимание, что свойство `name`, определенное в файле *function.json*, не должно совпадать с именем параметра, хотя и может.
 
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

Вы можете назначить данные выходным привязкам одним из следующих способов. Не следует объединять эти методы.
- **_[Рекомендуется, если существует несколько экземпляров выходных данных.]_ Возврат объекта.** Если вы используете функцию возврата (асинхронную или функцию обещаний), вы можете возвращать объект с назначенными выходными данными. В приведенном ниже примере выходные привязки в файле *function.json* называются httpResponse и queueOutput.

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
- **Присвоение значений для свойства `context.bindings`**. Вы можете присвоить значения непосредственно для context.bindings.

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

Возвращает именованный объект, который содержит все входные и выходные данные. Например, следующие определения привязки в function.json позволяют вам получить доступ к содержимому очереди из `context.bindings.myInput` и назначить выходные данные очереди, используя `context.bindings.myOutput`.

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

В Azure Functions воспользуйтесь методами `context.log` для записи выходных данных трассировки в консоль. В Функциях версии 2.x трассировка выходных данных с помощью `console.log` регистрируется на уровне приложения-функции. Это означает, что выходные данные из `console.log` не привязаны к вызову определенной функции, и поэтому не отображаются в журналах определенной функции. Тем не менее они распространяются в Application Insights. В Функциях версии 1.x для записи в консоль нельзя использовать метод `console.log`.

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

+ **_[Только ответ.]_ Путем вызова `context.done()`.** Специальный тип привязки HTTP возвращает ответ, передавшийся методу `context.done()`. Следующая привязка вывода HTTP определяет параметр вывода `$return`:

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
| 2.x  | _Активная версия LTS_ и четная _текущая_ версия Node.js (рекомендуются версии 8.11.1 и 10.14.1). Установите версию, используя [параметр приложения](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION.|

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

В Функциях [параметры приложения](functions-app-settings.md), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Вы можете получить доступ к этим параметрам с помощью `process.env`, как показано в функции `GetEnvironmentVariable`:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
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
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Файл `function.json` для `myNodeFunction` должен включать свойство `scriptFile`, указывающее на файл с экспортированной функцией, которую нужно выполнить.

```json
{
  "scriptFile": "../lib/nodeFunction.js",
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

    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

В этом примере важно отметить, что несмотря на выполняемый экспорт объекта, нет никакой гарантии, что состояния между выполнениями будут сохранены.

## <a name="considerations-for-javascript-functions"></a>Рекомендации для функций JavaScript

При работе с функциями JavaScript следует помнить о рекомендациях, приведенных в следующих разделах.

### <a name="choose-single-vcpu-app-service-plans"></a>Выбор планов службы приложений для конфигурации с одним виртуальным ЦП

При создании приложения-функции, которое использует план службы приложения, мы советуем выбирать план для конфигурации с одним виртуальным ЦП вместо плана для нескольких виртуальных ЦП. Сейчас служба "Функции Azure" намного эффективнее выполняет функции JavaScript на виртуальных машинах с одним ЦП. Использование более крупных виртуальных машин не приводит к ожидаемому улучшению производительности. При необходимости вы можете вручную добавить дополнительные экземпляры виртуальных машин с одним ЦП или включить автоматическое масштабирование. Дополнительные сведения см. в статье [Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Поддержка TypeScript и CoffeeScript

Так как прямой поддержки автоматической компиляции TypeScript и CoffeeScript в среде выполнения сейчас нет, соответствующую обработку необходимо осуществлять вне среды выполнения во время развертывания. 

### <a name="cold-start"></a>Холодный запуск

С разработкой Функций Azure в виде бессерверной модели размещения холодные запуски стали реальностью. *Холодный запуск* обозначает, что когда ваше приложение-функция запускается в первый раз после периода отсутствия активности, запуск займет больше времени. Для функций JavaScript, в частности с большими деревьями зависимостей, холодный запуск может оказаться достаточно длительным. Чтобы ускорить процесс холодного запуска, по возможности [выполняйте функции в виде файла пакета](run-functions-from-deployment-package.md). Многие методы развертывания используют эту модель по умолчанию, но если возникают большие временные задержки при холодном запуске и он не выполняется из файла пакета, запуск в виде файла пакета может значительно ускорить процесс.

## <a name="next-steps"></a>Дополнительная информация

Для получения дополнительных сведений см. следующие ресурсы:

+ [Рекомендации по функциям Azure](functions-best-practices.md)
+ [Справочник разработчика по функциям Azure](functions-reference.md)
+ [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
