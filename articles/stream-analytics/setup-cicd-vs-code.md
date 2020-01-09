---
title: Использование пакета Azure Stream Analytics CI/CD NPM
description: В этой статье описывается, как Azure Stream Analytics использовать пакет NPM CI/CD для настройки процесса непрерывной интеграции и развертывания.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ed1f6ebda81a7f036b5e09d15ef4a27323aa9b0d
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660874"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Использование пакета Stream Analytics CI/CD NPM
В этой статье описывается, как использовать пакет NPM CI/CD Azure Stream Analytics, чтобы настроить процесс непрерывной интеграции и развертывания.

## <a name="build-the-vs-code-project"></a>Построение проекта VS Code

Вы можете включить непрерывную интеграцию и развертывание для Azure Stream Analytics заданий с помощью пакета **ASA-streamanalytics-cicd** NPM. Пакет NPM предоставляет средства для создания шаблонов Azure Resource Manager [Stream Analytics Visual Studio Code проектов](quick-create-vs-code.md). Его можно использовать в Windows, macOS и Linux без установки Visual Studio Code.

Вы можете [скачать пакет](https://www.npmjs.com/package/azure-streamanalytics-cicd) напрямую или установить его [глобально](https://docs.npmjs.com/downloading-and-installing-packages-globally) с помощью команды `npm install -g azure-streamanalytics-cicd`. Это рекомендуемый подход, который также можно использовать в задаче "Скрипт" PowerShell или Azure CLI конвейера сборки в **конвейерах Azure DevOps**.

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
Узнайте, как [развернуть файл шаблона Resource Manager с помощью Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Узнайте, как [использовать объект в качестве параметра в шаблоне Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Чтобы в качестве приемника выходных данных использовать управляемое удостоверение для Azure Data Lake Storage 1-го поколения, предоставите доступ субъекту-службе с помощью PowerShell перед развертыванием в Azure. Дополнительные сведения см. в разделе о [развертывании ADLS 1-го поколения с управляемым удостоверением с помощью шаблона Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Создание Azure Stream Analytics облачного задания в Visual Studio Code (Предварительная версия)](quick-create-vs-code.md)
* [Тестирование Stream Analytics запросов локально с Visual Studio Code (Предварительная версия)](visual-studio-code-local-run.md)
* [Изучение Azure Stream Analytics с Visual Studio Code (Предварительная версия)](visual-studio-code-explore-jobs.md)
