---
title: Развертывание вложенных сред шаблонов в Лабораториях Azure DevTest
description: Узнайте, как развертывать вложенные шаблоны диспетчера ресурсов Azure, чтобы обеспечить среду с azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168823"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Развертывание вложенных шаблонов управления ресурсами Azure для тестирования сред
Вложенное развертывание позволяет выполнять другие шаблоны управления ресурсами Azure из основного шаблона диспетчера ресурсов. Это позволяет разложить развертывание на набор целевых и целевых шаблонов. Он предоставляет преимущества с точки зрения тестирования, повторного использования и читаемости. Статья [С использованием связанных шаблонов при развертывании ресурсов Azure](../azure-resource-manager/templates/linked-templates.md) дает хороший обзор этого решения с несколькими образцами кода. В этой статье приводится пример, характерный для лабораторий Azure DevTest Labs. 

## <a name="key-parameters"></a>Ключевые параметры
Хотя вы можете создавать свой собственный шаблон Resource Manager с нуля, мы рекомендуем использовать [проект Группы ресурсов Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) в Visual Studio, что упрощает разработку и отладку шаблонов. При добавлении вложенного ресурса развертывания в azuredeploy.json Visual Studio добавляет несколько элементов, чтобы сделать шаблон более гибким. Эти элементы включают в себя субфолдер со вторичным файлом шаблона и параметров, переменные имена в файле основного шаблона и два параметра для местоположения хранилища для новых файлов. **_artifactsLocation** и **_artifactsLocationSasToken** являются ключевыми параметрами, которые использует DevTest Labs. 

Если вы не знакомы с тем, как DevTest Labs работает с средами, [см.](devtest-lab-create-environment-from-arm.md) Ваши шаблоны хранятся в репозитории, связанном с лабораторией в DevTest Labs. При создании новой среды с этими шаблонами файлы перемещаются в контейнер хранения Azure в лаборатории. Чтобы определить и скопировать вложенные файлы, DevTest Labs определяет _artifactsLocation и _artifactsLocationSasToken параметров и копирует субфолилы до контейнера для хранения. Затем он автоматически вставляет в параметры маркера определения местоположения и общей подписи доступа (SaS). 

## <a name="nested-deployment-example"></a>Пример развертывания вложенных
Вот простой пример вложенного развертывания:

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

Папка в репозитории, содержащая этот `nestedtemplates` шаблон, имеет субфолдер с файлами **NestOne.json** и **NestOne.parameters.json.** В **azuredeploy.json**URI для шаблона строится с использованием местоположения артефактов, вложенной папки шаблона, вложенного имени файла шаблона. Аналогичным образом URI для параметров строится с использованием местоположения артефактов, вложенной папки шаблона и файла параметров для вложенного шаблона. 

Вот образ той же структуры проекта в Visual Studio: 

![Структура проекта в Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Вы можете добавить дополнительные папки в основной папке, но не глубже, чем один уровень. 

## <a name="next-steps"></a>Дальнейшие действия
Подробности о средах смотрите следующие статьи: 

- [Создание сред со множеством виртуальных машин и ресурсов PaaS с помощью шаблонов Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Настройка и использование общедоступных сред в Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Подключение среды к виртуальной сети вашей лаборатории в Лабораториях Azure DevTest Labs](connect-environment-lab-virtual-network.md)