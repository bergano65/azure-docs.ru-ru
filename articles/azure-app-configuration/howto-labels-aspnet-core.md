---
title: Использовать конфигурацию для отдельных сред
titleSuffix: Azure App Configuration
description: Использование меток для предоставления значений конфигурации для отдельных сред
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056800"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Использование меток для включения различных конфигураций для разных сред

Многие приложения должны использовать разные конфигурации для разных сред. Предположим, что приложение имеет значение конфигурации, определяющее строку подключения, которая будет использоваться для ее серверной базы данных. Разработчики приложения используют базу данных, отличную от той, которая используется в рабочей среде. Строка подключения к базе данных, используемая приложением, должна изменяться при переходе приложения из среды разработки в рабочую среду.

В конфигурации приложений Azure *метки* можно использовать для определения различных значений для одного и того же ключа. Например, можно определить один ключ с различными значениями для *разработки* и *рабочей среды*. Вы можете указать, какие метки следует загружать при подключении к конфигурации приложения.

Чтобы продемонстрировать эту функциональность, мы изменим веб-приложение, созданное в [кратком руководстве: создание ASP.NET Core приложения с помощью конфигурации приложений Azure](./quickstart-aspnet-core-app.md) для использования различных параметров конфигурации для разработки и рабочей среды. Для продолжения работы выполните краткое руководство.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Указание метки при добавлении значения конфигурации

В портал Azure перейдите в **Обозреватель конфигураций** и найдите раздел *TestApp: Settings: FontColor* , созданный в кратком руководстве. Выберите его контекстное меню и нажмите кнопку **Добавить значение**.

> [!div class="mx-imgBorder"]
> ![Добавить пункт меню "значение"](media/labels-add-value.png)

На экране **Добавить значение** введите **красный** и **Value** **метку** **разработки**. Оставьте поле **тип содержимого** пустым. Нажмите кнопку **Применить**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Загрузка значений конфигурации с указанной меткой

По умолчанию в конфигурации приложений Azure загружаются только значения конфигурации без метки. Если вы определили метки для значений конфигурации, вам нужно указать метки, которые будут использоваться при подключении к конфигурации приложения.

В последнем разделе вы создали другое значение конфигурации для среды *разработки* . `HostingEnvironment.EnvironmentName` Переменная используется для динамического определения среды, в которой в данный момент выполняется приложение. Дополнительные сведения см. в статье [Использование нескольких сред в ASP.NET Core](/aspnet/core/fundamentals/environments).

Загрузите значения конфигурации с меткой, соответствующей текущей среде, передав имя среды в `Select` метод:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Приведенный выше фрагмент кода загружает строку подключения конфигурации приложения из переменной среды с `AppConfigConnectionString`именем. Убедитесь, что эта переменная среды задана правильно.

Метод `Select` вызывается два раза. В первый раз он загружает значения конфигурации без метки. Затем он загружает значения конфигурации с меткой, соответствующей текущей среде. Эти значения, относящиеся к конкретной среде, переопределяют все соответствующие значения без метки. Для каждого ключа не нужно задавать значения, относящиеся к конкретной среде. Если у ключа нет значения с меткой, соответствующей текущему окружению, то используется значение без метки.

## <a name="testing-in-different-environments"></a>Тестирование в различных средах

Чтобы проверить различные значения конфигурации, откройте `launchSettings.json` файл в `Properties` каталоге. Выберите `config` запись в разделе `profiles`. В `environmentVariables` разделе Задайте для `ASPNETCORE_ENVIRONMENT` `Production`переменной значение.

После установки новых значений создайте и запустите приложение.

```dotnetcli
dotnet build
dotnet run
```

Используйте веб-браузер для перехода по `http://localhost:5000`адресу. Обратите внимание, что цвет шрифта — черный.

![Веб-приложение, выполняемое с рабочей конфигурацией](media/labels-website-prod.png)

Теперь обновите `launchSettings.json` , чтобы `ASPNETCORE_ENVIRONMENT` присвоить переменной значение `Development`. Еще раз запустите `dotnet run`. Вы заметите, что цвет шрифта теперь красный. Это связано с тем, что приложение теперь использует значение `TestApp:Settings:FontColor` , имеющее `Development` метку. Все остальные значения конфигурации остаются теми же, что и их рабочие значения.

![Веб-приложение, выполняемое с конфигурацией разработки](media/labels-website-dev.png)

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Конфигурация в .NET Core](/aspnet/core/fundamentals/configuration/)
