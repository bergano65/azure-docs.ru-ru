---
title: Заметки о выпуске для Application Insights | Документация Майкрософт
description: Добавление маркеров развертывания или сборки для диаграмм обозревателя метрик в Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mbullwin
ms.openlocfilehash: 126c0d63a7d59b76361a25844575ee6556a475b1
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002082"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Заметки к диаграммам метрик в Application Insights

Заметки к диаграммам [обозревателя метрик](../../azure-monitor/app/metrics-explorer.md) показывают, где развернута новая сборка, а также отображают другие важные события. С их помощью легко увидеть, повлияли ли ваши изменения на производительность приложения. Заметки могут создаваться автоматически [системой сборки Azure DevOps Services](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Заметки можно также создавать, чтобы помечать какие-либо события, [используя PowerShell](#create-annotations-from-powershell).

> [!NOTE]
> В этой статье описывается **классический интерфейс для работы с метриками**, который не рекомендуется к использованию. Сейчас заметки доступны только в классическом интерфейсе и в **[книгах](../../application-insights/app-insights-usage-workbooks.md)**. Сведения об интерфейсе для работы с метриками, который используется сейчас, см. в [этой статье](../../azure-monitor/platform/metrics-charts.md).

![Пример заметок с видимой корреляцией с временем ответа сервера](./media/annotations/00.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Заметки к выпуску со сборкой Azure DevOps Services

Заметки к выпуску — это функция облачной службы Azure Pipelines в службе Azure DevOps Services. 

### <a name="install-the-annotations-extension-one-time"></a>Установка расширения заметок (однократно)
Чтобы получить возможность создания заметок к выпуску, необходимо установить одно из расширений Azure DevOps Services, доступных в Visual Studio Marketplace.

1. Войдите в проект [Azure DevOps Services](https://visualstudio.microsoft.com/vso/).
2. В Visual Studio Marketplace [найдите расширение заметок к выпуску](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) и добавьте его в свою организацию Azure DevOps Services.

![В верхнем правом углу веб-страницы Azure DevOps Services откройте Marketplace. Выберите Azure DevOps Services, а затем в разделе Azure Pipelines щелкните "Больше".](./media/annotations/10.png)

Для вашей организации Azure DevOps Services это необходимо сделать всего один раз. Теперь вы можете настроить заметки к выпуску для любого проекта в организации. 

### <a name="configure-release-annotations"></a>Настройка заметок к выпуску

Для каждого шаблона выпуска Azure DevOps Services необходимо получить отдельный ключ API.

1. Выполните вход на [портал Microsoft Azure](https://portal.azure.com) и откройте ресурс Application Insights, который используется для мониторинга вашего приложения. (Или [создайте новый](../../application-insights/app-insights-overview.md), если вы этого еще не сделали.)
2. Откройте **Доступ через API** и выберите **Идентификатор Application Insights**.
   
    ![На сайте portal.azure.com откройте ресурс Application Insights и выберите "Параметры". Откройте "Доступ к API". Скопируйте идентификатор приложения.](./media/annotations/20.png)

4. В отдельном окне браузера откройте (или создайте) шаблон выпуска, который управляет развертываниями из Azure DevOps Services. 
   
    Добавьте задачу и выберите в меню задачу заметок к выпуску Application Insights.
   
    Вставьте **идентификатор приложения** , скопированный из колонки "Доступ к API".
   
    ![В Azure DevOps Services откройте выпуск, выберите конвейер выпуска и щелкните "Изменить". Щелкните "Добавьте задачу" и выберите "Заметки к выпуску Application Insights". Вставьте ИД Application Insights.](./media/annotations/30.png)
4. Задайте в качестве значения поля **APIKey** переменную `$(ApiKey)`.

5. В окне Azure создайте ключ API и скопируйте его.
   
    ![В колонке "Доступ к API" в окне Azure щелкните "Создать ключ API". Введите комментарий, щелкните "Написать заметки" и нажмите кнопку "Создать ключ". Скопируйте новый ключ.](./media/annotations/40.png)

6. Перейдите на вкладку "Конфигурация" шаблона выпуска.
   
    Создайте определение переменной для `ApiKey`.
   
    Вставьте ключ API в определение переменной ApiKey.
   
    ![В окне Azure DevOps Services перейдите на вкладку "Конфигурация" и щелкните "Добавить переменную". Задайте "ApiKey" в качестве имени и в поле "Значение" вставьте ключ, который был только что создан, и щелкните значок замка.](./media/annotations/50.png)
7. Наконец, **сохраните** конвейер выпуска.


## <a name="view-annotations"></a>Просмотр заметок
Теперь при каждом развертывании нового выпуска с помощью шаблона выпуска заметки будут отправляться в Application Insights. Заметки будут отображаться на диаграммах в обозревателе метрик.

Щелкните любой маркер заметки, чтобы открыть подробные сведения о выпуске, включая запросившую сторону, ветвь системы управления версиями, конвейер выпуска, среду и многое другое.

![Щелкните любой маркер заметки о выпуске.](./media/annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Создание настраиваемых заметок в PowerShell
Вы также можете создать заметки из любого необходимого процесса (без использования Azure DevOps Services). 


1. Создайте локальную копию [сценария PowerShell из GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Получите идентификатор приложения и создайте ключ API в колонке "Доступ через API".

3. Вызовите сценарий следующим образом.

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Сценарий легко изменить, например, чтобы создать заметки для предыдущих выпусков.

## <a name="next-steps"></a>Дополнительная информация

* [Создание рабочих элементов](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Автоматизация с помощью PowerShell](../../azure-monitor/app/powershell.md)