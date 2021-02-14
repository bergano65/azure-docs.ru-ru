---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569631"
---
Чтобы скачать требуемые пакеты, необходимо установить <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a>.

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