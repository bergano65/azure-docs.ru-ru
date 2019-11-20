---
title: Тестирование функций Azure
description: Создание автоматических тестов для функции C# в Visual Studio и функции JavaScript в VS Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: функции azure, функции, обработка событий, webhook, динамические вычисления, независимая архитектура, тестирование
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 3c826cd32b38676bcbfe1bec79f580e17a509145
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195973"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Методика тестирования кода с помощью Функций Azure

В этой статье показано, как создавать автоматические тесты для Функций Azure. 

Рекомендуется провести тестирование всего кода, однако наилучший результат можно получить благодаря созданию программы-оболочки для логики функции и создания тестов за ее пределами. Абстрагирование логики ограничивает строки кода функции и позволяет функции нести единоличную ответственность за вызов других классов или модулей. Тем не менее в этой статье показано, как создавать автоматические тесты для HTTP и активируемую по таймеру функцию.

Следующее содержимое разделяется на два различных раздела, что означает, что они предназначены для разных языков и сред. Можно научиться компилировать тесты в:

- [C# в Visual Studio с использованием xUnit](#c-in-visual-studio);
- [JavaScript в VS Code с использованием Jest](#javascript-in-vs-code).

Пример репозитория можно найти на [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# в Visual Studio
В следующем примере описано создание приложения-функции C# в Visual Studio, а также запуск и тестирование с помощью [xUnit](https://xunit.github.io).

![Тестирование Функций Azure с помощью C# в Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Setup

Чтобы настроить среду, создайте функцию и протестируйте приложение. Описанные ниже действия помогут создать приложения и функции, необходимые для поддержки тестов.

1. [Создайте новое приложение-функцию](./functions-create-first-azure-function.md) и назовите его *Functions*.
2. [Создайте функцию HTTP на основе шаблона](./functions-create-first-azure-function.md) и назовите ее *HttpTrigger*.
3. [Создайте функцию таймера на основе шаблона](./functions-create-scheduled-function.md) и назовите ее *TimerTrigger*.
4. [Создайте приложение тестирования xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) в Visual Studio, нажав **Файл > Создать > Проект > Visual C# > .NET Core > Тестовый проект xUnit**, и назовите его *Functions.Test*. 
5. Использование NuGet для добавления ссылок из тестового приложения [Microsoft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Создайте ссылку на приложение *Функции* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) из приложения *Functions.Test*.

### <a name="create-test-classes"></a>Создание тестовых классов

Теперь после создания приложений можно создать классы, используемые для запуска автоматических тестов.

Каждая функция принимает экземпляр [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) для обработки ведения журнала сообщений. Некоторые тесты либо не ведут журнал сообщений, либо для них не имеет значения, как это происходит. Другие тесты должны оценивать сообщения, записанные в журнал, чтобы определить, проходит ли тест.

Класс `ListLogger` предназначен для реализации интерфейса `ILogger` и хранения внутреннего списка сообщений для оценки во время теста.

**Щелкните правой кнопкой мыши** приложение *functions. Test* и выберите **Добавить > класс**, назовите его **NullScope.CS** и введите следующий код:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Затем щелкните **правой кнопкой мыши** приложение *functions. Test* и выберите **Добавить > класс**, назовите его **ListLogger.CS** и введите следующий код:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

Класс `ListLogger` реализует следующие элементы, как предусмотрено интерфейсом `ILogger`.

- **Бегинскопе**: области добавляют контекст в ведение журнала. В этом случае тест просто указывает на статический экземпляр класса `NullScope`, чтобы позволить тесту работать.

- **Включено**: указано значение по умолчанию `false`.

- **Журнал**. Этот метод использует предоставленную функцию `formatter` для форматирования сообщения, а затем добавляет полученный текст в коллекцию `Logs`.

Коллекция `Logs` является экземпляром `List<string>` и инициализируется в конструкторе.

Затем, **щелкните правой кнопкой мыши** на приложение *Functions.Test* и выберите **Добавить > Класс**, назовите его **LoggerTypes.cs** и введите приведенный ниже код.

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
Это перечисление определяет тип средства ведения журнала, используемого в тестах. 

Затем **щелкните правой кнопкой мыши** на приложение *Functions.Test* и выберите **Добавить > Класс**, назовите его **TestFactory.cs** и введите приведенный ниже код.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
Класс `TestFactory` реализует следующие элементы.

- **Данные**. это свойство возвращает коллекцию образцов данных [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) . Пары "ключ-значение" представляют собой значения, которые передаются в строку запроса.

- **Креатедиктионари**: Этот метод принимает пару "ключ-значение" в качестве аргументов и возвращает новый `Dictionary`, используемый для создания `QueryCollection` для представления значений строки запроса.

- **Креатехттпрекуест**. Этот метод создает HTTP-запрос, инициализируемый с помощью заданных параметров строки запроса.

- **Креателогжер**: в зависимости от типа средства ведения журнала этот метод возвращает класс ведения журнала, используемый для тестирования. `ListLogger` следит за сообщениями в журнале, доступными для оценки в тестах.

Затем **щелкните правой кнопкой мыши** на приложение *Functions.Test* и выберите **Добавить > Класс**, назовите его **FunctionsTests.cs** и введите приведенный ниже код.

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
В этом классе реализованы следующие элементы.

- **Http_trigger_should_return_known_string**: этот тест создает запрос со значениями строки запроса `name=Bill` в функцию HTTP и проверяет, возвращен ли ожидаемый ответ.

- **Http_trigger_should_return_string_from_member_data**: в этом тесте используются атрибуты xUnit для предоставления демонстрационных данных функции HTTP.

- **Timer_should_log_message**: этот тест создает экземпляр `ListLogger` и передает его в функции таймера. После выполнения функции журнал будет проверен на наличие ожидаемого сообщения.

Если вы хотите получить доступ к параметрам приложения в тестах, можно использовать [System. Environment. GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Выполнение тестов

Чтобы выполнить тесты, перейдите в **Обозреватель тестов** и щелкните **Запустить все**.

![Тестирование Функций Azure с помощью C# в Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Отладка тестов

Чтобы отладить тесты, установите точку останова в тесте, перейдите в **Обозреватель тестов** и щелкните **Запустить > Отладка последнего выполнения**.

## <a name="javascript-in-vs-code"></a>JavaScript в VS Code

В следующем примере описано создание приложения-функции JavaScript в VS Code, а также его запуск и проверка с помощью [Jest](https://jestjs.io). В этой процедуре используется [расширение функций VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) для создания Функций Azure.

![Тестирование Функций Azure с помощью JavaScript в VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Setup

Для настройки среды инициализируйте новое приложение Node.js в пустой папке, запустив `npm init`.

```bash
npm init -y
```
Затем установите Jest, выполнив следующую команду.

```bash
npm i jest
```
Теперь обновите _package.json_, чтобы заменить существующую тестовую команду приведенной ниже командой.

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Создание модулей тестирования
Благодаря инициализации проекта можно создать модули для запуска автоматических тестов. Для начала создайте новую папку с именем *testing* для хранения модулей поддержки.

В папке *testing* добавьте новый файл, присвойте ему имя **defaultContext.js** и добавьте приведенный ниже код.

```javascript
module.exports = {
    log: jest.fn()
};
```
Этот модуль имитирует функцию *log*, чтобы предоставить контекст выполнения по умолчанию.

Затем добавьте новый файл, присвойте ему имя **defaultTimer.js** и добавьте следующий код.

```javascript
module.exports = {
    IsPastDue: false
};
```

Этот модуль реализует свойство `IsPastDue`, чтобы установить его как ложный экземпляр таймера. Такие конфигурации таймера, как выражения НКРОНТАБ, не требуются здесь, так как тестовая программа просто вызывает функцию напрямую для проверки результата.

Затем используйте расширение Функций VS Code, чтобы [создать новую Функцию JavaScript HTTP](/azure/javascript/tutorial-vscode-serverless-node-01), и присвойте ей имя *HttpTrigger*. Создав функцию, добавьте новый файл в той же папке с именем **index.test.js** и приведенный ниже код.

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
Функция HTTP на основе шаблона возвращает строку "Hello", сцепленную с именем, указанным в строке запроса. Ложный экземпляр запроса создается этим тестом и передается функции HTTP. Тест проверяет, что метод *log* вызывается один раз, и возвращенный текст приравнивается к "Hello Bill".

Затем используйте расширение Функций VS Code, чтобы создать новую Функцию таймера JavaScript, и присвойте ей имя *TimerTrigger*. Создав функцию, добавьте новый файл в той же папке с именем **index.test.js** и приведенный ниже код.

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Функция таймера из шаблона записывает сообщение в конце основной части функции. Этот тест гарантирует, что функцию *log* вызывают один раз.

### <a name="run-tests"></a>Выполнение тестов
Для выполнения тестов нажмите клавиши **CTRL + ~** , чтобы открыть командное окно, и запустите `npm test`.

```bash
npm test
```

![Тестирование Функций Azure с помощью JavaScript в VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Отладка тестов

Для отладки тестов добавьте следующую конфигурацию в файл *launch.json*.

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Затем в тесте установите точку останова и нажмите клавишу **F5**.

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как записывать автоматизированные тесты для функций, изучите следующие ресурсы.
- [Запуск функции, не активируемой HTTP-запросом, вручную](./functions-manually-run-non-http.md)
- [Обработка ошибок службы "Функции Azure"](./functions-bindings-error-pages.md)
- [Локальная отладка триггера Сетки событий для функций Azure](./functions-debug-event-grid-trigger-local.md)
