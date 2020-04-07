---
title: Установка пакета удаленного рендеринга для Unity
description: Объясняет, как установить DLL-клиент удаленной визуализации для Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681185"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Установка пакета удаленного рендеринга для Unity

Удаленный отрисование Azure использует пакет Unity для инкапсулирования интеграции в Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Управление пакетами удаленной визуализации в Unity

Пакеты Unity — это контейнеры, которыми можно управлять через [менеджер пакетов](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)Unity.
Этот пакет содержит весь API C'API, а также все связные файлы плагинов, необходимые для использования удаленного рендеринга Azure с Unity.
Следуя схеме именования Unity для пакетов, пакет называется **com.microsoft.azure.remote-rendering.**

Пакет не является частью [репозитория образцов ARR](https://github.com/Azure/azure-remote-rendering)и недоступен во внутреннем реестре пакетов Unity. Чтобы добавить его в проект, необходимо вручную отсутсвие файла `manifest.md` проекта, чтобы добавить следующее:
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
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
После добавления этого пакета можно использовать менеджер пакета Unity, чтобы убедиться, что у вас есть последняя версия.
Более полные инструкции приведены в [учебнике: Настройка проекта Unity с нуля.](../../tutorials/unity/project-setup.md)

## <a name="unity-render-pipelines"></a>Конвейеры визуализации unity

Дистанционное рендерирование работает как с **конвейером Universal,** так и со **стандартным конвейером рендеринга.** По причинам производительности рекомендуется конвейер Универсального рендера.

Для использования **конвейера универсального рендера**его пакет должен быть установлен в Unity. Это можно сделать в **uI-едином** пакетном менеджере Unity (название пакета **Universal RP,** версия 7.2.1 или более новый), либо через `Packages/manifest.json` файл, как описано в [учебнике по настройке проекта Unity.](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)

## <a name="next-steps"></a>Дальнейшие действия

* [Объекты и компоненты Unity](objects-components.md)
* [Учебник: Настройка проекта Unity с нуля](../../tutorials/unity/project-setup.md)
