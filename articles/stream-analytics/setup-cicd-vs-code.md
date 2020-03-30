---
title: Развертывание задания Azure Stream Analytics с помощью пакета CI/CD npm
description: В этой статье описывается, как использовать пакет Azure Stream Analytics CI/CD npm для настройки непрерывного процесса интеграции и развертывания.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962227"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Развертывание задания Azure Stream Analytics с помощью пакета CI/CD npm 

Для настройки непрерывного процесса интеграции и развертывания для заданий Stream Analytics можно использовать пакет Azure Stream Analytics CI/CD npm. В этой статье описывается, как использовать пакет npm в целом с любой системой CI/CD, а также конкретные инструкции по развертыванию с azure Pipelines.

Для получения дополнительной информации о развертывании с Powershell см. [развертывание с файлом шаблона менеджера ресурсов и Azure PowerShell.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) Вы также можете узнать больше о том, как [использовать объект в качестве параметра в шаблоне resource Manager.](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)

## <a name="build-the-vs-code-project"></a>Построить проект VS Code

Можно обеспечить непрерывную интеграцию и развертывание заданий Azure Stream Analytics с помощью пакета **asa-streamanalytics-cicd** npm. Пакет npm предоставляет инструменты для создания шаблонов Управления ресурсами Azure для [проектов Code Analytics Visual Studio Code.](quick-create-vs-code.md) Он может быть использован на Windows, macOS и Linux без установки визуального кода студии.

