---
title: Глобальные параметры
description: Установка глобальных параметров для каждой среды фабрики данных Azure
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854356"
---
# <a name="global-parameters-in-azure-data-factory"></a>Глобальные параметры в фабрике данных Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Глобальные параметры — это константы в фабрике данных, которые могут использоваться конвейером в любом выражении. Они полезны при наличии нескольких конвейеров с одинаковыми именами и значениями параметров. При повышении фабрики данных с помощью процесса непрерывной интеграции и развертывания (CI/CD) эти параметры можно переопределить в каждой среде. 

## <a name="creating-global-parameters"></a>Создание глобальных параметров

Чтобы создать глобальный параметр, перейдите на вкладку *глобальные параметры* в разделе *Управление* . Выберите **создать** , чтобы открыть боковую панель навигации.

![Создание глобальных параметров](media/author-global-parameters/create-global-parameter-1.png)

В боковой панели навигации введите имя, выберите тип данных и укажите значение параметра.

![Создание глобальных параметров](media/author-global-parameters/create-global-parameter-2.png)

После создания глобального параметра его можно изменить, щелкнув имя параметра. Чтобы изменить сразу несколько параметров, выберите **изменить все**.

![Создание глобальных параметров](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Использование глобальных параметров в конвейере

Глобальные параметры можно использовать в любом [выражении конвейера](control-flow-expression-language-functions.md). Если конвейер ссылается на другой ресурс, например набор данных или поток данных, можно передать значение глобального параметра с помощью параметров этого ресурса. На глобальные параметры указывают ссылки как `pipeline().globalParameters.<parameterName>` .

![Использование глобальных параметров](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a>Глобальные параметры в CI/CD

Глобальные параметры имеют уникальные процессы CI/CD относительно других сущностей в фабрике данных Azure. При публикации фабрики или экспорте шаблона ARM с глобальными параметрами создается папка с именем *globalParameters* с файлом с именем *your-factory-name_GlobalParameters.js*. Этот файл представляет собой объект JSON, который содержит каждый глобальный тип параметра и значение в опубликованной фабрике.

![Публикация глобальных параметров](media/author-global-parameters/global-parameters-adf-publish.png)

Если вы выполняете развертывание в новой среде, например в ТЕСТовой или ПРОИЗВ. рекомендуется создать копию этого файла глобальных параметров и перезаписать соответствующие значения, относящиеся к конкретной среде. При повторной публикации исходного файла глобальных параметров будет перезаписан, но копия для другой среды будет нетронутой.

Например, если имеется фабрика с именем "ADF-DEV" и глобальный параметр типа String с именем "среда" со значением "Dev", то при публикации файла с именем *ADF-DEV_GlobalParameters.jsв* будет создан. При развертывании в фабрике тестов с именем "ADF_TEST" Создайте копию JSON-файла (например, с именем ADF-TEST_GlobalParameters.jsв) и замените значения параметров значениями, зависящими от среды. Параметр "Environment" может иметь значение "Test". 

![Развертывание глобальных параметров](media/author-global-parameters/powershell-task.png)

Используйте приведенный ниже сценарий PowerShell для повышения уровня глобальных параметров в дополнительных средах. Добавьте Azure PowerShell задачу DevOps перед Шаблоны развертывания ARM. В задаче DevOps необходимо указать расположение нового файла параметров, целевую группу ресурсов и целевую фабрику данных.

> [!NOTE]
> Чтобы развернуть глобальные параметры с помощью PowerShell, необходимо использовать по меньшей мере версию 4.4.0 модуля AZ.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Дальнейшие шаги

* Сведения о [процессе непрерывной интеграции и развертывания](continuous-integration-deployment.md) фабрики данных Azure
* Узнайте, как использовать [язык выражений потока управления](control-flow-expression-language-functions.md)