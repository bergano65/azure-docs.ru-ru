---
title: CreateUiDefinition.jsна панели "файл" для портала
description: Описывает создание определений пользовательского интерфейса для портал Azure. Используется при определении управляемых приложений Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80294889"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Использование файла CreateUiDefinition.json для создания управляемого приложения Azure

В этом документе представлены основные понятия **createUiDefinition.js** файла, который портал Azure использует для определения пользовательского интерфейса при создании управляемого приложения.

Шаблон выглядит следующим образом:

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

Обработчик всегда должен иметь значение `Microsoft.Azure.CreateUIDef` , а последняя поддерживаемая версия — `0.1.2-preview` .

Схема свойства parameters зависит от сочетания определенных значений handler и version. Для управляемых приложений поддерживаются свойства `basics`, `steps` и `outputs`. Свойства basics и steps содержат [элементы](create-uidefinition-elements.md), например текстовые поля и раскрывающиеся списки, которые отображаются на портале Azure. Свойство output используется для сопоставления значений определенных элементов с параметрами шаблона развертывания Azure Resource Manager.

Советуем также использовать параметр `$schema`, но это необязательно. При его использовании значение параметра `version` должно совпадать с версией в URI `$schema`.

С помощью редактора JSON можно создать createUiDefinition, а затем протестировать его в [песочнице createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) для предварительного просмотра. Дополнительные сведения о песочнице см. в статье [тестирование интерфейса портала для управляемых приложений Azure](test-createuidefinition.md).

## <a name="basics"></a>Основы

Основные принципы — это первый шаг, созданный при портал Azure синтаксического анализа файла. Помимо отображения элементов, указанных в `basics`, портал вводит элементы, чтобы пользователи выбрали подписку, группу ресурсов и местоположение для развертывания. По возможности элементы, которые запрашивают параметры уровня развертывания, такие как имя кластера или учетные данные администратора, должны быть в этом шаге.

## <a name="steps"></a>Шаги

Свойство steps может содержать как ноль, так и несколько дополнительных шагов для отображения после basics, каждый из которых содержит один или несколько элементов. Вы можете добавить действия для каждой роли или уровня развертываемого приложения. Например, добавьте шаг для входных данных главного узла и шаг для рабочих узлов в кластере.

## <a name="outputs"></a>Выходные данные

Портал Azure использует свойство `outputs` для сопоставления элементов `basics` и `steps` с параметрами шаблона развертывания Azure Resource Manager. Ключами являются имена параметров шаблона, а значениями — свойства выходных объектов из элементов, на которые даются ссылки.

Чтобы задать имя ресурса управляемого приложения, необходимо включить значение `applicationResourceName` в свойство outputs. Если это значение не задано, приложение назначает идентификатор GUID для имени. В пользовательский интерфейс, запрашивающий имя пользователя, можно включить текстовое поле.

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

Чтобы отфильтровать доступные расположения только для тех мест, которые поддерживают типы ресурсов для развертывания, укажите массив типов ресурсов. При предоставлении более одного типа ресурсов возвращаются только те расположения, которые поддерживают все типы ресурсов. Это необязательное свойство.

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

CreateUiDefinition предоставляет [функции](create-uidefinition-functions.md) для работы с входными и выходными элементами элементов, а также такими функциями, как условия. Эти функции похожи как в синтаксисе, так и в функциональности для Azure Resource Manager функций шаблонов.

## <a name="next-steps"></a>Дальнейшие действия

Файл createUiDefinition.json имеет простую схему. Его реальные возможности основаны на поддерживаемых элементах и функциях. Эти элементы более подробно описаны здесь:

- [Elements (XElement Dynamic Property)](create-uidefinition-elements.md) (Elements (Динамическое свойство XElement))
- [Функции](create-uidefinition-functions.md)

Текущая схема JSON для createUiDefinition доступна здесь: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

Пример файла пользовательского интерфейса: [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
