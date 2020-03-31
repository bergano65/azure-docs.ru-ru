---
title: Просмотр и использование шаблона менеджера ресурсов Azure виртуальной машины
description: Сведения об использовании шаблона Azure Resource Manager виртуальной машины для создания других виртуальных машин.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 7064fdeec04f4dc5ae2c73c1a3896cf2d10dd01d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169117"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Создание виртуальных машин с использованием шаблона Azure Resource Manager 

При создании виртуальной машины в DevTest Labs на [портале Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) вы можете просмотреть шаблон Azure Resource Manager, прежде чем сохранить виртуальную машину. Этот шаблон можно использовать как основу, чтобы создать больше виртуальных машин лаборатории с теми же настройками.

В этой статье сравниваются шаблоны Resource Manager для нескольких виртуальных машин и для одной виртуальной машины. Кроме того, вы узнаете, как просмотреть и сохранить шаблон при создании виртуальной машины.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Шаблоны Resource Manager для нескольких и одной виртуальной машины
Существует два способа создания виртуальных машин в DevTest Labs с помощью шаблона «Менеджер ресурсов»: предоставление ресурса Microsoft.DevTestLab/labs/virtualmachines или предоставление ресурса Microsoft.Compute/virtualmachines. Каждый из этих ресурсов используется в различных сценариях и требует разных разрешений.

- Шаблоны Resource Manager, использующие тип ресурса Microsoft.DevTestLab/labs/virtualmachines (как объявлено в свойстве resource в шаблоне), могут подготовить отдельные виртуальные машины лаборатории. Каждая виртуальная машина затем отображается в виде отдельного элемента в списке виртуальных машин DevTest Labs:

   ![Список виртуальных машин как отдельных элементов в списке виртуальных машин DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Этот тип шаблона диспетчера ресурсов можно подготовить через команду Azure PowerShell **New-AzResourceGroupDeployment** или через команду az группы az команды Az, **создаваемый**командой Az. Для это требуется иметь разрешения администратора, поэтому пользователи, которым назначена роль пользователя DevTest Labs, не могут выполнить развертывание. 

- Шаблоны Resource Manager, использующие тип ресурса Microsoft.Compute/virtualmachines, могут подготовить несколько виртуальных машин, которые отображаются в качестве отдельной среды в списке виртуальных машин DevTest Labs:

   ![Список виртуальных машин как отдельных элементов в списке виртуальных машин DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Виртуальные машины в одной среде могут находиться под общим управлением и использовать один и тот же жизненный цикл. Пользователи, которым назначена роль пользователя DevTest Labs, могут создавать среды с помощью этих шаблонов, при условии, что администратор настроил лабораторию нужным образом.

В оставшейся части статьи рассматриваются шаблоны Resource Manager, использующие Microsoft.DevTestLab/labs/virtualmachines. Они используются администраторами лаборатории для автоматизации создания виртуальной машины лаборатории (например, запрашиваемые виртуальные машины) или для создания образа золотого стандарта (например, фабрики образов).

В статье [Рекомендации по созданию шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) содержится множество рекомендаций и указаний, облегчающих создание надежных и простых в использовании шаблонов Azure Resource Manager.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Просмотр и сохранение шаблона Azure Resource Manager виртуальной машины
1. Выполните действия, описанные в статье [Создание первой виртуальной машины в лаборатории в Azure DevTest Labs](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), чтобы начать создание виртуальной машины.
1. Введите необходимые сведения для виртуальной машины и добавьте все необходимые для нее артефакты.
1. Swtich на вкладку **Расширенные настройки.** 
1. В нижней части окна настройки параметров выберите **View ARM template** (Просмотреть шаблон ARM).
1. Скопируйте и сохраните шаблон Resource Manager, чтобы позже использовать его для создания другой виртуальной машины.

   ![Сохранение шаблона Resource Manager для последующего использования](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Прежде чем использовать сохраненный шаблон Resource Manager, необходимо обновить раздел параметров. Вы можете создать файл parameter.json, который настраивает только параметры за пределами фактического шаблона Resource Manager. 

![Настройка параметров с помощью JSON-файла](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Шаблон Resource Manager готов к использованию для [создания виртуальной машины](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Дата истечения срока действия
В таких сценариях, как обучение, демонстрации и испытания, может потребоваться создать виртуальные машины и удалить их автоматически после фиксированной продолжительности, чтобы не понести ненужных затрат. Можно создать лабораторию VM со сроком годности, указав **свойство даты истечения срока действия** для VM. Ознакомьтесь с тем же шаблоном ресурсного менеджера в [нашем репозитории GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration)



### <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения см. в статье [Создание сред со множеством виртуальных машин и ресурсов PaaS с помощью шаблонов Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Развертывание шаблона Resource Manager для создания виртуальной машины](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Исследуйте более быстрые шаблоны менеджера ресурсов для автоматизации DevTest Labs от [публичного репо DevTest Labs GitHub.](https://github.com/Azure/azure-quickstart-templates)
