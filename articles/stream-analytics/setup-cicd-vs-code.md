---
title: Развертывание задания Azure Stream Analytics с помощью пакета CI/CD NPM
description: В этой статье описывается, как Azure Stream Analytics использовать пакет NPM CI/CD для настройки процесса непрерывной интеграции и развертывания.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76962227"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Развертывание задания Azure Stream Analytics с помощью пакета CI/CD NPM 

С помощью пакета Azure Stream Analytics CI/CD NPM можно настроить процесс непрерывной интеграции и развертывания для заданий Stream Analytics. В этой статье описывается использование пакета NPM в целом с любой системой CI/CD, а также приведены инструкции по развертыванию с помощью Azure Pipelines.

Дополнительные сведения о развертывании с помощью PowerShell см. в статье [развертывание с помощью Диспетчер ресурсов файла шаблона и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Вы также можете узнать больше об [использовании объекта в качестве параметра в шаблоне диспетчер ресурсов](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Построение проекта VS Code

Вы можете включить непрерывную интеграцию и развертывание для Azure Stream Analytics заданий с помощью пакета **ASA-streamanalytics-cicd** NPM. Пакет NPM предоставляет средства для создания шаблонов Azure Resource Manager [Stream Analytics Visual Studio Code проектов](quick-create-vs-code.md). Его можно использовать в Windows, macOS и Linux без установки Visual Studio Code.

Вы можете [скачать пакет](https://www.npmjs.com/package/azure-streamanalytics-cicd) напрямую или установить его [глобально](https://docs.npmjs.com/downloading-and-installing-packages-globally) с `npm install -g azure-streamanalytics-cicd` помощью команды. Это рекомендуемый подход, который также можно использовать в задаче "Скрипт" PowerShell или Azure CLI конвейера сборки в **Azure pipelines**.

После установки пакета используйте следующую команду для вывода шаблонов Azure Resource Manager. Аргумент **scriptPath** — это абсолютный путь к файлу **asaql** в проекте. Убедитесь, что файлы асапрож. JSON и Жобконфиг. JSON находятся в одной папке с файлом скрипта. Если **outputPath** не указан, шаблоны будут помещены в папку **deploy** в папке **bin** проекта.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Пример (на macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Когда проект Stream Analytics Visual Studio Code успешно строится, он создает следующие два файла шаблонов Azure Resource Manager в папке **bin/[Debug/Retail]/Deploy** : 

*  файл шаблона Resource Manager;

       [ProjectName].JobTemplate.json 

*  файл параметров Resource Manager.

       [ProjectName].JobTemplate.parameters.json   

Параметры по умолчанию в файле Parameters. JSON относятся к параметрам в проекте Visual Studio Code. При необходимости развертывания в другой среде замените соответствующие параметры.

> [!NOTE]
> Для всех учетных данных значения по умолчанию имеют значение NULL. Их **необходимо** установить перед развертыванием в облаке.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Развертывание с использованием Azure Pipelines

В этом разделе подробно описано, как создавать конвейеры [сборки](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) и [выпуска](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) Azure pipelines с помощью NPM.

Откройте веб-браузер и перейдите к Azure Stream Analytics Visual Studio Code проекта.

1. В разделе **конвейеры** в меню навигации слева выберите **сборки**. Выберите **Новый конвейер** .

   ![Создание нового конвейера Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Выберите **использовать классический редактор** для создания конвейера без YAML.

3. Выберите тип источника, командный проект и репозиторий. Затем нажмите **Продолжить**.

   ![Выбор проекта Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. На странице **Выбор шаблона** выберите **пустое задание**.

### <a name="add-npm-task"></a>Добавить задачу NPM

1. На странице **задачи** щелкните знак «плюс» рядом с **заданием агента 1**. В области поиска задач введите "NPM" и выберите **NPM**.

   ![Выбор задачи NPM](./media/setup-cicd-vs-code/search-npm.png)

2. Присвойте задаче **Отображаемое имя**. Измените параметр **команды** на *Custom* и введите следующую команду в **команде и аргументах**. Оставьте остальные параметры по умолчанию.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Введите конфигурации для задачи NPM](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Добавить задачу командной строки

1. На странице **задачи** щелкните знак «плюс» рядом с **заданием агента 1**. Найдите **командную строку**.

2. Присвойте задаче **Отображаемое имя** и введите следующий скрипт. Измените сценарий, указав имя репозитория и имя проекта.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Введите конфигурации для задачи командной строки](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Задача "добавить файлы копирования файлов"

1. На странице **задачи** щелкните знак «плюс» рядом с **заданием агента 1**. Поиск **копий файлов**. Затем введите следующие конфигурации.

   |Параметр|Входные данные|
   |-|-|
   |Отображаемое имя|Копировать файлы в: $ (Build. artifactstagingdirectory)|
   |Исходная папка|`$(system.defaultworkingdirectory)`| 
   |Содержимое| `**\Deploy\**` |
   |Целевая папка| `$(build.artifactstagingdirectory)`|

   ![Введите конфигурации для задачи копирования](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Задача "Добавление артефактов сборки публикации"

1. На странице **задачи** щелкните знак «плюс» рядом с **заданием агента 1**. Выполните поиск по запросу **Публикация артефактов сборки** и выберите параметр со значком с черной стрелкой. 

2. Не изменяйте конфигурации по умолчанию.

### <a name="save-and-run"></a>Сохранение и запуск

После завершения добавления задач NPM, Командная строка, копирование файлов и публикация артефактов сборки выберите **сохранить & очередь**. При появлении запроса введите комментарий и выберите **сохранить и выполнить**.

## <a name="release-with-azure-pipelines"></a>Выпуск с Azure Pipelines

Откройте веб-браузер и перейдите к Azure Stream Analytics Visual Studio Code проекта.

1. В разделе **конвейеры** в меню навигации слева выберите **выпуски**. Затем выберите **создать конвейер**.

2. Выберите **начать с пустого задания**.

3. В поле **артефакты** выберите **+ Добавить артефакт**. В разделе **источник**выберите только что созданный конвейер сборки и нажмите кнопку **Добавить**.

   ![Введите артефакт конвейера сборки](./media/setup-cicd-vs-code/build-artifact.png)

4. Измените имя **этапа 1** , чтобы **развернуть задание в тестовую среду**.

5. Добавьте новый этап и присвойте ему имя **Развертывание задания в рабочей среде**.

### <a name="add-tasks"></a>Добавление задач

1. В раскрывающемся списке задачи выберите **развернуть задание в тестовой среде**. 

2. Выберите **+** рядом с **заданием агента** и выполните поиск по запросу *Развертывание группы ресурсов Azure*. Задайте следующие параметры:

   |Параметр|Значение|
   |-|-|
   |Отображаемое имя| *Развертывание Мясажоб*|
   |Подписка Azure| Выберите свою подписку.|
   |Действие| *Создание или изменение группы ресурсов*|
   |Группа ресурсов| Выберите имя для тестовой группы ресурсов, которая будет содержать задание Stream Analytics.|
   |Расположение|Выберите расположение группы тестовых ресурсов.|
   |Расположение шаблона| *Связанный артефакт*|
   |Шаблон| \Дроп\мясажоб.жобтемплате.жсон $ (Build. ArtifactStagingDirectory) |
   |Параметры шаблона|($ (Build. ArtifactStagingDirectory) \Дроп\мясажоб.жобтемплате.параметерс.жсон|
   |Переопределение параметров шаблона|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |Режим развертывания|Добавочное|

3. В раскрывающемся списке задачи выберите **развернуть задание в рабочей среде**.

4. Выберите **+** рядом с **заданием агента** и выполните поиск по запросу *Развертывание группы ресурсов Azure*. Задайте следующие параметры:

   |Параметр|Значение|
   |-|-|
   |Отображаемое имя| *Развертывание Мясажоб*|
   |Подписка Azure| Выберите свою подписку.|
   |Действие| *Создание или изменение группы ресурсов*|
   |Группа ресурсов| Выберите имя рабочей группы ресурсов, которая будет содержать задание Stream Analytics.|
   |Расположение|Выберите расположение рабочей группы ресурсов.|
   |Расположение шаблона| *Связанный артефакт*|
   |Шаблон| \Дроп\мясажоб.жобтемплате.жсон $ (Build. ArtifactStagingDirectory) |
   |Параметры шаблона|($ (Build. ArtifactStagingDirectory) \Дроп\мясажоб.жобтемплате.параметерс.жсон|
   |Переопределение параметров шаблона|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |Режим развертывания|Добавочное|

### <a name="create-release"></a>Создать выпуск

Чтобы создать выпуск, выберите **создать выпуск** в правом верхнем углу.

![Создание выпуска с помощью Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

Чтобы в качестве приемника выходных данных использовать управляемое удостоверение для Azure Data Lake Storage 1-го поколения, предоставите доступ субъекту-службе с помощью PowerShell перед развертыванием в Azure. Дополнительные сведения см. в разделе о [развертывании ADLS 1-го поколения с управляемым удостоверением с помощью шаблона Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Дальнейшие шаги

* [Краткое руководство. Создание Azure Stream Analytics облачного задания в Visual Studio Code (Предварительная версия)](quick-create-vs-code.md)
* [Тестирование Stream Analytics запросов локально с Visual Studio Code (Предварительная версия)](visual-studio-code-local-run.md)
* [Изучение Azure Stream Analytics с Visual Studio Code (Предварительная версия)](visual-studio-code-explore-jobs.md)
