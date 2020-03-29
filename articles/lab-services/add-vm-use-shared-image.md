---
title: Добавить VM с помощью общего изображения в Лабораториях Azure DevTest (ru) Документы Майкрософт
description: Узнайте, как добавить виртуальную машину (VM) с помощью изображения из прикрепленной общей галереи изображений в Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775595"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Добавить VM с помощью изображения из прикрепленной общей галереи изображений
Azure DevTest Labs позволяет прикрепить общую галерею изображений к вашей лаборатории, а затем использовать изображения в галерее в качестве основы для m-технологий, которые вы создаете в лаборатории. Чтобы узнать, как прикрепить общую галерею изображений к вашей лаборатории, [см.](configure-shared-image-gallery.md) В этой статье показано, как добавить VM в лабораторию, используя изображение из прикрепленной общей галереи изображений в качестве основы. 

## <a name="azure-portal"></a>Портал Azure
В этом разделе вы узнаете, как использовать портал Azure для добавления VM в лабораторию на основе изображения из прилагаемых общих галерей изображений. В этом разделе не содержатся подробные пошаговые инструкции по созданию VM с помощью портала Azure. Для получения этих [деталей, смотрите Создать VM - Azure портал](devtest-lab-add-vm.md). Он только выделяет шаги, где вы выбираете изображение из прилагаемых общих галереи изображений и выбрать версию изображения, которые вы хотите использовать. 

При добавлении VM в лабораторию можно выбрать изображение из прикрепленной общей галереи изображений в качестве базового изображения: 

![Выберите общее изображение для базы](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Затем на **вкладке Расширенные настройки** страницы **ресурса Create** Lab можно выбрать версию изображения, который вы хотите использовать в качестве базового изображения:

![Выберите версию изображения](./media/add-vm-use-shared-image/select-version-shared-image.png)

После создания VM можно переключиться на другую версию изображения. 

## <a name="resource-manager-template"></a>Шаблон Resource Manager
Если вы используете шаблон Azure Resource Manager для создания виртуальной машины с помощью общего изображения галереи изображений, укажите значение для **sharedImageId** в разделе **Свойства.** См. следующий пример. 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Для полного примера шаблона управления ресурсами смотрите [Создать виртуальную машину, используя общий](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) образ изображения галереи в нашем репозитории GitHub. 

## <a name="rest-api"></a>REST API

1. Во-первых, необходимо получить идентификатор изображения в общей галерее изображений. Один из способов состоит в том, чтобы перечислить все изображения в прилагаемых общих галереи изображений с помощью следующей команды GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Вызовите метод PUT на виртуальных машинах, передавая идентификатор `properties.SharedImageId`общего изображения, полученного от предыдущего вызова в .

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать, как прикрепить общую галерею изображений к лаборатории и настроить ее, [см.](configure-shared-image-gallery.md)