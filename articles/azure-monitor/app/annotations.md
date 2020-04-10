---
title: Заметки о выпуске для Application Insights | Документация Майкрософт
description: Добавление маркеров развертывания или сборки для диаграмм обозревателя метрик в Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 0ad773ca6a7102ac718d43dfbbf6a4f834e681a0
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010743"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Заметки к диаграммам метрик в Application Insights

Аннотации показывают, где вы развернули новую сборку или другие важные события. Аннотации упрощает представление о том, повлияли ли изменения на производительность приложения. Они могут быть автоматически созданы системой [сборки трубопроводов Azure.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Заметки можно также создавать, чтобы помечать какие-либо события, используя PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Выпуск аннотаций со сборкой трубопроводов Azure

Аннотации к выпуску — это функция облачной службы Azure Pipelines Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Установка расширения заметок (однократно)

Чтобы создать аннотации к выпуску, необходимо установить одно из многочисленных расширений Azure DevOps, доступных в Visual Studio Marketplace.

1. Вопийте в проекте [Azure DevOps.](https://azure.microsoft.com/services/devops/)
   
1. На странице [расширения аннотации](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) Visual Studio Marketplace выберите организацию Azure DevOps, а затем выберите **Install,** чтобы добавить расширение в организацию Azure DevOps.
   
   ![Выберите организацию Azure DevOps, а затем выберите Установку.](./media/annotations/1-install.png)
   
Для организации Azure DevOps необходимо установить расширение только один раз. Теперь можно настроить аннотации к выпуску для любого проекта в вашей организации.

### <a name="configure-release-annotations"></a>Настройка заметок к выпуску

Создайте отдельный ключ API для каждого из шаблонов выпуска Azure Pipelines.

1. Вопийте на [портале Azure](https://portal.azure.com) и открывайте ресурс Application Insights, который отслеживает ваше приложение. Или, если у вас его нет, [создайте новый ресурс Application Insights.](../../azure-monitor/app/app-insights-overview.md)
   
1. Откройте вкладку **API Access** и скопируйте **идентификатор Application Insights.**
   
   ![Под API Access скопируйте идентификатор приложения.](./media/annotations/2-app-id.png)

1. В отдельном окне браузера откройте или создайте шаблон выпуска, который управляет развертыванием azure Pipelines.
   
1. Выберите **задачу добавить,** а затем выберите задачу **аннотации application Application Insights** из меню.
   
   ![Выберите Добавить задачу и выберите Аннотацию выпуска Application Insights.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Задача аннотации релиза в настоящее время поддерживает только агенты на базе Windows; он не будет работать на Linux, macOS или других типах агентов.
   
1. В соответствии с **идентификатором приложения,** вставьте id Application Insights, который вы скопировали со вкладки **API Access.**
   
   ![Вставьте идентификатор исследования приложений](./media/annotations/4-paste-app-id.png)
   
1. Вернувшись в окно **API Доступа** К приложению, выберите **«Создайте ключ API».** 
   
   ![Во вкладке API Access выберите «Создайте ключ API».](./media/annotations/5-create-api-key.png)
   
1. В окне **ключа Create API** введите описание, выберите **аннотации write,** а затем выберите **ключ generate.** Скопируйте новый ключ.
   
   ![В окне ключа Create API введите описание, выберите аннотации write, а затем выберите ключ для создания.](./media/annotations/6-create-api-key.png)
   
1. В окне шаблона релиза на вкладке **Переменные** выберите **Добавить,** чтобы создать переменное определение для нового ключа API.

1. Под **названием,** введите, `ApiKey`и под **значением,** вставьте клавишу API, который вы скопировали из вкладки **API Access.**
   
   ![Во вкладке Azure DevOps Variables выберите Добавить, назовите переменную ApiKey и вставьте ключ API под значение.](./media/annotations/7-paste-api-key.png)
   
1. Выберите **Сохранить** в главном окне шаблона выпуска, чтобы сохранить шаблон.

## <a name="view-annotations"></a>Просмотр заметок


   > [!NOTE]
   > Аннотации к выпуску в настоящее время не доступны в панели Метрики Application Insights

Теперь, когда вы используете шаблон релиза для развертывания нового релиза, аннотация отправляется в Application Insights. Аннотации можно просматривать в следующих местах:

Панель использования, где вы также имеете возможность вручную создавать аннотации релиза:

![Скриншот диаграммы с количеством посещений пользователей, отображаемых в течение нескольких часов. Аннотации релиза отображаются в виде зеленых контрольных знаков над графиком, указывающими момент времени, когда произошел релиз](./media/annotations/usage-pane.png)

В любом запросе на основе журналов, где визуализация отображает время вдоль x-оси.

![Скриншот панели трудовых книжек с журналом на основе временных рядов с аннотациями](./media/annotations/workbooks-annotations.png)

Для включения аннотаций в вашей трудовой книжке перейдите в **Расширенные настройки** и выберите **аннотации Show.**

![Скриншот меню Расширенные настройки со словами показать аннотации выделены с галочкой рядом с настройкой, чтобы включить его.](./media/annotations/workbook-show-annotations.png)

Выберите любой маркер аннотации, чтобы открыть сведения о выпуске, включая запросора, ветку управления исходным кодом, конвейер выпуска и среду.

## <a name="create-custom-annotations-from-powershell"></a>Создание настраиваемых заметок в PowerShell
Вы можете использовать скрипт [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell от GitHub для создания аннотаций от любого процесса, который вам нравится, без использования Azure DevOps. 

1. Сделать локальную копию [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Используйте этапы предыдущей процедуры, чтобы получить идентификатор Application Insights и создать ключ API из вкладки **API Access** Application.
   
1. Позвоните в скрипт PowerShell со следующим кодом, заменив заполнители с угловыми скобками на ваши значения. Необязательны. `-releaseProperties` 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Можно изменить скрипт, например, для создания аннотаций для прошлого.

## <a name="next-steps"></a>Следующие шаги

* [Создание рабочих элементов](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Автоматизация с помощью PowerShell](../../azure-monitor/app/powershell.md)
