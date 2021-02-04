---
title: Установка пространственных привязок Azure для Unity
description: Настройка проекта Unity для использования пространственных привязок Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550629"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Настройка пространственных привязок Azure в проекте Unity

В этом руководство показано, как приступить к работе с пакетом SDK для пространственных привязок Azure в проекте Unity.

## <a name="requirements"></a>Требования

Пространственные привязки Azure в настоящее время поддерживают Unity 2019,4 (LTS) со следующими конфигурациями.

* Unity 2019,4 с AR Foundation 3,1 поддерживается в пространственных привязках Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Настройка проекта

### <a name="download-packages"></a>Скачивание пакетов
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Импорт пакетов
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Только для Android: Настройка файла mainTemplate. gradle

1. Выберите **Изменить** > **Параметры проекта** > **Проигрыватель**.
2. На **панели инспектора** для **параметров проигрывателя** выберите значок **Android** .
3. В разделе **Сборка** установите флажок **настраиваемый основной шаблон Gradle** , чтобы создать пользовательский шаблон Gradle по адресу `Assets\Plugins\Android\mainTemplate.gradle` .
4. Откройте файл `mainTemplate.gradle` в текстовом редакторе.
5. В `dependencies` разделе вставьте следующие зависимости:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Когда все будет готово, `dependencies` раздел должен выглядеть примерно так:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Как создавать и размещать привязки в Unity](./create-locate-anchors-unity.md)
