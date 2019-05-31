---
title: Настройка коллекции общего образа в Azure DevTest Labs | Документация Майкрософт
description: Сведения о настройке коллекции общего образа в Azure DevTest Labs
services: devtest-lab
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 51b394043f88789865edea5be6376ae536f88848
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420443"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Настройка коллекции общего образа в Azure DevTest Labs
DevTest Labs теперь поддерживает [коллекции образов Shared](/virtual-machines/windows/shared-image-galleries.md) функции. Он позволяет пользователям лаборатории для доступа к образы из общей папке при создании ресурсов лаборатории. Это также поможет вам создавать структуру и организацию вокруг вашего управляемых пользовательских образов виртуальных Машин. Коллекция изображений общего функция поддерживает:

- Управляемые глобальная репликация образов
- Управление версиями и группирование образов для упрощенного управления
- Сделать доступным с помощью учетных записей хранения избыточных зоны (ZRS) изображений в регионах с поддержкой зон доступности. ZRS предлагает повышенной устойчивости к сбоям зоны.
- Совместное использование нескольких подписок и даже между клиентами и с помощью управления доступом на основе ролей (RBAC).

Дополнительные сведения см. в разделе [документации коллекции образов Shared](../virtual-machines/windows/shared-image-galleries.md). 
 
Если у вас есть большое количество управляемых образов, которые нужно сохранить и сделать доступными для всей компании, можно использовать коллекцию общих образов в качестве репозитория, что позволит легко обновлять и передавать образы. В качестве владельца лаборатории можно присоединить существующей коллекцией общего образа в лаборатории. После присоединения коллекции пользователи лаборатории могут создать машин на основе этих последних образов. Ключевым преимуществом этой функции является то, что DevTest Labs могут воспользоваться совместного использования образов между labs, между подписками и регионами. 

## <a name="considerations"></a>Рекомендации
- Одна Галерея общего образа можно подключить только в лабораторию за раз. Если вы хотите подключить другой коллекции, необходимо будет существующий присоединение и отсоединение другой. 
- DevTest Labs в настоящее время не поддерживает отправки образов в коллекции через лаборатории. 
- При создании виртуальной машины, используя образ из коллекции общего образа, DevTest Labs всегда использует последнюю опубликованную версию этого образа.
- Несмотря на то, что DevTest Labs автоматически выполняет максимально точного, чтобы убедиться, что коллекция изображений общего реплицирует образы в регионе, в которой существует лаборатории, это не всегда возможно. Чтобы пользователям не возникли проблемы при создании виртуальных машин на основе этих образов, убедитесь, что образы реплицированных лаборатории регион.»

## <a name="use-azure-portal"></a>Использование портала Azure
1. Войдите на [портале Azure](https://portal.azure.com).
1. Выберите **все службы** в левом меню навигации.
1. Выберите **DevTest Labs** из списка.
1. Из списка лабораторий выберите ваш **лаборатории**.
1. Выберите **конфигурация и политики** в **параметры** раздел, в меню слева.
1. Выберите **галерей изображений общего** под **базы виртуальных машин** в меню слева.
1. Присоединение существующей коллекцией общего образа в лаборатории, щелкнув **Attach** кнопку и выберите в раскрывающемся списке вашей коллекции.
1. Перейдите к вложенные коллекции и настройте коллекции таким образом, чтобы **включить или отключить** общих образов для создания виртуальной Машины.
1. Пользователи лаборатории могут затем создать виртуальную машину, используя поддержкой образов, щелкнув **+ добавить** и поиск изображения в **выберите с основным** страницы.

## <a name="use-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Присоединение коллекции общего образа в лаборатории
При использовании шаблона Azure Resource Manager для присоединения коллекции общего образа в лаборатории, необходимо добавить его в разделе resources шаблона Resource Manager, как показано в следующем примере:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

Полный пример шаблона Resource Manager см. в статье эти примеры шаблонов Resource Manager, в нашем общедоступном репозитории GitHub: [Настройка коллекции общего образа при создании лаборатории](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Создание виртуальной Машины с помощью образа из общей коллекции
При использовании шаблона Azure Resource Manager, чтобы создать виртуальную машину, используя образ из коллекции общего образа, используйте следующий пример:

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

Дополнительные сведения см. в статье эти примеры шаблонов Resource Manager на открытый GitHub.
[Создайте виртуальную машину, используя образ из коллекции общего образа](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>С помощью API

- Используйте API версии 2018 г.-10-15-предварительной версии.
- Для присоединения вашей коллекции, отправьте запрос, как показано в следующем фрагменте:
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Чтобы просмотреть все изображения в галерее общего образа, можно перечислить все общие образы с идентификаторами ресурсов

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Чтобы создать виртуальную Машину с помощью общие образы, можно сделать запрос PUT на виртуальных машинах и в свойствах виртуальной машины, передайте идентификатор общие образы, полученный из предыдущего вызова. Для свойства. SharedImageId


## <a name="next-steps"></a>Дальнейшие действия
На артефакты, ознакомьтесь со следующими статьями:

- [Укажите обязательные артефакты для лаборатории](devtest-lab-mandatory-artifacts.md)
- [Создание настраиваемых артефактов](devtest-lab-artifact-author.md)
- [Добавить репозиторий артефактов в лабораторию](devtest-lab-artifact-author.md)
- [Диагностика сбоев артефактов](devtest-lab-troubleshoot-artifact-failure.md)
