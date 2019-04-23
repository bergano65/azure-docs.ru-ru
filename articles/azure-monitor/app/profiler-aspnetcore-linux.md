---
title: Профилирование веб-приложений ASP.NET Core в Azure для Linux с помощью Application Insights Profiler | Документация Майкрософт
description: Общие сведения и пошаговое руководство по использованию Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.reviewer: mbullwin
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: cweining
ms.openlocfilehash: 35789cc1e516fb24d5e985e12b44fe3cd01b795d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790162"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Профилирование веб-приложений ASP.NET Core в Azure для Linux с помощью Application Insights Profiler

Эта функция в настоящее время находится на стадии предварительной версии.

Узнайте, сколько времени выполняется каждый метод в динамическом веб-приложении с помощью [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights Profiler теперь доступен для веб-приложений ASP.NET Core, размещенных в службах приложений Azure для Linux. В этом руководстве описано, как выполнить сбор трассировок профилировщика для веб-приложений ASP.NET Core для Linux.

Когда вы завершите работу с этим руководством, ваше приложение сможет собирать трассировки профилировщика, как показано на рисунке ниже. В этом примере трассировка профилировщика указывает на то, что определенный веб-запрос выполняется медленно, так как время тратится на ожидание. *Горячий путь* в коде, который замедляет работу приложения, отмечен значком пламени. Метод **About** в разделе **HomeController** замедляет работу веб-приложения, так как он вызывает функцию **Thread.Sleep**.

![Трассировки профилировщика](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Технические условия
Приведенные ниже инструкции применяются ко всем средам разработки для Windows, Linux и Mac:

* Установите [пакет SDK для .NET Core 2.1.2 или более поздней версии](https://dotnet.microsoft.com/download/archives).
* Установите Git, следуя [руководству по началу работы и установке Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Локальная настройка проекта

1. Откройте окно командной строки на компьютере. Приведенные ниже инструкции работают для всех сред разработки для Windows, Linux и Mac.

1. Создайте веб-приложение MVC для ASP.NET Core.

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Измените рабочую папку на корневую папку для проекта.

1. Добавьте пакет NuGet для сбора трассировок профилировщика.

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Включите Application Insights в файле Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Включите Profiler в файле Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Добавьте строку кода в раздел **HomeController.cs** для случайной задержки на несколько секунд.

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

1. Сохраните и зафиксируйте внесенные изменения в локальном репозитории.

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Создание веб-приложения в Linux для размещения проекта

1. Создайте среду веб приложения с помощью службы приложений в Linux.

    ![Создание веб-приложения в Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Создайте учетные данные для развертывания.

    > [!NOTE]
    > Запишите пароль. Он потребуется позже при развертывании приложения.

    ![Создание учетных данных для развертывания](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Выберите варианты развертывания. Настройте локальный репозиторий Git в веб-приложении, следуя инструкциям на портале Azure. Репозиторий Git создается автоматически.

    ![Настройка репозитория Git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Дополнительные варианты развертывания см. в [этой статье](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Развертывание проекта

1. В окне командной строки перейдите в корневую папку проекта. Добавьте удаленный репозиторий Git, чтобы он указывал на репозиторий в службе приложений.

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Укажите вместо **username** имя пользователя, указанное на этапе создания учетных данных для развертывания.
    * Укажите вместо **app_name** имя приложения, указанное на этапе создания веб-приложения с помощью службы приложений в Linux.

2. Разверните проект, применив изменения в Azure с помощью команды push:

    ```
    git push azure master
    ```

Вы должны увидеть результат, аналогичный приведенному ниже.

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Добавление Application Insights для мониторинга веб-приложений

1. [Создайте ресурс Application Insights](./../../azure-monitor/app/create-new-resource.md ).

2. Скопируйте значение **iKey** ресурса Application Insights и задайте следующие параметры в веб-приложениях.

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    После изменения параметров приложения выполняется автоматический перезапуск сайта. Когда новые параметры будут применены, профилировщик немедленно запустится на две минуты. Затем он будет запускаться на две минуты каждый час.

3. Создайте трафик для веб-сайта. Вы можете создать трафик, обновив несколько раз страницу **с основными сведениями** сайта.

4. Подождите от двух до пяти минут для статистической обработки событий в Application Insights.

5. Перейдите в область **производительности** Application Insights на портале Azure. В правой нижней части области можно просмотреть трассировки профилировщика.

    ![Просмотр трассировок профилировщика](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Известные проблемы

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>Профиль теперь кнопка не работает для Linux Profiler
Версии Linux профилировщик App Insights не еще поддерживает по требованию, профилирование с помощью профиля теперь кнопка.


## <a name="next-steps"></a>Дальнейшие действия
Если вы используете пользовательские контейнеры, размещенные в Службе приложений Azure, чтобы включить Application Insights Profiler, следуйте инструкциям из статьи [Enable Service Profiler for containerized ASP.NET Core application](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) (Включение профилировщика службы для контейнерного приложения ASP.NET Core).

О каких-либо проблемах или предложениях сообщайте в репозиторий GitHub [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
