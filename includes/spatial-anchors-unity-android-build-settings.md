---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752729"
---
Откройте Unity, а затем откройте проект в папке `Unity`.

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** -> **Build Settings** (Файл > Параметры сборки).

В разделе **Платформа** выберите **Android**. Затем измените значение параметра **Build System** (Система сборки) на **Gradle** и установите флажок **Экспорт проекта**.

Выберите **Switch Platform** (Изменить платформу), чтобы изменить платформу на **Android**. Если отсутствуют какие-то дополнительные компоненты Android, Unity может запросить их установку.

![Параметры сборки Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Закройте окно **Build Settings** (Параметры сборки).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Скачивание и импорт пакета SDK ARCore для Unity

Скачайте файл `unitypackage` со страницы [выпуска пакета SDK ARCore для Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). В проекте Unity выберите **Assets** (Ресурсы) -> **Import Package** (Импорт пакета) -> **Custom Package...** (Пользовательский пакет), а затем выберите файл `unitypackage`, который вы только что скачали. В диалоговом окне **Import Unity Package** (Импорт пакета Unity) выберите все файлы и щелкните **Import** (Импорт).
