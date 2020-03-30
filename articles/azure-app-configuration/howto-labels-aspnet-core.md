---
title: Использование конфигурации для окружающей среды
titleSuffix: Azure App Configuration
description: Используйте метки для обеспечения значений конфигурации для окружающей среды
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056800"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Используйте метки для включения различных конфигураций для различных сред

Многим приложениям необходимо использовать различные конфигурации для различных сред. Предположим, что приложение имеет значение конфигурации, определяющее строку соединения для использования для своей базы данных. Разработчики приложения используют другую базу данных, используемую в производстве. Строка подключения к базе данных, используемая приложением, должна меняться по мере перемещения приложения от разработки к производству.

В конфигурации приложений Azure можно использовать *метки* для определения различных значений для одного и того же ключа. Например, можно определить один ключ с различными значениями для *разработки* и *производства.* Можно указать, какую метку (ы) загружать при подключении к конфигурации приложения.

Чтобы продемонстрировать эту функциональность, мы изменим веб-приложение, созданное в [приложении «Быстрый старт»: Создайте приложение ASP.NET Core с конфигурацией приложений Azure,](./quickstart-aspnet-core-app.md) чтобы использовать различные настройки конфигурации для разработки против производства. Пожалуйста, заполните quickstart перед продолжением.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Указать метку при добавлении значения конфигурации

На портале Azure перейдите в **Configuration Explorer** и найдите клавишу *TestApp:FontColor,* который вы создали в quickstart. Выберите контекстное меню, а затем нажмите **Добавить значение**.

> [!div class="mx-imgBorder"]
> ![Добавить элемент меню «Значение»](media/labels-add-value.png)

На экране **добавленной стоимости** введите **значение** **красного цвета** и **этикетку** **развития.** Оставьте **тип содержимого** пустым. Нажмите кнопку **Применить**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Загрузка значений конфигурации с указанной меткой

По умолчанию конфигурация приложения Azure загружает только значения конфигурации без метки. Если вы определили метки для значений конфигурации, необходимо указать метку (ы) для использования при подключении к конфигурации приложения.

В последнем разделе создано другое значение конфигурации для среды *разработки.* Вы используете переменную, `HostingEnvironment.EnvironmentName` чтобы динамически определить, в какой среде в настоящее время работает приложение. Дополнительные сведения см. в статье [Использование нескольких сред в ASP.NET Core](/aspnet/core/fundamentals/environments).

Значения конфигурации нагрузки с меткой, соответствующей текущей `Select` среде, путем передачи имени среды в метод:

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
> Вышеупомянутый фрагмент кода загружает строку подключения `AppConfigConnectionString`конфигурации Приложения от переменной среды, называемой . Убедитесь, что эта переменная среды установлена должным образом.

Метод `Select` вызывается два раза. В первый раз он загружает значения конфигурации без метки. Затем он загружает значения конфигурации с меткой, соответствующей текущей среде. Эти значения, связанные с окружающей средой, переопределяют любые соответствующие значения без метки. Вам не нужно определять значения, связанные с окружающей средой для каждого ключа. Если ключ не имеет значения с меткой, соответствующей текущей среде, используется значение без метки.

## <a name="testing-in-different-environments"></a>Тестирование в различных средах

Чтобы протестировать различные значения `launchSettings.json` конфигурации, `Properties` откройте файл под каталогом. Найдите `config` вход `profiles`под . В `environmentVariables` разделе установите `ASPNETCORE_ENVIRONMENT` переменную на `Production`.

С набором новых значений создайте и запустите приложение.

```dotnetcli
dotnet build
dotnet run
```

Используйте веб-браузер `http://localhost:5000`для навигации по . Вы заметите, что цвет шрифта черный.

![Веб-приложение, работая с производственной конфигурацией](media/labels-website-prod.png)

Теперь `launchSettings.json` обновите для того чтобы установить переменную `ASPNETCORE_ENVIRONMENT` к `Development`. Еще раз запустите `dotnet run`. Вы заметите, что цвет шрифта теперь красный. Это происходит потому, что `TestApp:Settings:FontColor` приложение теперь использует значение, которое `Development` имеет метку. Все остальные значения конфигурации остаются такими же, как и их значения production.

![Веб-приложение, работая с конфигурацией разработки](media/labels-website-dev.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Конфигурация в .NET Core](/aspnet/core/fundamentals/configuration/)
