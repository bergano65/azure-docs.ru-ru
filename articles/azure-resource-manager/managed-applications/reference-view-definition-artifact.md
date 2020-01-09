---
title: Просмотреть ссылку артефакта определения
description: Содержит пример артефакта определения представления для управляемых приложений Azure. Имя файла — viewDefinition. JSON.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651206"
---
# <a name="reference-view-definition-artifact"></a>Ссылка: Просмотр артефакта определения

Эта статья представляет собой справочник по артефакту *viewDefinition. JSON* в управляемых приложениях Azure. Дополнительные сведения о конфигурации создания представлений см. в разделе [View definition артефакт](concepts-view-definition.md).

## <a name="view-definition"></a>Просмотр определений

В следующем JSON показан пример файла *viewDefinition. JSON* для управляемых приложений Azure:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Учебник. Создание управляемого приложения с настраиваемыми действиями и ресурсами](tutorial-create-managed-app-with-custom-provider.md)
- [Ссылка: артефакт элементов пользовательского интерфейса](reference-createuidefinition-artifact.md)
- [Ссылка: артефакт шаблона развертывания](reference-main-template-artifact.md)