Вы можете [загрузить пакет](https://www.npmjs.com/package/azure-streamanalytics-cicd) напрямую или установить его по [всему миру](https://docs.npmjs.com/downloading-and-installing-packages-globally) через `npm install -g azure-streamanalytics-cicd` команду. Это рекомендуемый подход, который также может быть использован в задаче сценария PowerShell или Azure CLI для **сборки конвейера в Azure Pipelines.**

После установки пакета используйте следующую команду для вывода шаблонов управления ресурсами Azure. Аргумент **scriptPath** — это абсолютный путь к файлу **asaql** в вашем проекте. Убедитесь, что файлы asaproj.json и JobConfig.json находятся в одной папке с файлом скрипта. Если **выводной путь** не указан, шаблоны будут помещены в папку **Deploy** в папке **ячейки** проекта.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Пример (на macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Когда проект Stream Analytics Visual Studio Code успешно строится, он генерирует следующие два шаблона шаблона Azure Resource Manager под **ячейкой/"Debug/Retail/Deploy:"** 

*  файл шаблона Resource Manager;

       [ProjectName].JobTemplate.json 

*  файл параметров Resource Manager.

       [ProjectName].JobTemplate.parameters.json   

Параметры по умолчанию в файле parameters.json взяты из настроек в проекте Visual Studio Code. При необходимости развертывания в другой среде замените соответствующие параметры.

> [!NOTE]
> Для всех учетных данных значения по умолчанию имеют значение NULL. Их **необходимо** установить перед развертыванием в облаке.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Развертывание с использованием Azure Pipelines

В этом разделе подробно описано, как создавать [и](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) [выпускать](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) конвейеры Azure с помощью npm.

Откройте веб-браузер и перейдите к проекту Azure Stream Analytics Visual Studio Code.

1. Под **трубопроводами** в левом меню навигации выберите **сборки**. Затем выберите **Новый конвейер**

   ![Создание нового трубопровода Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Выберите **Используйте классический редактор** для создания конвейера без YAML.

3. Выберите тип исходного кода, проект команды и репозиторий. Затем выберите **Продолжить**.

   ![Выберите проект Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. На странице **«Выбрать шаблон»** выберите **«Пустое задание».**

### <a name="add-npm-task"></a>Добавление задачи npm

1. На странице **Задачи** выберите знак плюс рядом с **работой агента 1.** Введите "npm" в поиске задач и выберите **npm.**

   ![Выберите задачу npm](./media/setup-cicd-vs-code/search-npm.png)

2. Дайте задаче **имя дисплея.** Измените опцию **командования** на *пользовательский* и введите следующую команду в **Командовании и аргументах.** Оставьте оставшиеся параметры по умолчанию.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Введите конфигурации для задачи npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Добавление задачи командной строки

1. На странице **Задачи** выберите знак плюс рядом с **работой агента 1.** Поиск **командной строки**.

2. Дайте задаче **имя дисплея** и введите следующий сценарий. Измените сценарий с вашим именем репозитория и именем проекта.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Введите конфигурации для задачи командной строки](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Добавление задачи файлов копирования

1. На странице **Задачи** выберите знак плюс рядом с **работой агента 1.** Поиск **файлов копирования**. Затем введите следующие конфигурации.

   |Параметр|Входные данные|
   |-|-|
   |Отображаемое имя|Копирование файлов по: $(build.artifactstagingdirectory)|
   |Исходная папка|`$(system.defaultworkingdirectory)`| 
   |Содержимое| `**\Deploy\**` |
   |Целевой Фолдер| `$(build.artifactstagingdirectory)`|

   ![Введите конфигурации для задачи копирования](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Добавление задачи артефактов сборки публикации

1. На странице **Задачи** выберите знак плюс рядом с **работой агента 1.** Поиск **артефактов сборки публикации** и выберите опцию с черной иконой стрелки. 

2. Не изменяйте ни одной из конфигураций по умолчанию.

### <a name="save-and-run"></a>Сохранение и запуск

После того как вы закончите добавлять npm, командную строку, копировать файлы и публиковать задачи артефактов сборки, выберите **Очередь Сохранить &.** Когда вам будет предложено, введите комментарий сохранить и выберите **Сохранить и запустить**.

## <a name="release-with-azure-pipelines"></a>Выпуск с помощью трубопроводов Azure

Откройте веб-браузер и перейдите к проекту Azure Stream Analytics Visual Studio Code.

1. Под **трубопроводами** в левом меню навигации выберите **Релизы**. Затем выберите **Новый конвейер**.

2. Выберите **начало с пустой задания.**

3. В поле **Артефакты** выберите **и добавьте артефакт.** Под **Source**выберите только что созданный конвейер сборки и выберите **Добавить.**

   ![Введите артефакт конвейера сборки](./media/setup-cicd-vs-code/build-artifact.png)

4. Измените название **этапа 1** для развертывания **задания для тестирования среды.**

5. Добавьте новый этап и назовите **его задание развертыванием в производственную среду.**

### <a name="add-tasks"></a>Добавление задач

1. Из задачи отсева выберите **задание развертывание для тестирования среды.** 

2. Выберите **+** следующую **работу «Агент»** и ищите *развертывание группы ресурсов Azure.* Задайте следующие параметры:

   |Параметр|Значение|
   |-|-|
   |Отображаемое имя| *Развертывание myASAJob*|
   |Подписка Azure.| Выберите свою подписку.|
   |Действие| *Создание или изменение группы ресурсов*|
   |Группа ресурсов| Выберите имя для группы ресурсов тестирования, которая будет содержать задание Stream Analytics.|
   |Расположение|Выберите местоположение группы ресурсов тестирования.|
   |Расположение шаблона| *Связанный артефакт*|
   |Шаблон| $(Build.ArtifactStagingDirectory) |
   |Параметры шаблона|($(Build.ArtifactStagingDirectory)|
   |Переопределение параметров шаблона|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Режим развертывания|Добавочное|

3. От выпадения задач выберите **задание Развертывание в производственную среду.**

4. Выберите **+** следующую **работу «Агент»** и ищите *развертывание группы ресурсов Azure.* Задайте следующие параметры:

   |Параметр|Значение|
   |-|-|
   |Отображаемое имя| *Развертывание myASAJob*|
   |Подписка Azure.| Выберите свою подписку.|
   |Действие| *Создание или изменение группы ресурсов*|
   |Группа ресурсов| Выберите название для группы производственных ресурсов, которая будет содержать задание Stream Analytics.|
   |Расположение|Выберите местоположение группы производственных ресурсов.|
   |Расположение шаблона| *Связанный артефакт*|
   |Шаблон| $(Build.ArtifactStagingDirectory) |
   |Параметры шаблона|($(Build.ArtifactStagingDirectory)|
   |Переопределение параметров шаблона|-Input_IoTHub1_iotHubNamespace $(событие)|
   |Режим развертывания|Добавочное|

### <a name="create-release"></a>Создание релиза

Чтобы создать релиз, выберите **Создать релиз** в правом верхнем углу.

![Создание выпуска с помощью проводов Azure](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

Чтобы в качестве приемника выходных данных использовать управляемое удостоверение для Azure Data Lake Storage 1-го поколения, предоставите доступ субъекту-службе с помощью PowerShell перед развертыванием в Azure. Дополнительные сведения см. в разделе о [развертывании ADLS 1-го поколения с управляемым удостоверением с помощью шаблона Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Дальнейшие действия

* [Быстрый запуск: Создание облачной работы Azure Stream Analytics в коде Visual Studio (Предварительный просмотр)](quick-create-vs-code.md)
* [Тестовая аналитика потока запрашивает локально с помощью визуального кода studio (Предварительный просмотр)](visual-studio-code-local-run.md)
* [Исследуйте аналитику потоков Azure с помощью визуального кода студии (Предварительный просмотр)](visual-studio-code-explore-jobs.md)
