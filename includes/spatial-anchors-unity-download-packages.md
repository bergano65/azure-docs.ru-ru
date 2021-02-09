---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: c97067c66296e8980a36b21298a7b2061e0f9b4c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550410"
---
Выполните следующую команду, заменив `<version_number>` версией Пространственных привязок Azure, которую необходимо скачать в текущую папку:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Чтобы получить список доступных версий пакета Пространственных привязок Azure, выполните следующую команду:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

Основной пакет Пространственных привязок Azure будет скачан в папку, из которой была выполнена команда.

Повторите этот шаг, чтобы скачать пакет для каждой платформы (Android/iOS/HoloLens), поддержку которой вы хотите обеспечить в своем проекте.

| Платформа | Имя пакета                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<номер_версии> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<номер_версии>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<номер_версии> |