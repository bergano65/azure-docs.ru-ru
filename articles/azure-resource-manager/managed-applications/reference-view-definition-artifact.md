---
title: Посмотреть ссылку артефакта определения
description: Предоставляет пример артефакта определения представления для управляемых приложений Azure. Имя файла — viewDefinition.json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651206"
---
# <a name="reference-view-definition-artifact"></a>Ссылка: Артефакт определения представления

Эта статья является ссылкой на артефакт *viewDefinition.json* в управляемых приложениях Azure. Для получения дополнительной информации об [View definition artifact](concepts-view-definition.md)настройке конфигурации представлений см.

## <a name="view-definition"></a>Просмотр определений

Ниже приводится пример файла *viewDefinition.json* для управляемых приложений Azure:

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

- [Учебник: Создание управляемого приложения с помощью пользовательских действий и ресурсов](tutorial-create-managed-app-with-custom-provider.md)
- [Справка: Артефакт элементов пользовательского интерфейса](reference-createuidefinition-artifact.md)
- [Справка: артефакт шаблона развертывания](reference-main-template-artifact.md)