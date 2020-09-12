---
title: Установка пространственных привязок Azure для Unity
description: Настройка проекта Unity для использования пространственных привязок Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 08/17/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57ead9636b7218ecfc7d72bb605b469d6a7d1ac6
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536367"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Настройка пространственных привязок Azure в проекте Unity

В этом руководство показано, как приступить к работе с пакетом SDK для пространственных привязок Azure в проекте Unity.

## <a name="requirements"></a>Требования

Пространственные привязки Azure в настоящее время поддерживают Unity 2019,4 (LTS) со следующими конфигурациями.

* Unity 2019,4 с AR Foundation 3,1 поддерживается в пространственных привязках Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Настройка проекта

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Добавление пакетов диспетчера пакетов Unity в проект](#tab/UPMPackage)

Пространственные привязки Azure для Unity в настоящее время распределяются с помощью пакетов УПМ пакета Unity. Эти пакеты можно найти в нашем [реестре NPM](https://bintray.com/microsoft/AzureMixedReality-NPM). Дополнительные сведения о работе с реестрами пакетов с [заданной](https://docs.unity3d.com/Manual/upm-scoped.html)областью в проекте Unity см. в официальной документации по Unity здесь.

#### <a name="add-the-registry-to-your-unity-project"></a>Добавление реестра в проект Unity

1. В проводнике перейдите к папке проекта Unity `Packages` . Откройте файл манифеста проекта `manifest.json` в текстовом редакторе.
2. В верхней части файла на том же уровне, что и `dependencies` раздел, добавьте следующую запись, чтобы включить в проект реестр пространственных привязок Azure. `scopedRegistries`Запись указывает Unity, где искать пакеты SDK для пространственных привязок Azure.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Добавление пакетов SDK в проект Unity

| Платформа | Имя пакета                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. пространственные-Anchors-SDK. Android |
| iOS      | com. Microsoft. Azure. спатиал-анчорс-СДК. iOS     |
| HoloLens | com. Microsoft. Azure. пространственные-Anchors-SDK. Windows |

1. Для каждой платформы (Android/iOS/HoloLens), которую вы хотели бы поддерживать в проекте, добавьте запись с именем пакета и версией пакета в `dependencies` раздел в манифесте проекта. Пример см. ниже.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Сохраните и закройте файл `manifest.json`. При возврате в Unity Unity должен автоматически обнаруживать изменения манифеста проекта и получать указанные пакеты. Вы можете развернуть `Packages` папку в представлении проекта, чтобы убедиться в том, что нужные пакеты импортированы.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Только для Android: Настройка файла mainTemplate. gradle

1. Выберите **Изменить** > **Параметры проекта** > **Проигрыватель**.
2. На **панели инспектора** для **параметров проигрывателя**выберите значок **Android** .
3. В разделе **Сборка** установите флажок **настраиваемый основной шаблон Gradle** , чтобы создать пользовательский шаблон Gradle по адресу `Assets\Plugins\Android\mainTemplate.gradle` .
4. Откройте файл `mainTemplate.gradle` в текстовом редакторе. 
5. В `dependencies` разделе вставьте следующие зависимости:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Когда все будет готово, `dependencies` раздел должен выглядеть примерно так:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Импорт пакета ресурсов](#tab/UnityAssetPackage)

> [!WARNING]
> Распределение пакета ресурсов Unity в пакете SDK пространственных привязок Azure будет признано устаревшим после пакета SDK версии 2.5.0.

1. Скачайте `AzureSpatialAnchors.unitypackage` файл для версии, которую вы хотите ориентировать, из [выпусков GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). 
2. Следуйте приведенным [здесь](https://docs.unity3d.com/Manual/AssetPackagesImport.html) инструкциям, чтобы импортировать пакет ресурсов Unity в проект.    

---

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Как создавать и размещать привязки в Unity](./create-locate-anchors-unity.md)
