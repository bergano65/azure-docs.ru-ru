---
title: Общие сведения о создании определения пользовательского интерфейса для управляемых приложений Azure | Документация Майкрософт
description: Сведения о создании определений пользовательского интерфейса для управляемых приложений Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 3d0a6d97440404904c041369a4631fdd3fb618b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257564"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Создание пользовательского интерфейса портала Azure для управляемого приложения
В этом документе описывается файл createUiDefinition.json. На основе этого файла портал Azure создает пользовательский интерфейс для создания управляемого приложения.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition всегда содержит три свойства: 

* handler;
* версия
* parameters

Для управляемых приложений handler должен всегда иметь значение `Microsoft.Compute.MultiVm`, а последняя поддерживаемая версия — `0.1.2-preview`.

Схема свойства parameters зависит от сочетания определенных значений handler и version. Для управляемых приложений поддерживаются свойства `basics`, `steps` и `outputs`. Свойства basics и steps содержат _элементы_, например текстовые поля и раскрывающиеся списки, которые отображаются на портале Azure. Свойство output используется для сопоставления значений определенных элементов с параметрами шаблона развертывания Azure Resource Manager.

Советуем также использовать параметр `$schema`, но это необязательно. При его использовании значение параметра `version` должно совпадать с версией в URI `$schema`.

Можно использовать редактор JSON для создания своего определения пользовательского интерфейса, или "песочницы" Определение пользовательского интерфейса можно использовать для создания и предварительного просмотра определения пользовательского интерфейса. Дополнительные сведения о «песочнице», см. в разделе [тестирования портала интерфейса для управляемых приложений Azure](test-createuidefinition.md).

## <a name="basics"></a>Основы
Шаг с использованием свойства basics всегда является первым шагом мастера, создаваемого во время анализа файла порталом Azure. Помимо отображения элементов, указанных в `basics`, портал вводит элементы, чтобы пользователи выбрали подписку, группу ресурсов и местоположение для развертывания. Как правило, на этом шаге должны выполняться элементы, запрашивающие параметры для всего развертывания, такие как имя кластера или учетные данные администратора.

Если поведение элемента зависит от подписки пользователя, группы ресурсов или местоположения, тогда он не может использоваться на шаге basics. Например, **Microsoft.Compute.SizeSelector** определяет список доступных размеров в зависимости от подписки пользователя и местоположения. Таким образом, **Microsoft.Compute.SizeSelector** может использоваться только в steps. Как правило, только элементы в пространстве имен **Microsoft.Common** могут использоваться в basics. Хотя некоторые элементы в других пространствах имен (например, **Microsoft.Compute.Credentials**), которые не зависят от контекста пользователя, по-прежнему разрешены.

## <a name="steps"></a>Действия
Свойство steps может содержать как ноль, так и несколько дополнительных шагов для отображения после basics, каждый из которых содержит один или несколько элементов. Вы можете добавить действия для каждой роли или уровня развертываемого приложения. Например, действие для входных данных главных узлов и действие для рабочих узлов в кластере.

## <a name="outputs"></a>outputs
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

## <a name="functions"></a>Функции Azure
Аналогично функциям шаблонов в Azure Resource Manager (как в синтаксисе и функции), CreateUiDefinition предоставляет функции для работы с входные и выходные данные и функции, такие как условные выражения элементов.

## <a name="next-steps"></a>Дальнейшие действия
Файл createUiDefinition.json имеет простую схему. Его реальные возможности основаны на поддерживаемых элементах и функциях. Эти элементы более подробно описаны здесь:

- [CreateUiDefinition elements](create-uidefinition-elements.md) (Элементы CreateUiDefinition)
- [Функции](create-uidefinition-functions.md)

Текущая схема JSON для createUiDefinition доступна здесь: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Пример файла пользовательского интерфейса: [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
