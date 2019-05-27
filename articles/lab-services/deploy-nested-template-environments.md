---
title: Развертывание вложенных сред шаблона Resource Manager в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как развертывание вложенных шаблонов Azure Resource Manager для предоставления сред с помощью Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835290"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Развертывание вложенных шаблонов Azure Resource Manager для среды тестирования
Вложенное развертывание позволяет выполнять другие шаблоны Azure Resource Manager из в основной шаблон Resource Manager. Он позволяет разбивать развертывания набор шаблонов, назначать и конкретной цели. Он предоставляет преимущества с точки зрения тестирования, повторного использования и удобочитаемости. Статья [использование связанных шаблонов при развертывании ресурсов Azure](../azure-resource-manager/resource-group-linked-templates.md) предоставляет хороший обзор этого решения с несколько примеров кода. В этой статье приведен пример, относящиеся к Azure DevTest Labs. 

## <a name="key-parameters"></a>Основные параметры
Вы можете создать собственный шаблон Resource Manager с нуля, а мы рекомендуем использовать [проект группы ресурсов Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) в Visual Studio, который позволяет легко разрабатывать и отлаживать шаблонов. При добавлении вложенного развертывания ресурсов в azuredeploy.json в Visual Studio добавляет несколько элементов, чтобы сделать шаблон более гибким. Эти элементы включают во вложенную папку с вторичный файл шаблона и параметров, имена переменных в файле основного шаблона и два параметра для расположения хранилища для новых файлов. **_ArtifactsLocation** и **_artifactsLocationSasToken** DevTest Labs использует параметры ключа. 

Если вы не знакомы с принципами работы DevTest Labs с помощью сред, см. в разделе [Создание сред со множеством виртуальных Машин и ресурсов PaaS с помощью шаблонов Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Шаблоны хранятся в репозитории, связанного с в лабораторию в DevTest Labs. При создании новой среды с помощью этих шаблонов, файлы перемещаются в контейнер хранилища Azure в лаборатории. Чтобы иметь возможность идентифицировать и скопируйте вложенные файлы, DevTest Labs определяет параметры _artifactsLocation и _artifactsLocationSasToken и копирует вложенные папки к контейнеру хранилища. Затем он автоматически вставляет расположение и токен подписи общего доступа (SaS) в параметры. 

## <a name="nested-deployment-example"></a>Пример вложенных развертывания
Ниже приведен простой пример вложенное развертывание:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

Папка в репозиторий, содержащий этот шаблон содержит вложенную папку `nestedtemplates` с файлами **NestOne.json** и **NestOne.parameters.json**. В **azuredeploy.json**, URI for шаблон создается с использованием расположения артефактов, вложенный шаблон папки, вложенных имя файла шаблона. Аналогичным образом URI для параметров сборка выполняется с помощью расположения артефактов, вложенный шаблон папку и файл параметров для вложенного шаблона. 

Ниже приведен снимок же структура проекта в Visual Studio. 

![Структура проекта в Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Можно добавить дополнительные папки, в основной папке, но не все большее, чем один уровень. 

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями, Дополнительные сведения о средах: 

- [Создание сред со множеством виртуальных машин и ресурсов PaaS с помощью шаблонов Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Настройка и использование общих средах в Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Подключение среды к виртуальной сети в лаборатории в Azure DevTest Labs](connect-environment-lab-virtual-network.md)