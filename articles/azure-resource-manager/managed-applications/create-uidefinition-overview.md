---
title: CreateUiDefinition.json файл для панели портала
description: Описывает, как создавать определения пользовательского интерфейса для портала Azure. Используется при определении управляемых приложений Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294889"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Использование файла CreateUiDefinition.json для создания управляемого приложения Azure

В этом документе представлены основные понятия **файла createUiDefinition.json,** который портал Azure использует для определения пользовательского интерфейса при создании управляемого приложения.

Шаблон следующим образом

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

CreateUiDefinition всегда содержит три свойства: 

* handler;
* version
* параметры

Обработчик должен `Microsoft.Azure.CreateUIDef`быть всегда, и `0.1.2-preview`последняя поддерживаемая версия .

Схема свойства parameters зависит от сочетания определенных значений handler и version. Для управляемых приложений поддерживаются свойства `basics`, `steps` и `outputs`. Свойства basics и steps содержат [элементы](create-uidefinition-elements.md), например текстовые поля и раскрывающиеся списки, которые отображаются на портале Azure. Свойство output используется для сопоставления значений определенных элементов с параметрами шаблона развертывания Azure Resource Manager.

Советуем также использовать параметр `$schema`, но это необязательно. При его использовании значение параметра `version` должно совпадать с версией в URI `$schema`.

Вы можете использовать редактор JSON для создания createUiDefinition, а затем протестировать его в [песочнице createUiDefinition,](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) чтобы просмотреть его. Для получения дополнительной информации о песочнице смотрите [интерфейс портала для управляемых приложений Azure.](test-createuidefinition.md)

## <a name="basics"></a>Основы

Основы — это первый шаг, сгенерированный при разборе файла портала Azure. Помимо отображения элементов, указанных в `basics`, портал вводит элементы, чтобы пользователи выбрали подписку, группу ресурсов и местоположение для развертывания. По возможности элементы, которые задавили параметры развертывания, такие как имя кластера или учетные данные администратора, должны идти на этом этапе.

## <a name="steps"></a>Шаги

Свойство steps может содержать как ноль, так и несколько дополнительных шагов для отображения после basics, каждый из которых содержит один или несколько элементов. Вы можете добавить действия для каждой роли или уровня развертываемого приложения. Например, добавьте шаг для входов в мастер-узла и шаг для узлов рабочего в кластере.

## <a name="outputs"></a>Выходные данные

Портал Azure использует свойство `outputs` для сопоставления элементов `basics` и `steps` с параметрами шаблона развертывания Azure Resource Manager. Ключами являются имена параметров шаблона, а значениями — свойства выходных объектов из элементов, на которые даются ссылки.

Чтобы задать имя ресурса управляемого приложения, необходимо включить значение `applicationResourceName` в свойство outputs. Если вы не установите это значение, приложение назначает GUID для имени. В пользовательский интерфейс, запрашивающий имя пользователя, можно включить текстовое поле.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Типы ресурсов

Чтобы отфильтровать доступные местоположения только в тех местах, которые поддерживают типы ресурсов для развертывания, предоставьте массив типов ресурсов. При предоставлении нескольких типов ресурсов возвращаются только те места, которые поддерживают все типы ресурсов. Это необязательное свойство.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Функции

CreateUiDefinition предоставляет [функции](create-uidefinition-functions.md) для работы с входными и выходными элементами, а также такими функциями, как условные. Эти функции схожи как по синтаксису, так и по функциональности с функциями шаблона Azure Resource Manager.

## <a name="next-steps"></a>Дальнейшие действия

Файл createUiDefinition.json имеет простую схему. Его реальные возможности основаны на поддерживаемых элементах и функциях. Эти элементы более подробно описаны здесь:

- [Элементы](create-uidefinition-elements.md)
- [Функции](create-uidefinition-functions.md)

Текущая схема JSON для createUiDefinition доступна здесь: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

Пример файла пользовательского интерфейса: [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
