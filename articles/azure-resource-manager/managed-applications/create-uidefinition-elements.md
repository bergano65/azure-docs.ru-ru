---
title: Создание элементов определения uI
description: Сведения об элементах, используемых при создании определений пользовательского интерфейса для портала Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086704"
---
# <a name="createuidefinition-elements"></a>Элементы CreateUiDefinition

В этой статье описывается схема и свойства всех поддерживаемых элементов CreateUiDefinition. 

## <a name="schema"></a>схема

Для большинства элементов схема выглядит следующим образом:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Свойство | Обязательно | Описание |
| -------- | -------- | ----------- |
| name | Да | Внутренний идентификатор для ссылки на конкретный экземпляр элемента. Чаще всего имя элемента используется в `outputs`, где значения выходных данных указанных элементов сопоставляются с параметрами шаблона. Его также можно использовать для привязки выходного значения элемента к `defaultValue` другого элемента. |
| type | Да | Элемент управления пользовательского интерфейса для обработки элемента. Список поддерживаемых типов см. в разделе [Элементы](#elements). |
| label | Да | Отображаемый текст элемента. Некоторые типы элементов содержат несколько меток, поэтому значение может быть объектом, содержащим несколько строк. |
| defaultValue | нет | Значение элемента по умолчанию. Некоторые типы элементов поддерживают сложные значения по умолчанию, поэтому значение может быть объектом. |
| toolTip | нет | Текст для отображения в подсказке элемента. Аналогично `label` некоторые элементы поддерживают несколько строк с подсказками. С помощью синтаксиса Markdown можно внедрить встроенные ссылки.
| constraints | нет | Одно или несколько свойств, используемых для настройки поведения проверки элемента. Поддерживаемые свойства для constraints зависят от типа элемента. Некоторые типы элементов не поддерживают настройку поведения проверки и поэтому не содержат свойство constraints. |
| параметры | нет | Дополнительные свойства, позволяющие настроить поведение элемента. Аналогично `constraints` поддерживаемые свойства зависят от типа элемента. |
| visible | нет | Указывает, отображается ли элемент. Если задано значение `true`, элемент и применимые дочерние элементы будут отображены. Значение по умолчанию — `true`. Используйте [логические функции](create-uidefinition-functions.md#logical-functions), чтобы динамически управлять значением свойства.

## <a name="elements"></a>Элементы

Документация для каждого элемента содержит примеры пользовательского интерфейса, схемы, примечания о поведении элемента (обычно в отношении проверки и поддерживаемой настройки) и пример выходных данных.

- [Элемент пользовательского интерфейса Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Элемент пользовательского интерфейса Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Элемент пользовательского интерфейса Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Элемент пользовательского интерфейса Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Элемент пользовательского интерфейса Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Элемент пользовательского интерфейса Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Элемент пользовательского интерфейса Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Элемент пользовательского интерфейса Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Элемент пользовательского интерфейса Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.ManagedIdentity.IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Элемент пользовательского интерфейса Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Элемент пользовательского интерфейса Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Элемент пользовательского интерфейса Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Элемент пользовательского интерфейса Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Дальнейшие действия

Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
