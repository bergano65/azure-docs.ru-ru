---
title: Элемент пользовательского интерфейса СервицепринЦипалселектор
description: Описывает элемент пользовательского интерфейса Microsoft. Common. СервицепринЦипалселектор для портал Azure. Предоставляет раскрывающийся список для выбора идентификатора приложения и текстового поля для ввода пароля или отпечатка сертификата.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576002"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Элемент пользовательского интерфейса Microsoft. Common. СервицепринЦипалселектор

Элемент управления, позволяющий пользователям выбрать существующий субъект-службу или зарегистрировать новый. При выборе **создать новый**необходимо выполнить действия по регистрации нового приложения. При выборе существующего приложения элемент управления предоставляет текстовое поле для ввода пароля или отпечатка сертификата.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

Представление по умолчанию определяется значениями в `defaultValue` свойстве. Если `principalId` свойство содержит допустимый глобальный уникальный идентификатор (GUID), элемент управления ищет идентификатор объекта приложения. Значение по умолчанию применяется, если пользователь не делает выбор из раскрывающегося списка.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Начальное представление Microsoft. Common. СервицепринЦипалселектор":::

При выборе **создать новый** или существующий идентификатор приложения из раскрывающегося списка **тип проверки подлинности** отображается для ввода пароля или отпечатка сертификата в текстовом поле.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Начальное представление Microsoft. Common. СервицепринЦипалселектор":::

## <a name="schema"></a>схема

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarks

- Обязательные свойства:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Указывает значения по умолчанию `principalId` и `name` .

- Необязательные свойства:
  - `toolTip`: Присоединяет подсказку `infoBalloon` к каждой метке.
  - `visible`: Скрытие или отображение элемента управления.
  - `options`: Указывает, следует ли сделать параметр отпечатка сертификата доступным.
  - `constraints`: Регулярные ограничения для проверки пароля.

## <a name="example"></a>Пример

Ниже приведен пример `Microsoft.Common.ServicePrincipalSelector` элемента управления. `defaultValue`Свойство задает `principalId` в `<default guid>` качестве заполнителя для идентификатора приложения по умолчанию.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Пример выходных данных

`appId`— Это идентификатор регистрации приложения, который вы выбрали или создали. `objectId`Представляет собой массив ObjectID для субъектов-служб, настроенных для регистрации выбранного приложения.

Если выборка не сделана из раскрывающегося списка, то `newOrExisting` значение свойства — **New**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

При выборе параметра **создать новый** или существующий идентификатор приложения из раскрывающегося списка `newOrExisting` значение свойства является **существующим**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Дальнейшие шаги

- Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
- Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
