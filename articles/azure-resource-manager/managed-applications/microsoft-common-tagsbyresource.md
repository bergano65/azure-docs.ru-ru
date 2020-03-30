---
title: Элемент uI TagsByResource
description: Описывает элемент uI Microsoft.Common.TagsByResource для портала Azure. Используйте для применения тегов к ресурсу во время развертывания.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652207"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Элемент uI Microsoft.Common.TagsByResource

Контроль для связывания [тегов](../management/tag-resources.md) с ресурсами в развертывании.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>схема

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

## <a name="remarks"></a>Remarks

- Необходимо указать по `resources` крайней мере один элемент в массиве.
- Каждый `resources` элемент должен быть полностью квалифицированным типом ресурсов. Эти элементы отображаются в выпадении **ресурсов** и помечаются пользователем.
- Выход элемента управления отформатирован для простого назначения значений тегов в шаблоне управления ресурсами Azure. Чтобы получить выход элемента управления в шаблоне, включите параметр в шаблоне, как показано в следующем примере:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Для каждого ресурса, который может быть помечен, присвоите свойство тегов значению параметра для этого типа ресурса:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Используйте функцию [if](../templates/template-functions-logical.md#if) при доступе к параметру tagsByResource. Это позволяет назначить пустой объект, когда не присваиваются теги данному типу ресурса.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
- Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
