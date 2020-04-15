---
title: Разработка и развертывание WebJobs с помощью VS
description: Узнайте, как развивать Azure WebJobs в Visual Studio и развертывать их в службе приложений Azure, включая создание запланированной задачи.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 75e1f5fbfa41ac310d2a737dcfe28199715a094b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312877"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Разработка и развертывание веб-заданий в службе приложений Azure с помощью Visual Studio

В этой статье объясняется, как использовать Visual Studio для развертывания проекта консольного приложения в веб-приложении в [App Service](overview.md) в качестве [Azure WebJob.](https://go.microsoft.com/fwlink/?LinkId=390226) Дополнительные сведения о развертывании веб-заданий с использованием [портала Azure](https://portal.azure.com) см. в руководстве по [выполнению фоновых задач с помощью веб-заданий](webjobs-create.md).

Вы можете опубликовать несколько WebJobs в одном веб-приложении. Убедитесь, что каждый WebJob в веб-приложении имеет уникальное имя.

Версия 3.x [SDK Azure WebJobs](webjobs-sdk-how-to.md) позволяет разрабатывать WebJobs, которые работают как приложения .NET Core или .NET Framework, в то время как версия 2.x поддерживает только рамочную систему .NET. Способ развертывания проекта WebJobs отличается для проектов .NET Core по сравнению с проектами .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs — консольные приложения .NET Core

При использовании версии 3.x WebJobs можно создавать и публиковать WebJobs как консольные приложения .NET Core. Для пошаговых инструкций по созданию и публикации консольного приложения .NET Core в Azure в качестве WebJob [см.](webjobs-sdk-get-started.md)

> [!NOTE]
> .NET Core WebJobs не может быть связан с веб-проектами. Если вам нужно развернуть WebJob с помощью веб-приложения, необходимо [создать WebJob в виде консольного приложения .NET Framework.](#webjobs-as-net-framework-console-apps)  

### <a name="deploy-to-azure-app-service"></a>Развертывание в Службе приложений Azure

Публикация приложения .NET Core WebJob в App Service от Visual Studio использует тот же инструмент, что и публикация приложения ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Типы веб-заданий

По умолчанию WebJob, опубликованный с консоли .NET Core, работает только при срабатывании или по требованию. Вы также можете обновить проект для [выполнения по расписанию](#scheduled-execution) или непрерывного выполнения.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Запланированное исполнение

При публикации приложения консоли .NET Core в Azure в проект добавляется новый файл *settings.job.* Используйте этот файл для настройки графика выполнения WebJob. Для получения дополнительной информации [см.](#scheduling-a-triggered-webjob)

#### <a name="continuous-execution"></a>Непрерывное выполнение

Visual Studio можно использовать visual Studio для изменения WebJob для непрерывного выполнения, когда всегда включено в Azure.

1. Если вы еще не сделали этого, [опубликуйте проект в Azure](#deploy-to-azure-app-service).

1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**.

1. Во вкладке **«Публикация»** выберите **настройки**. 

1. В **диалоге настройки профиля** выберите **Непрерывный** для **типа WebJob**и выберите **Сохранить**.

    ![Диалог настроек для WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Выберите **Publish,** чтобы переиздать WebJob с обновленными настройками.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs — консольные приложения .NET Framework  

Когда Visual Studio развертывает проект приложения для консоли WebJobs с поддержкой .NET, она копирует файлы времени выполнения в соответствующую папку в веб-приложении *(App_Data/рабочих мест/непрерывнодля* непрерывного WebJobs и *App_Data/рабочих мест/триггера* для запланированных или по требованию WebJobs).

Проект с поддержкой веб-заданий содержит следующие добавленные элементы.

* Пакет NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Файл [webjob-publish-settings.json](#publishsettings) , который содержит параметры развертывания и планировщика. 

![Диаграмма, отображающая добавленные элементы в приложение консоли для реализации развертывания как веб-задания](./media/webjobs-dotnet-deploy-vs/convert.png)

Вы можете добавить эти элементы в существующий проект приложения консоли или использовать шаблон для создания нового проекта приложения консоли с поддержкой веб-заданий. 

Можно развернуть проект как веб-задание или связать его с веб-проектом для автоматического развертывания при каждом развертывании веб-проекта. Чтобы связать проекты, Visual Studio включает имя проекта с поддержкой веб-заданий в файле [webjobs-list.json](#webjobslist) веб-проекта.

![Диаграмма, отображающая проект веб-задания, связанный с веб-проектом](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Предварительные требования

Если вы используете Visual Studio 2015, установите [пакет Azure SDK для .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Если вы используете Visual Studio 2017, установите [рабочую нагрузку для разработки Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a> Включение развертывания веб-заданий для существующего проекта консольного приложения

Имеются две возможности.

* [Включение автоматического развертывания с веб-проектом](#convertlink).

  Настройка существующего проекта приложения консоли для автоматического развертывания в качестве веб-приложения при развертывании веб-проекта. Используйте этот параметр, если нужно запустить веб-задание в том же веб-приложении, в котором запущено связанное веб-приложение.

* [Включение развертывания без веб-проекта](#convertnolink).

  Настройка существующего проекта приложения консоли для развертывания в качестве веб-задания без ссылки на веб-проект. Используйте этот параметр, если нужно запустить веб-задание в веб-приложении самостоятельно, без веб-приложения, запущенного в веб-приложении. Это может понадобиться для независимого масштабирования ресурсов веб-заданий ресурсов веб-приложений.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Включение автоматического развертывания веб-заданий с помощью веб-проекта

1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Добавить** > **Существующий проект как веб-задание Azure**.
   
    ![Существующий проект как веб-задание Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Откроется диалоговое окно [Добавление веб-задания Azure](#configure) .
2. В раскрывающемся списке **Имя проекта** выберите проект приложения консоли для добавления в качестве веб-задания.
   
    ![Выбор проекта в диалоговом окне "Добавление веб-задания Azure"](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Заполните диалоговое окно [Добавление веб-задания Azure](#configure) и щелкните кнопку **ОК**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Включение развертывания веб-заданий без веб-проекта
1. Щелкните правой кнопкой мыши проект консольного приложения в **обозревателе решений** и выберите **Опубликовать как веб-задание Azure…**. 
   
    ![Опубликовать как веб-задание Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Появится диалоговое окно [Добавление веб-задания Azure](#configure) с проектом, выбранным в поле **Имя проекта** .
2. Заполните диалоговое окно [Добавление веб-задания Azure](#configure) и щелкните кнопку **ОК**.
   
   Откроется мастер **веб-публикации** .  Если вы не хотите сразу выполнить публикацию, закройте мастер. Указанные параметры сохраняются до момента [развертывания проекта](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Создание нового проекта с поддержкой веб-заданий
Чтобы создать проект с поддержкой веб-заданий, можно использовать шаблон проекта приложения консоли и включить развертывание веб-заданий, как показано в [предыдущем разделе](#convert). В качестве альтернативы можно использовать шаблон создания проекта с поддержкой веб-заданий:

* [Используйте шаблон нового проекта WebJobs для независимого WebJob](#createnolink)
  
    Создайте проект и настройте его для развертывания в качестве веб-задания без ссылки на веб-проект. Используйте этот параметр, если нужно запустить веб-задание в веб-приложении самостоятельно, без веб-приложения, запущенного в веб-приложении. Это может понадобиться для независимого масштабирования ресурсов веб-заданий ресурсов веб-приложений.
* [Используйте шаблон нового проекта WebJobs для WebJob, связанного с веб-проектом](#createlink)
  
    Создайте проект, настроенный для автоматического развертывания, как веб-задание при развертывании веб-проекта в том же решении. Используйте этот параметр, если нужно запустить веб-задание в том же веб-приложении, в котором запущено связанное веб-приложение.

> [!NOTE]
> Шаблон нового проекта веб-задания автоматически устанавливает пакеты NuGet и включает в файл *Program.cs* код для [пакета SDK веб-заданий](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Если вы не хотите использовать пакет SDK для веб-заданий, удалите или измените инструкцию `host.RunAndBlock` в файле *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Использование шаблона создания проекта веб-заданий для отдельного веб-задания
1. Нажмите **Файл** > **Новый проект**, а затем в диалоговом поле **нового проекта** нажмите **Облако** > **Azure WebJob (.NET Framework)**.
   
    ![Диалоговое окно "Новый проект", отображающее шаблон веб-заданий](./media/webjobs-dotnet-deploy-vs/np.png)
2. Следуйте инструкциям, показанным ранее, чтобы [сделать проект приложения консоли независимым проектом веб-заданий](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Использование шаблона создания проекта с поддержкой веб-заданий для веб-задания, связанного с веб-проектом
1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Добавить** > **Новый проект веб-задания Azure**.
   
    ![Запись меню "Новый проект веб-задания Azure"](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Откроется диалоговое окно [Добавление веб-задания Azure](#configure) .
2. Заполните диалоговое окно [Добавление веб-задания Azure](#configure) и щелкните кнопку **ОК**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Диалог добавить Azure WebJob
В диалогом окне **Добавить веб-задание Azure** можно указать имя веб-задания и настроить режим его выполнения. 

![Диалоговое окно "Добавление веб-заданий Azure"](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Поля в этом диалоговом окне соответствуют полям в диалоговом окне **Добавление веб-задания** на портале Azure. Дополнительные сведения см. в статье [Выполнение фоновых задач с помощью веб-заданий в службе приложений Azure](webjobs-create.md).

> [!NOTE]
> * Дополнительные сведения о развертывании из командной строки см. в разделе [Включение предоставления веб-заданий Azure из командной строки или постоянно](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Если после развертывания веб-задания принимается решение об изменении типа веб-задания и его повторного развертывания, нужно удалить файл *webjobs-publish-settings.json*. При этом Visual Studio снова покажет параметры публикации и можно будет изменить тип веб-задания.
> * Если вы развертываете веб-задание, а затем изменяете режим выполнения с постоянного на непостоянный или наоборот, Visual Studio создаст новое веб-задание в Azure при повторном развертывании. При изменении других параметров планирования но сохранении режима выполнения или при переключении между режимами "Запланировано" и "По запросу" Visual Studio обновляет существующее задание, а не создает новое.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
При настройке консольного приложения для развертывания веб-заданий Visual Studio устанавливает пакет NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) и сохраняет сведения о планировании в файле *webjob-publish-settings.json* в папке *Properties* проекта веб-заданий. Вот пример этого файла:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Вы можете изменить этот файл напрямую, и Visual Studio предоставит IntelliSense. Схема файла хранится [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) в и может быть просмотрена там.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
При связывании проекта с поддержкой веб-заданий с веб-проектом Visual Studio сохраняет имя проекта веб-заданий в файле *webjobs-list.json* в папке веб-проекта *Properties*. Список может содержать несколько проектов веб-заданий, как показано в следующем примере:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Вы можете изменить этот файл напрямую, и Visual Studio предоставит IntelliSense. Схема файла хранится [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) в и может быть просмотрена там.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Развертывание проекта веб-заданий
Проект веб-заданий, связанный с веб-проектом, развертывается автоматически с веб-проектом. Для получения информации о развертывании **веб-проектов** > **Deploy app** см.

Чтобы развернуть проект веб-заданий, щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать как веб-задание Azure…**. 

![Опубликовать как веб-задание Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Для независимого веб-задания отобразится тот же мастер **веб-публикации** , который использовался для веб-проектов, но некоторые параметры будут недоступны для изменения.

## <a name="scheduling-a-triggered-webjob"></a>Планирование активируемого веб-задания

WebJobs использует файл *settings.job* для определения того, когда запущен WebJob. Используйте этот файл для настройки графика выполнения WebJob. Следующий пример работает каждый час с 9 утра до 5 вечера:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Этот файл должен быть расположен в корне папки WebJobs, наряду со `wwwroot\app_data\jobs\triggered\{job name}` `wwwroot\app_data\jobs\continuous\{job name}`скриптом WebJob, например, При развертывании веб-задания из Visual Studio для свойств файла `settings.job` установите значение **Копировать, если новее**. 

При [создании WebJob с портала Azure](webjobs-create.md)для вас создается файл settings.job.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Выражения CRON

WebJobs использует те же выражения CRON для планирования, как таймер триггера в Azure Функции. Чтобы узнать больше о [поддержке](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)CRON, см.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>ссылка на работу на настройках.job

Следующие настройки поддерживаются WebJobs:

| **Параметр** | **Type**  | **Описание** |
| ----------- | --------- | --------------- |
| `is_in_place` | Все | Позволяет выполнять задание без предварительного копирования в папку temp. Чтобы узнать больше, смотрите [каталог работы WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Непрерывные | Только запустить WebJobs на одном экземпляре при масштабировании. Чтобы узнать больше, см [Установить непрерывную работу в качестве синглтона](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Активируемые | Запустите WebJob по графику, основанному на CRON. Чтобы узнать больше, [см.](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) |
| `stopping_wait_time`| Все | Позволяет контролировать поведение выключения. Чтобы узнать больше, см [Изящный выключения](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Подробнее о пакете SDK веб-заданий](webjobs-sdk-how-to.md)
