---
title: Функции, определяемые пользователем в шаблонах
description: Описывает, как определить и использовать функции, определяемые пользователями, в шаблоне Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943216"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Функции, определяемые пользователем в шаблоне менеджера ресурсов Azure

В шаблоне можно создать свои собственные функции. Эти функции доступны для использования в шаблоне. Функции, определяемые пользователем, отделены от [стандартных функций шаблона,](template-functions.md) которые автоматически доступны в шаблоне. Создавайте свои собственные функции, когда у вас есть сложные выражения, которые используются повторно в шаблоне.

В этой статье описывается, как добавить функции, определяемые пользователем, в шаблон Azure Resource Manager.

## <a name="define-the-function"></a>Определить функцию

Для функции требуется значение пространства имен, чтобы избежать конфликтов именования с функциями шаблона. В следующем примере показана функция, которая возвращает уникальное имя:

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

В следующем примере показан шаблон, включающий функцию, определенную пользователем. Он использует эту функцию, чтобы получить уникальное имя для учетной записи хранилища. Шаблон имеет параметр, названный **storageNamePrefix,** который он передает в качестве параметра функции.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
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
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Ограничения

При определении пользовательской функции есть несколько ограничений:

* Функция не может обращаться к переменным.
* Функция может использовать только параметры, определенные в самой функции. При использовании функции [параметров](template-functions-deployment.md#parameters) в функции, определяемой пользователем, вы ограничены параметрами для этой функции.
* Функция не может вызывать другие функции, определяемые пользователем.
* Функция не может использовать [функцию ссылки](template-functions-resource.md#reference) или любую из функций [списка.](template-functions-resource.md#list)
* Для параметров этой функции нельзя задавать значения по умолчанию.


## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать о доступных свойствах для функций, определяемых пользователем, [см.](template-syntax.md)
* Для списка доступных функций [Azure Resource Manager template functions](template-functions.md)шаблонов см.
