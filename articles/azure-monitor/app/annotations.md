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
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476171"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Заметки к диаграммам метрик в Application Insights

Заметки к диаграммам [обозревателя метрик](../../azure-monitor/app/metrics-explorer.md) показывают, где развернута новая сборка, а также отображают другие важные события. С их помощью легко увидеть, повлияли ли ваши изменения на производительность приложения. Заметки могут создаваться автоматически [системой сборки Azure DevOps Services](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Заметки можно также создавать, чтобы помечать какие-либо события, используя PowerShell.

> [!NOTE]
> В этой статье описывается **классический интерфейс для работы с метриками**, который не рекомендуется к использованию. Сейчас заметки доступны только в классическом интерфейсе и в **[книгах](../../azure-monitor/app/usage-workbooks.md)** . Сведения об интерфейсе для работы с метриками, который используется сейчас, см. в [этой статье](../../azure-monitor/platform/metrics-charts.md).

![Пример заметок](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Заметки к выпуску со сборкой Azure DevOps Services

Заметки к выпуску — это функция облачной службы Azure Pipelines в службе Azure DevOps Services.

### <a name="install-the-annotations-extension-one-time"></a>Установка расширения заметок (однократно)
Чтобы получить возможность создания заметок к выпуску, необходимо установить одно из расширений Azure DevOps Services, доступных в Visual Studio Marketplace.

1. Войдите в проект [Azure DevOps Services](https://azure.microsoft.com/services/devops/).
2. В Visual Studio Marketplace [найдите расширение заметок к выпуску](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) и добавьте его в свою организацию Azure DevOps Services.

![Выберите организацию Azure DevOps, а затем установите.](./media/annotations/1-install.png)

Для вашей организации Azure DevOps Services это необходимо сделать всего один раз. Теперь вы можете настроить заметки к выпуску для любого проекта в организации.

### <a name="configure-release-annotations"></a>Настройка заметок к выпуску

Для каждого шаблона выпуска Azure DevOps Services необходимо получить отдельный ключ API.

1. Войдите в [портал Microsoft Azure](https://portal.azure.com) и откройте ресурс Application Insights, отслеживающий вашего приложения. (Или [создайте новый](../../azure-monitor/app/app-insights-overview.md), если вы этого еще не сделали.)
2. Откройте **доступ через API** вкладка и копирования **идентификатор Application Insights**.
   
    ![На сайте portal.azure.com откройте ресурс Application Insights и выберите "Параметры". Откройте "Доступ к API". Скопируйте идентификатор приложения.](./media/annotations/2-app-id.png)

4. В отдельном окне браузера откройте (или создайте) шаблон выпуска, который управляет развертываниями из Azure DevOps Services.
   
    Добавьте задачу и выберите в меню задачу заметок к выпуску Application Insights.

   ![Щелкните знак «плюс», чтобы добавить задачу и выберите заметки к выпуску Application Insights. Вставьте ИД Application Insights.](./media/annotations/3-add-task.png)

    Вставить **идентификатор приложения** , скопированный на вкладке "доступ через API".
   
    ![Вставьте идентификатор Application Insights](./media/annotations/4-paste-app-id.png)

5. В окне Azure создайте ключ API и скопируйте его.
   
    ![На вкладке "доступ через API" в окне Azure нажмите кнопку "Создать ключ API".](./media/annotations/5-create-api-key.png)

    ![На вкладке ключей создать API ввести комментарий, проверьте написать заметки и нажмите кнопку Создать ключ. Скопируйте новый ключ.](./media/annotations/6-create-api-key.png)

6. Перейдите на вкладку "Конфигурация" шаблона выпуска.
   
    Создайте определение переменной для `ApiKey`.
   
    Вставьте ключ API в определение переменной ApiKey.
   
    ![В окне службам DevOps в Azure перейдите на вкладку переменных и щелкните "Добавить". Задайте имя, ApiKey и в значение, вставьте ключ, который был создан и щелкните значок замка.](./media/annotations/7-paste-api-key.png)
1. Наконец, **сохраните** конвейер выпуска.


## <a name="view-annotations"></a>Просмотр заметок
Теперь при каждом развертывании нового выпуска с помощью шаблона выпуска заметки будут отправляться в Application Insights. Заметки будут отображаться на диаграммах в обозревателе метрик.

Щелкните любой маркер заметки (света Серая стрелка) чтобы открыть сведения о выпуске, включая запросившую сторону, ветвь системы управления версиями, выпуск конвейера, среду и многое другое.

![Щелкните любой маркер заметки о выпуске.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Создание настраиваемых заметок в PowerShell
Вы также можете создать заметки из любого необходимого процесса (без использования Azure DevOps Services). 


1. Создайте локальную копию [сценария PowerShell из GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Получение идентификатора приложения и создайте ключ API на вкладке "доступ через API".

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

## <a name="next-steps"></a>Дальнейшие действия

* [Создание рабочих элементов](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Автоматизация с помощью PowerShell](../../azure-monitor/app/powershell.md)
