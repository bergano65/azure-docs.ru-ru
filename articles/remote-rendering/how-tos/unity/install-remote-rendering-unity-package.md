---
title: Установка пакета Удаленной отрисовки для Unity
description: Объясняет, как установить клиентские библиотеки DLL удаленной подготовки к просмотру для Unity.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564313"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Установка пакета Удаленной отрисовки для Unity

Удаленная визуализация Azure использует пакет Unity для инкапсуляции интеграции в Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Управление пакетами удаленной подготовки к просмотру в Unity

Пакеты Unity — это контейнеры, которыми можно управлять через [Диспетчер пакетов](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)Unity.
Этот пакет содержит весь API C#, а также все двоичные файлы подключаемого модуля, необходимые для использования удаленной отрисовки Azure с Unity.
В следующей схеме именования Unity для пакетов пакет называется **com. Microsoft. Azure. Remote-Rendering**.

Пакет не является частью [репозитория примеров arr](https://github.com/Azure/azure-remote-rendering)и недоступен из внутреннего реестра пакетов Unity. Чтобы добавить его в проект, необходимо вручную изменить файл проекта, `manifest.md` добавив в него следующее:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

После добавления этого можно использовать диспетчер пакетов Unity, чтобы убедиться в наличии последней версии.
Более полные инструкции приведены в руководстве по [просмотру удаленных моделей](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="unity-render-pipelines"></a>Конвейеры отрисовки Unity

Удаленная визуализация работает как с, так **:::no-loc text="Universal render pipeline":::** и с **:::no-loc text="Standard render pipeline":::** . По соображениям производительности рекомендуется использовать универсальный конвейер отрисовки.

Чтобы использовать **:::no-loc text="Universal render pipeline":::** , его пакет должен быть установлен в Unity. Это можно сделать в пользовательском интерфейсе **диспетчера пакетов** ( **Universal RP**, версии 7.3.1 или более новой) или с помощью `Packages/manifest.json` файла, как описано в [руководстве по установке проекта Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Дальнейшие действия

* [Игровые объекты и компоненты Unity](objects-components.md)
* [Учебник. Просмотр удаленных моделей](../../tutorials/unity/view-remote-models/view-remote-models.md)
