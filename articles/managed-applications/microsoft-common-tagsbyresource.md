---
title: Элемент пользовательского интерфейса Тагсбиресаурце для Azure
description: Описывает элемент пользовательского интерфейса Microsoft. Common. Тагсбиресаурце для портал Azure. Используйте для применения тегов к ресурсу во время развертывания.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 5711759666cd68dc93e5bfb67e51ec1cc0a48bd1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933146"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Элемент пользовательского интерфейса Microsoft. Common. Тагсбиресаурце

Элемент управления для связывания [тегов](../azure-resource-manager/resource-group-using-tags.md) с ресурсами в развертывании.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>SCHEMA (Схема)

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Примечания

- Необходимо указать хотя бы один элемент в массиве `resources`.
- Каждый элемент в `resources` должен быть полным типом ресурса. Эти элементы отображаются в раскрывающемся списке **ресурсов** и таггабле пользователем.
- Выходные данные элемента управления форматируются для простого присваивания значений тегов в шаблоне Azure Resource Manager. Чтобы получить выходные данные элемента управления в шаблоне, включите параметр в шаблон, как показано в следующем примере:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Для каждого ресурса, который может быть помечен, присвойте свойству Tags значение параметра для этого типа ресурса:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Используйте функцию [If](../azure-resource-manager/resource-group-template-functions-logical.md#if) при доступе к параметру тагсбиресаурце. Он позволяет назначить пустой объект, если ни один из тегов не назначен данному типу ресурса.

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
- Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
