---
title: Ссылка разработчика JavaScript для функций Azure
description: Узнайте, как разрабатывать функции с помощью JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: 345df8e1ea88caa6f8dbe941245c1f989c3e81c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276833"
---
# <a name="azure-functions-javascript-developer-guide"></a>Руководство разработчика JavaScript для Функций Azure

Это руководство содержит сведения о сложностях написания Функций Azure на языке JavaScript.

Функция JavaScript — это экспортированная `функция` , которая `function` выполняется при активации ([триггеры настраиваются в файле function.json](functions-triggers-bindings.md)). Первым аргументом, передаваемым каждой `context` функции, является объект, который используется для получения и отправки обязательных данных, регистрации и связи с временем выполнения.

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по Функциям Azure](functions-reference.md). Завершите быстрый запуск функций для создания первой функции, используя [Visual Studio Code](functions-create-first-function-vs-code.md) или на [портале.](functions-create-first-azure-function.md)

Эта статья также поддерживает [разработку приложения TypeScript](#typescript).

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

Функции JavaScript должны [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) экспортироваться через (или). [`exports`](https://nodejs.org/api/modules.html#modules_exports) Экспортированная функция должна быть функцией JavaScript, которая выполняется при активации.

По умолчанию среда выполнения Функций ищет функцию в файле `index.js`, где `index.js` использует тот же родительский каталог, что и соответствующий файл `function.json`. В стандартном случае экспортированная функция должна быть единственным экземпляром экспорта из соответствующего файла (экспорта с именем `run` или `index`). Чтобы настроить расположение файла и имя экспорта функции, ознакомьтесь с разделом ниже о [настройке точки входа функции](functions-reference-node.md#configure-function-entry-point).

Экспортированной функции передается число аргументов при выполнении. Первый аргумент, который она всегда принимает, — это объект `context`. Если функция синхронна (не возвращает Обещание), вы `context` должны пройти `context.done` объект, так как вызов необходим для правильного использования.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Экспорт асинхронной функции
При использовании [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) декларации или простого JavaScript [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) в версии 2.x времени [`context.done`](#contextdone-method) выполнения функций не нужно явно вызывать обратный вызов, чтобы сигнализировать о завершении функции. Ваша функция завершается при завершении экспортированной асинхронной функции или обещания. Для функций, ориентированных на время выполнения [`context.done`](#contextdone-method) версии 1.x, необходимо вызвать выполнение кода.

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
 - **_[Рекомендуется.]_ Как параметры, передаваемые функции.** Они передаются в функцию в том же порядке, в каком они определены в файле *function.json*. Свойство, `name` определяемое в *function.json,* не должно соответствовать названию параметра, хотя и должно.
 
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

Вы можете назначить данные выходным привязки одним из следующих способов (не объединяйте эти методы):

- **_[Рекомендуется, если существует несколько экземпляров выходных данных.]_ Возврат объекта.** Если вы используете функцию возврата async/Promise, можно вернуть объект с назначенными выходными данными. В приведенном ниже примере выходные привязки в файле *function.json* называются httpResponse и queueOutput.

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

  Если вы используете синхронную функцию, [`context.done`](#contextdone-method) вы можете вернуть этот объект с помощью (см. пример).
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

Возвращает названный объект, который используется для чтения или присвоения связывающих данных. Доступ к входным и триггерным связывающим данным можно получить, считывая свойства на. `context.bindings` Данные связывающей вывод ныедек могут быть назначены путем добавления данных в`context.bindings`

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

Это свойство возвращает именованный объект, содержащий метаданные триггера и данные вызова функции (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Пример метаданных триггера см. [по этой ссылке с примером для концентраторов событий](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>Метод context.done

```js
context.done([err],[propertyBag])
```

Позволяет среде выполнения узнать, что ваш код выполнен. Когда функция использует [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) декларацию, вам не `context.done()`нужно использовать . Обратный вызов `context.done` выполняется неявным образом. Асинхронные функции доступны в Node 8 или более поздней версии, для которой требуется среда выполнения Функций версии 2.x.

Если функция async не является функцией async, **необходимо позвонить,** `context.done` чтобы сообщить время выполнения, что функция завершена. Если этот метод отсутствует, истекает время ожидания выполнения.

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
| **ошибка _(сообщение)_**   | Записывает сообщение в журнал на уровне ошибок или более низком.   |
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

В Azure Functions воспользуйтесь методами `context.log` для записи выходных данных трассировки в консоль. В Функциях версии 2.x трассировка выходных данных с помощью `console.log` регистрируется на уровне приложения-функции. Это означает, что `console.log` выходы из не привязаны к определенному вызову функции и не отображаются в журналах определенной функции. Тем не менее они распространяются в Application Insights. В Функциях версии 1.x для записи в консоль нельзя использовать метод `console.log`.

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
| _Тела_        | Объект, содержащий текст запроса.               |
| _Заголовки_     | Объект, содержащий заголовок запроса.                   |
| _Метод_      | Метод HTTP, используемый для запроса.                                |
| _originalUrl_ | URL-адрес запроса.                                        |
| _params_      | Объект, содержащий параметры маршрутизации запроса. |
| _Запроса_       | Объект, содержащий параметры запроса.                  |
| _rawBody_     | Текст сообщения в виде строки.                           |


### <a name="response-object"></a>Объект ответа

У объекта (ответа) `context.res` есть следующие свойства:

| Свойство  | Описание                                               |
| --------- | --------------------------------------------------------- |
| _Тела_    | Объект, содержащий текст ответа.         |
| _Заголовки_ | Объект, содержащий заголовок ответа.             |
| _isRaw_   | Указывает, что форматирование пропускается для ответа.    |
| _состояние_  | Код состояния HTTP ответа.                     |
| _Печенье_ | Массив объектов cookie HTTP, которые установлены в ответе. Объект cookie HTTP `name`имеет `value`свойства , и другие свойства cookie, такие как `maxAge` или `sameSite`. |

### <a name="accessing-the-request-and-response"></a>Доступ к запросу и ответу 

При работе с триггерами HTTP вы можете получить доступ к объектам запроса и ответа HTTP одним из нескольких способов.

+ **От свойств `req` и `res` объекта `context`.** В этом случае можно использовать обычный шаблон доступа к данным HTTP из объекта context вместо полного шаблона `context.bindings.name`. Следующий пример демонстрирует доступ к объектам `req` и `res` в `context`:

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
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

+ **_[Только ответ.]_ Путем вызова `context.done()`.** Особый тип привязки HTTP возвращает `context.done()` ответ, который передается методу. Следующая привязка вывода HTTP определяет параметр вывода `$return`:

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

## <a name="scaling-and-concurrency"></a>Масштабирование и параллелизм

По умолчанию Azure Functions автоматически отслеживает нагрузку на приложение и создает дополнительные экземпляры узлов для Node.js по мере необходимости. Функции используют встроенные (не настраиваемые пользователем) пороги для различных типов триггеров, чтобы решить, когда добавлять экземпляры, такие как возраст сообщений и размер очереди для очереди Trigger. Для получения дополнительной [How the Consumption and Premium plans work](functions-scale.md#how-the-consumption-and-premium-plans-work)информации см.

Такое поведение масштабирования является достаточным для многих приложений Node.js. Для приложений, привязанных к цП, можно повысить производительность, используя несколько процессов работы с языковыми процессорами.

По умолчанию каждый экземпляр хоста Функции имеет единый процесс работы на языке. Можно увеличить количество рабочих процессов на узла (до 10) с помощью настройки [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) приложения. Затем Azure Functions пытается равномерно распределить одновременные вызовы функций между этими сотрудниками. 

В FUNCTIONS_WORKER_PROCESS_COUNT применяется к каждому узлам, который создает Функции при масштабировании приложения для удовлетворения спроса. 

## <a name="node-version"></a>Версия узла

В следующей таблице показаны текущие поддерживаемые версии Node.js для каждой основной версии времени выполнения функций, с помощью операционной системы:

| Версия службы "Функции" | Версия узла (Windows) | Узла версия (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (заблокировано средой выполнения) | Недоступно |
| 2.x  | No8<br/>10 евро (рекомендуется)<br/>No12<sup>*</sup> | No 8 (рекомендуется)<br/>10 евро  |
| 3.x | 10 евро<br/>No 12 (рекомендуется)  | 10 евро<br/>No 12 (рекомендуется) |

<sup>*</sup>Узла No12 в настоящее время разрешено в версии 2.x времени выполнения функций. Однако для наилучшей производительности мы рекомендуем использовать версию 3.x функции выполнения с узлами No12. 

Введя `process.version` из любой функции или путем проверки параметра приложения, указанного выше, можно увидеть текущую версию, которую использует среда выполнения. Целевой версии в Azure, установив [настройку](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION приложения `~10`на поддерживаемую версию LTS, например.

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
1. Перейдите к `https://<function_app_name>.scm.azurewebsites.net`.

2. Нажмите **Debug Консоль** > **CMD**.

3. Перейдите к `D:\home\site\wwwroot`, а затем перетащите файл package.json в папку **wwwroot** в верхней части страницы.  
    Существуют другие способы передачи файлов в приложение-функцию. Дополнительные сведения см. в разделе [Как обновить файлы приложения-функции](functions-reference.md#fileupdate). 

4. После загрузки файла package.json запустите команду `npm install` в **консоли удаленного выполнения Kudu**.  
    В результате этого действия будут загружены пакеты, указанные в файле package.json, и перезапущено приложение-функция.

## <a name="environment-variables"></a>Переменные среды

В Функциях [параметры приложения](functions-app-settings.md), такие как строки подключения службы, доступны в виде переменных среды во время выполнения. Вы можете получить доступ `process.env`к этим параметрам, используя, как показано здесь во втором и третьем вызовах, `context.log()` где мы регистрируем переменные `AzureWebJobsStorage` среды: `WEBSITE_SITE_NAME`

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

В этом примере важно отметить, что, хотя объект экспортируется, нет никаких гарантий сохранения состояния между выполнением.

## <a name="local-debugging"></a>Локальная дебудирование

При начале `--inspect` работы с параметром процесс Node.js прослушивает отладку клиента в указанном порту. В Azure Functions 2.x можно указать аргументы для прохождения в процесс Node.js, `languageWorkers:node:arguments = <args>`который запускает ваш код, добавив переменную среды или настройку приложения. 

Чтобы отладить локально, `"languageWorkers:node:arguments": "--inspect=5858"` `Values` добавьте под в файл [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) и прикрепите отладчик к порту 5858.

При отладке с `--inspect` использованием VS Code `port` параметр автоматически добавляется с использованием значения в файле launch.json проекта.

В версии 1.x настройка `languageWorkers:node:arguments` не работает. Порт отладки может быть [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) выбран с параметром на основных инструментах функций Azure.

## <a name="typescript"></a>TypeScript

При таргетинге версии 2.x времени выполнения функций, как [Функции Azure для визуального кода Studio,](functions-create-first-function-vs-code.md) так и [основные инструменты Azure Functions](functions-run-local.md) позволяют создавать функциональные приложения с помощью шаблона, поддерживающего проекты приложений функции TypeScript. Шаблон генерирует `package.json` и `tsconfig.json` проектирует файлы, которые упрощают транспиле, запуск и публикацию функций JavaScript из кода TypeScript с помощью этих инструментов.

Генерируемый `.funcignore` файл используется для указания того, какие файлы исключаются при публикации проекта в Azure.  

Файлы TypeScript (.ts) транскапливаются в файлы JavaScript (.js) в каталоге `dist` вывода. Шаблоны TypeScript используют `function.json` [ `scriptFile` параметр](#using-scriptfile) для обозначения местоположения соответствующего `dist` файла .js в папке. Местона вывода устанавливается шаблоном `outDir` с `tsconfig.json` помощью параметра в файле. При изменении этой настройки или имени папки время выполнения не сможет найти код для запуска.

> [!NOTE]
> Экспериментальная поддержка TypeScript существует версия 1.x времени выполнения функций. Экспериментальная версия transpiles TypeScript файлы в JavaScript файлы, когда функция вызывается. В версии 2.x эта экспериментальная поддержка была заменена инструментированным методом, который делает транспилацию до инициализации узла и во время процесса развертывания.

Способ локальной разработки и развертывания проекта TypeScript зависит от инструмента разработки.

### <a name="visual-studio-code"></a>Visual Studio Code

Расширение [Azure Functions для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) позволяет разрабатывать функции с помощью TypeScript. Основные инструменты являются требованием расширения функций Azure.

Чтобы создать приложение функции TypeScript в `TypeScript` Visual Studio Code, выберите язык в качестве языка при создании приложения функции.

При нажатии **F5** для локального запуска приложения транспилляция выполняется до того, как хост (func.exe) будет инициализирован. 

При развертывании приложения функции в Azure с помощью кнопки **«Развертывание функциональных функций...** расширение Функции Azure сначала генерирует готовую к производству сборку файлов JavaScript из исходных файлов TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Существует несколько способов, с помощью которых проект TypeScript отличается от проекта JavaScript при использовании основных инструментов.

#### <a name="create-project"></a>Создание проекта

Чтобы создать проект приложения функции TypeScript с использованием основных инструментов, необходимо указать вариант языка TypeScript при создании приложения функции функции TypeScript. Вы можете сделать это одним из следующих способов:

- Выполнить `func init` команду, `node` выбрать в качестве стек языка, а затем выбрать. `typescript`

- Выполните команду `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Выполнить локальный

Чтобы запустить код приложения функции локально с помощью Основных инструментов, используйте следующие команды `func host start`вместо: 

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

Перед тем, [`func azure functionapp publish`] как использовать команду для развертывания в Azure, необходимо создать готовую к производству сборку файлов JavaScript из исходных файлов TypeScript. 

Следующие команды готовят и публикуют проект TypeScript с помощью основных инструментов: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

В этой команде `<APP_NAME>` замените имя приложения функции.

## <a name="considerations-for-javascript-functions"></a>Рекомендации для функций JavaScript

При работе с функциями JavaScript следует помнить о рекомендациях, приведенных в следующих разделах.

### <a name="choose-single-vcpu-app-service-plans"></a>Выбор планов службы приложений для конфигурации с одним виртуальным ЦП

При создании приложения-функции, которое использует план службы приложения, мы советуем выбирать план для конфигурации с одним виртуальным ЦП вместо плана для нескольких виртуальных ЦП. Сейчас служба "Функции Azure" намного эффективнее выполняет функции JavaScript на виртуальных машинах с одним ЦП. Использование более крупных виртуальных машин не приводит к ожидаемому улучшению производительности. При необходимости можно вручную масштабировать, добавляя больше экземпляров с одним VCPU VM, или включить автомасштаб. Дополнительные сведения см. в статье [Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Холодный запуск

С разработкой Функций Azure в виде бессерверной модели размещения холодные запуски стали реальностью. *Холодный запуск* обозначает, что когда ваше приложение-функция запускается в первый раз после периода отсутствия активности, запуск займет больше времени. Для функций JavaScript, в частности с большими деревьями зависимостей, холодный запуск может оказаться достаточно длительным. Чтобы ускорить процесс холодного запуска, по возможности [выполняйте функции в виде файла пакета](run-functions-from-deployment-package.md). Многие методы развертывания используют эту модель по умолчанию, но если возникают большие временные задержки при холодном запуске и он не выполняется из файла пакета, запуск в виде файла пакета может значительно ускорить процесс.

### <a name="connection-limits"></a>Пределы подключения

При использовании клиента, работаемного в службе, в приложении Azure Functions не создавайте нового клиента с каждым вызовом функции. Вместо этого создайте единого статического клиента в глобальной области. Для получения дополнительной [managing connections in Azure Functions](manage-connections.md)информации см.

### <a name="use-async-and-await"></a>Использование `async` и`await`

При написании функций Azure в JavaScript следует писать код с помощью `async` `await` и ключевых слов. Написание `async` `await` кода с использованием `.then` `.catch` и вместо обратных вызовов или с обещаниями помогает избежать двух распространенных проблем:
 - Бросая непойманные исключения, которые [разбивают процесс Node.js,](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)потенциально влияющие на выполнение других функций.
 - Неожиданное поведение, например, отсутствие журналов из context.log, вызванное асинхронными вызовами, которые не ожидаются должным образом.

В приведенном ниже примере асинхронный метод `fs.readFile` вызывается с функцией обратного вызова первой ошибки в качестве второго параметра. Этот код вызывает обе проблемы, упомянутые выше. Исключение, которое явно не попало в правильную область, разбило весь процесс (проблема #1). Вызов `context.done()` вне сферы действия функции обратного вызова означает, что вызов функции может закончиться до считывания файла (проблема #2). В этом примере вызов `context.done()` слишком ранних `Data from file:`результатов в отсутствующих записях журнала, начиная с .

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

Использование `async` и `await` ключевые слова помогает избежать обеих этих ошибок. Функция утилиты Node.js [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) должна использоваться для превращения функций в стиле обратного вызова с первой ошибкой в функции, которые можно ожидать.

В приведенном ниже примере любые необработанные исключения, брошенные во время выполнения функции, не выполняют отдельные вызовы, которые вызвали исключение. Ключевое `await` слово означает, `readFileAsync` что `readFile` последующие шаги выполняются только после завершения. С `async` `await`и, вам также не нужно `context.done()` вызывать обратный вызов.

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

Дополнительные сведения см. в следующих ресурсах:

+ [Рекомендации по функциям Azure](functions-best-practices.md)
+ [Справочник разработчика по функциям Azure](functions-reference.md)
+ [Триггеры и привязки функций Azure](functions-triggers-bindings.md)

['func лазурный functionapp опубликовать']: functions-run-local.md#project-file-deployment
