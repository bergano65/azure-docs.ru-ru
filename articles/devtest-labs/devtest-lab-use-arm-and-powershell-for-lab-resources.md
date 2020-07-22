---
title: Создание или изменение лабораторий с помощью шаблонов Azure Resource Manager
description: Сведения об использовании шаблонов Azure Resource Manager с помощью PowerShell для автоматического создания или изменения лабораторных в DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: af225e8cfade30a64aa1f4cf747e2a4d8d3f895f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483182"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Автоматическое создание и изменение лабораторий помощью шаблонов Azure Resource Manager и PowerShell

DevTest Labs предоставляет множество шаблонов Azure Resource Manager и сценариев PowerShell, которые позволяют быстро и автоматически создавать новые или изменять существующие лаборатории и затем развертывать эти ресурсы.

Эта статья описывает использования этих шаблонов и сценариев для автоматизации создания, изменения и развертывания лабораторий. В ней также показано, где найти дополнительные сведения об использовании PowerShell для выполнения общих задач в DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Шаг 1. Сбор шаблонов и сценариев
Предварительно созданные [шаблоны Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) и [сценарии PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) можно найти в общедоступном [репозитории GitHub](https://github.com/Azure/azure-devtestlab). Используйте их в исходном виде или настраивайте их и сохраняйте в своем [частном репозитории Git](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Шаг 2. Изменение шаблона Azure Resource Manager
Если вы никогда не создавали шаблоны, выполните действия из раздела [Создание первого шаблона Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).

Кроме того, в разделе [Рекомендации по созданию шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) содержится множество рекомендаций и указаний, облегчающих создание надежных и простых в использовании шаблонов Azure Resource Manager. Как правило, вы будете использовать вариант одного из приведенных подходов или примеров и измените шаблон согласно своим потребностям.

## <a name="step-3-deploy-resources-with-powershell"></a>Шаг 3. Развертывание ресурсов с помощью PowerShell
После настройки шаблонов и сценариев выполните шаги для [развертывания ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Эта статья содержит общие сведения об использовании Azure PowerShell с шаблонами Azure Resource Manager для развертывания ресурсов в Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Общие задачи, которые можно выполнять в DevTest Labs с помощью PowerShell
Существует множество других распространенных задач, которые можно автоматизировать с помощью PowerShell. В следующих разделах документации описаны шаги, необходимые для выполнения этих задач.

* [Создание пользовательского образа из VHD-файла с помощью PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Отправка VHD-файла в учетную запись хранения лаборатории с помощью PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Добавление внешнего пользователя в лабораторию с помощью PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Создание пользовательской роли лаборатории с помощью PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Дальнейшие шаги
* Вы можете научиться создавать [частный репозиторий Git](devtest-lab-add-artifact-repo.md) для хранения пользовательских шаблонов или сценариев.
* Изучите [шаблоны Azure Resource Manager из коллекции шаблонов](https://github.com/Azure/azure-quickstart-templates)быстрого запуска Azure.
