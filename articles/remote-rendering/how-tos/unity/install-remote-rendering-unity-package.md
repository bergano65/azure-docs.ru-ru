---
title: Установка пакета Удаленной отрисовки для Unity
description: Объясняет, как установить клиентские библиотеки DLL удаленной подготовки к просмотру для Unity.
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681185"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Установка пакета Удаленной отрисовки для Unity

Удаленная визуализация Azure использует пакет Unity для инкапсуляции интеграции в Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Управление пакетами удаленной подготовки к просмотру в Unity

Пакеты Unity — это контейнеры, которыми можно управлять через [Диспетчер пакетов](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)Unity.
Этот пакет содержит весь API C#, а также все двоичные файлы подключаемого модуля, необходимые для использования удаленной отрисовки Azure с Unity.
В следующей схеме именования Unity для пакетов пакет называется **com. Microsoft. Azure. Remote-Rendering**.

Пакет не является частью [репозитория примеров arr](https://github.com/Azure/azure-remote-rendering)и недоступен из внутреннего реестра пакетов Unity. Чтобы добавить его в проект, необходимо вручную изменить `manifest.md` файл проекта, добавив в него следующее:
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
После добавления этого можно использовать диспетчер пакетов Unity, чтобы убедиться в наличии последней версии.
Более полные инструкции приведены в руководстве по [настройке проекта Unity с нуля](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Конвейеры отрисовки Unity

Удаленная визуализация работает как с **универсальным конвейером отрисовки** , так и с **стандартным конвейером отрисовки**. По соображениям производительности рекомендуется использовать универсальный конвейер отрисовки.

Для использования **универсального конвейера отрисовки**его пакет должен быть установлен в Unity. Это можно сделать в пользовательском интерфейсе **диспетчера пакетов** ( **Universal RP**, версии 7.2.1 или более новой) или с помощью `Packages/manifest.json` файла, как описано в руководстве по [установке проекта Unity](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Дальнейшие шаги

* [Игровые объекты и компоненты Unity](objects-components.md)
* [Руководство. Настройка проекта Unity с нуля](../../tutorials/unity/project-setup.md)
