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
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604540"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Заметки к диаграммам метрик в Application Insights

Заметки на [обозревателя метрик](../../azure-monitor/app/metrics-explorer.md) диаграммы показывают, где развернута новая сборка или другие значимые события. Заметки позволяют легко см. в разделе ли внесенные изменения повлияли на производительность приложения. Они могут автоматически создавать [конвейеры Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/) системы сборки. Заметки можно также создавать, чтобы помечать какие-либо события, используя PowerShell.

> [!NOTE]
> В этой статье описывается **классический интерфейс для работы с метриками**, который не рекомендуется к использованию. Сейчас заметки доступны только в классическом интерфейсе и в **[книгах](../../azure-monitor/app/usage-workbooks.md)** . Дополнительные сведения о текущей деятельности метрики, см. в разделе [дополнительные возможности обозревателя метрик Azure](../../azure-monitor/platform/metrics-charts.md).

![Пример заметок](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure конвейеры сборки заметки о выпуске

Заметки к выпуску являются компонентом облачной службы Azure конвейеры DevOps в Azure.

### <a name="install-the-annotations-extension-one-time"></a>Установка расширения заметок (однократно)
Чтобы иметь возможность создания заметок к выпуску, необходимо установить один из доступных расширений Azure DevOps в Visual Studio Marketplace.

1. Войдите в ваш [Azure DevOps](https://azure.microsoft.com/services/devops/) проекта.
   
1. В Visual Studio Marketplace [расширение заметок к выпуску](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) странице, выберите вашу организацию Azure DevOps, а затем выберите **установить** для добавления расширения к вашей организации DevOps в Azure.
   
   ![Выберите организацию Azure DevOps, а затем выберите Install.](./media/annotations/1-install.png)
   
Необходимо установить расширение один раз для вашей организации DevOps в Azure. Теперь можно настроить заметки к выпуску для любого проекта в вашей организации.

### <a name="configure-release-annotations"></a>Настройка заметок к выпуску

Создайте отдельный ключ API для каждого из шаблонов выпуска Azure конвейеров.

1. Войдите в [портала Azure](https://portal.azure.com) и откройте ресурс Application Insights, отслеживающий вашего приложения. Или если у вас нет, [создания ресурса Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Откройте **доступ через API** вкладка и копирования **идентификатор Application Insights**.
   
   ![В разделе доступ через API скопируйте идентификатор приложения.](./media/annotations/2-app-id.png)

1. В отдельном окне браузера откройте или создайте шаблон выпуска, который управляет развертываниями Azure конвейеров.
   
1. Выберите **добавить задачу**, а затем выберите **заметки к выпуску Application Insights** задач в меню.
   
   ![Выберите Добавление задач и заметок к выпуску Application Insights.](./media/annotations/3-add-task.png)
   
1. В разделе **идентификатор приложения**, вставьте идентификатор Application Insights, скопированный из **доступ через API** вкладки.
   
   ![Вставьте идентификатор Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Вернитесь в Application Insights **доступ через API** выберите **Создание ключа API**. 
   
   ![На вкладке "доступ через API" выберите Создание ключа API.](./media/annotations/5-create-api-key.png)
   
1. В **Создание ключа API** выберите окно, введите описание, **записи заметок**, а затем выберите **создать ключ**. Скопируйте новый ключ.
   
   ![В окне API для создания ключа введите описание, выберите запись заметок и выберите создать ключ.](./media/annotations/6-create-api-key.png)
   
1. В окне шаблон выпуска на **переменных** выберите **добавить** создать определение переменной для нового ключа API.

1. В разделе **имя**, введите `ApiKey`и в разделе **значение**, вставьте ключ API, скопированный из **доступ через API** вкладки.
   
   ![На вкладке "переменные DevOps Azure" выберите Add, имя переменной ApiKey и вставьте ключ API в значением.](./media/annotations/7-paste-api-key.png)
   
1. Выберите **Сохранить** в окне шаблон основной выпуск, чтобы сохранить шаблон.

## <a name="view-annotations"></a>Просмотр заметок
Теперь при использовании шаблона выпуска для развертывания нового выпуска заметки отправляется в Application Insights. Заметки отображаются на диаграммах в **обозревателя метрик**.

Выберите любой маркер заметки (света Серая стрелка) чтобы открыть сведения о выпуске, включая запрашивающей стороны, ветвь системы управления версиями, конвейер выпуска и среды.

![Выберите маркер заметки о выпуске.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Создание настраиваемых заметок в PowerShell
Можно использовать [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) сценарий PowerShell из GitHub, чтобы создать аннотации из любого процесса, вам нравится, без использования DevOps в Azure. 

1. Создать локальную копию [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Следуйте инструкциям в предыдущей процедуре, чтобы получить идентификатор приложения Insights и создайте ключ API из Application Insights **доступ через API** вкладки.
   
1. Вызовите сценарий PowerShell следующим кодом, заменив заполнители в угловых скобках своими значениями. `-releaseProperties` Являются необязательными. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Можно изменить сценарий, например для создания заметок за последний.

## <a name="next-steps"></a>Следующие шаги

* [Создание рабочих элементов](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Автоматизация с помощью PowerShell](../../azure-monitor/app/powershell.md)
