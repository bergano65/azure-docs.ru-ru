---
title: Заметки о выпуске для Application Insights | Документация Майкрософт
description: Добавление маркеров развертывания или сборки для диаграмм обозревателя метрик в Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/01/2019
ms.openlocfilehash: f9ab1f1ef2b64c07a3b0c8ddf93d6ef4293517db
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668114"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Заметки к диаграммам метрик в Application Insights

Заметки на [Обозреватель метрик](../../azure-monitor/app/metrics-explorer.md) диаграммах показывают, где развернута новая сборка или другие важные события. Заметки позволяют легко определить, влияют ли ваши изменения на производительность приложения. Они могут автоматически создаваться системой сборки [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) . Заметки можно также создавать, чтобы помечать какие-либо события, используя PowerShell.

> [!NOTE]
> В этой статье описывается **классический интерфейс для работы с метриками**, который не рекомендуется к использованию. Сейчас заметки доступны только в классическом интерфейсе и в **[книгах](../../azure-monitor/app/usage-workbooks.md)** . Дополнительные сведения о текущих метриках см. в статье [Дополнительные возможности Azure обозреватель метрик](../../azure-monitor/platform/metrics-charts.md).

![Пример заметок](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Заметки о выпуске с Azure Pipelines сборкой

Заметки о выпуске — это функция облачной Azure Pipelines службы Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Установка расширения заметок (однократно)
Чтобы иметь возможность создавать заметки о выпуске, необходимо установить одно из многих расширений Azure DevOps, доступных в Visual Studio Marketplace.

1. Войдите в проект [Azure DevOps](https://azure.microsoft.com/services/devops/) .
   
1. На странице [расширение заметок к Выпуску](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) Visual Studio Marketplace выберите свою организацию Azure DevOps и нажмите кнопку **установить** , чтобы добавить расширение в организацию Azure DevOps.
   
   ![Выберите организацию Azure DevOps и нажмите кнопку установить.](./media/annotations/1-install.png)
   
Это расширение необходимо установить только один раз для вашей организации Azure DevOps. Теперь можно настроить заметки к выпуску для любого проекта в Организации.

### <a name="configure-release-annotations"></a>Настройка заметок к выпуску

Создайте отдельный ключ API для каждого из шаблонов выпуска Azure Pipelines.

1. Войдите в [портал Azure](https://portal.azure.com) и откройте ресурс Application Insights, который наблюдает за приложением. Если у вас ее нет, [Создайте новый ресурс Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Откройте вкладку **доступ к API** и скопируйте **идентификатор Application Insights**.
   
   ![В разделе доступ через API скопируйте идентификатор приложения.](./media/annotations/2-app-id.png)

1. В отдельном окне браузера откройте или создайте шаблон выпуска, который управляет развертываниями Azure Pipelines.
   
1. Выберите **Добавить задачу**, а затем в меню выберите задачу **заметки о выпуске Application Insights** .
   
   ![Выберите Добавить задачу и выберите Application Insights заметки о выпуске.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Сейчас задача заметки о выпуске поддерживает только агенты на основе Windows. Он не будет выполняться в Linux, macOS или других типах агентов.
   
1. В поле **Application ID (идентификатор приложения**) вставьте идентификатор Application Insights, скопированный на вкладке **доступ к API** .
   
   ![Вставьте идентификатор Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Вернитесь в окно Application Insights **доступа к API** и выберите **создать ключ API**. 
   
   ![На вкладке доступ через API выберите Создать ключ API.](./media/annotations/5-create-api-key.png)
   
1. В окне **Создание ключа API** введите описание, выберите **записать заметки**и нажмите кнопку **создать ключ**. Скопируйте новый ключ.
   
   ![В окне Создание ключа API введите описание, выберите записать заметки и нажмите кнопку Создать ключ.](./media/annotations/6-create-api-key.png)
   
1. В окне шаблон выпуска на вкладке **переменные** выберите **Добавить** , чтобы создать определение переменной для нового ключа API.

1. В поле **имя**введите `ApiKey`и в поле **значение**вставьте ключ API, скопированный на вкладке **доступ к API** .
   
   ![На вкладке DevOps переменные Azure выберите Добавить, назовите переменную ApiKey и вставьте ключ API в поле значение.](./media/annotations/7-paste-api-key.png)
   
1. Выберите **сохранить** в главном окне шаблона выпуска, чтобы сохранить шаблон.

## <a name="view-annotations"></a>Просмотр заметок
Теперь при использовании шаблона выпуска для развертывания нового выпуска заметка отправляется в Application Insights. Заметки отображаются на диаграммах в **Обозреватель метрик**.

Выберите любой маркер заметки (светло-серая стрелка), чтобы открыть подробные сведения о выпуске, включая запрашивающей стороны, ветвь системы управления версиями, конвейер выпуска и среду.

![Выберите маркер заметки выпуска.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Создание настраиваемых заметок в PowerShell
Вы можете использовать сценарий PowerShell [креатерелеасеаннотатион](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) из GitHub для создания заметок из любого процесса, не используя Azure DevOps. 

1. Создайте локальную копию [креатерелеасеаннотатион. ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Выполните действия, описанные в предыдущей процедуре, чтобы получить идентификатор Application Insights и создать ключ API на вкладке **доступ к api** Application Insights.
   
1. Вызовите сценарий PowerShell с помощью следующего кода, заменив заполнители в угловых скобках значениями. `-releaseProperties` являются необязательными. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Можно изменить скрипт, например, чтобы создать заметки в прошлом.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание рабочих элементов](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Автоматизация с помощью PowerShell](../../azure-monitor/app/powershell.md)
