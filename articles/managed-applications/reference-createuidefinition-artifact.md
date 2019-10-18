---
title: Артефакт createUiDefinition управляемого приложения Azure
description: Здесь показано, как создать артефакт createUiDefinition для управляемого приложения Azure. Файл называется createUiDefinition. JSON.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 3e952476373d1692494d06f22dfeb202cab6d6d7
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528971"
---
# <a name="reference-user-interface-elements-artifact"></a>Ссылка: артефакт элементов пользовательского интерфейса

Эта статья представляет собой справочник по артефакту *createUiDefinition. JSON* в управляемых приложениях Azure. Дополнительные сведения о создании элементов пользовательского интерфейса см. в разделе [Создание элементов пользовательского интерфейса](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Элементы пользовательского интерфейса

В следующем JSON показан пример файла *createUiDefinition. JSON* для управляемых приложений Azure:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник. Создание управляемого приложения с настраиваемыми действиями и ресурсами](tutorial-create-managed-app-with-custom-provider.md)
- [Ссылка: артефакт шаблона развертывания](reference-main-template-artifact.md)
- [Ссылка: Просмотр артефакта определения](reference-view-definition-artifact.md)