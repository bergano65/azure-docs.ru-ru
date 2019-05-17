---
title: Непрерывная интеграция и разработка с помощью пакета Azure Stream Analytics Непрерывной интеграции и NuGet
description: В этой статье описывается использование Azure Stream Analytics Непрерывной интеграции и NuGet-пакет для настройки непрерывной интеграции и процесса развертывания.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: f34139dafffe3d4890f17988114dffdd8b480d2d
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827315"
---
# <a name="continuously-integrate-and-develop-with-azure-stream-analytics-cicd-nuget-package"></a>Непрерывная интеграция и разработка с помощью пакета Azure Stream Analytics Непрерывной интеграции и NuGet
В этой статье описывается использование Azure Stream Analytics Непрерывной интеграции и пакета NuGet для настройки непрерывной интеграции и процесса развертывания.

Чтобы получить поддержку MSBuild, используйте [инструменты Stream Analytics для Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) версии 2.3.0000.0 или выше.

Доступен пакет NuGet: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Он предоставляет MSBuild, локальное выполнение и средства развертывания, которые поддерживают процесс непрерывной интеграции и развертывания из [Stream Analytics Visual Studio проекты](stream-analytics-vs-tools.md). 
> [!NOTE]
> Пакет NuGet может использоваться только с версией 2.3.0000.0 или более поздней версией средств Stream Analytics для Visual Studio. При наличии проектов, созданных в предыдущих версиях средств Visual Studio, откройте его с помощью версии 2.3.0000.0 или более поздней версии и сохраните. После этого будут включены новые возможности. 

Дополнительные сведения см. в статье [Использование инструментов Azure Stream Analytics для Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Как и в стандартном интерфейсе Visual Studio MSBuild, для выполнения сборки проекта имеются две возможности. Можно щелкнуть правой кнопкой мыши проект и выбрать **Собрать**. Можно также использовать **MSBuild** в пакете NuGet из командной строки.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

При успешном выполнении сборки проекта Stream Analytics Visual Studio создаются два файла шаблона Azure Resource Manager в папке **bin/[Debug/Retail]/Deploy**: 

*  файл шаблона Resource Manager;

       [ProjectName].JobTemplate.json 

*  файл параметров Resource Manager.

       [ProjectName].JobTemplate.parameters.json   

Параметры по умолчанию в файле parameters.json на основе параметров в проекте Visual Studio. При необходимости развертывания в другой среде замените соответствующие параметры.

> [!NOTE]
> Для всех учетных данных значения по умолчанию имеют значение NULL. Их **необходимо** установить перед развертыванием в облаке.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Узнайте, как [развернуть файл шаблона Resource Manager с помощью Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Узнайте, как [использовать объект в качестве параметра в шаблоне Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Чтобы в качестве приемника выходных данных использовать управляемое удостоверение для Azure Data Lake Storage 1-го поколения, предоставите доступ субъекту-службе с помощью PowerShell перед развертыванием в Azure. Дополнительные сведения см. в разделе о [развертывании ADLS 1-го поколения с управляемым удостоверением с помощью шаблона Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Программа командной строки

### <a name="build-the-project"></a>Сборка проекта
В пакете NuGet есть служебная программа командной строки, которая называется **SA.exe**. Она поддерживает сборку проекта и локальное тестирование на произвольном компьютере, которое можно использовать в процессе непрерывной интеграции и непрерывной доставки. 

Файлы развертывания по умолчанию помещаются в текущем каталоге. С помощью параметра -OutputPath можно указать выходной путь.

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Тестирование сценария локально

Если в проекте указаны локальные входные файлы Visual Studio, можно выполнить автоматическое тестирование сценария с помощью команды *localrun*. Выходной результат помещается в текущий каталог.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Создайте файл определения задания для использования с API PowerShell для Stream Analytics.

Команда *arm* принимает шаблон задания и файл параметров шаблона задания, созданный путем сборки в качестве входных данных. Затем объедините их в файл определения задания JSON, который может использоваться с API PowerShell для Stream Analytics.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Пример:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство Создание задания Azure Stream Analytics cloud в Visual Studio](stream-analytics-quick-create-vs.md)
* [Локальное тестирование запросов Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Просмотр заданий Azure Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools.md)
