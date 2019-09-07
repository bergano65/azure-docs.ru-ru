---
title: Определяемые пользователем функции в шаблоне Azure Resource Manager
description: Описывает, как определять и использовать определяемые пользователем функции в шаблоне Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 227ecb57b125264e7d0eba56e634966d677e0f58
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387279"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Определяемые пользователем функции в шаблоне Azure Resource Manager

В шаблоне можно создать свои собственные функции. Эти функции доступны для использования в шаблоне. Определяемые пользователем функции отделены от [стандартных функций шаблонов](resource-group-template-functions.md) , которые автоматически доступны в шаблоне. Создавайте собственные функции при наличии сложных выражений, которые многократно используются в шаблоне.

В этой статье описывается добавление определяемых пользователем функций в шаблон Azure Resource Manager.

## <a name="define-the-function"></a>Определение функции

Для функции требуется значение пространства имен, чтобы избежать конфликтов именования с функциями шаблона. В следующем примере показана функция, которая возвращает имя учетной записи хранения:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Использование функции

В следующем примере показано, как вызвать функцию.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>Ограничения

При определении пользовательской функции есть несколько ограничений:

* Функция не может обращаться к переменным.
* Функция может использовать только параметры, определенные в самой функции. При использовании функции [Parameters](resource-group-template-functions-deployment.md#parameters) в определяемой пользователем функции вы можете использовать только параметры этой функции.
* Функция не может вызывать другие функции, определяемые пользователем.
* Функция не может использовать функцию [Reference](resource-group-template-functions-resource.md#reference) или любую из функций [списка](resource-group-template-functions-resource.md#list) .
* Для параметров этой функции нельзя задавать значения по умолчанию.


## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о доступных свойствах определяемых пользователем функций см. в разделе Общие сведения о [структуре и синтаксисе шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Список доступных функций шаблонов см. в разделе [функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
