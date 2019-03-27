---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 53f480b8858e2bbe7d4699d8637ecaa5ab0c08a3
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305331"
---
В Unity откройте проект в папке `Unity`.

Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Платформа** выберите **Android**. Измените значение параметра **Build System** (Система сборки) на **Gradle** и выберите **Export Project** (Экспортировать проект).

Выберите **Switch Platform** (Изменить платформу), чтобы изменить платформу на **Android**. Если отсутствуют какие-то дополнительные компоненты Android, Unity может запросить их установку.

![Окно параметров сборки Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Закройте окно **Build Settings** (Параметры сборки).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Скачивание и импорт пакета SDK ARCore для Unity

Скачайте файл `unitypackage` со страницы [выпуска пакета SDK ARCore для Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). В проекте Unity выберите **Assets** (Ресурсы) > **Import Package** (Импортировать пакет) > **Custom Package** (Пользовательский пакет), а затем выберите файл `unitypackage`, который вы только что скачали. В диалоговом окне **Import Unity Package** (Импорт пакета Unity) выберите все файлы и щелкните **Import** (Импортировать).
