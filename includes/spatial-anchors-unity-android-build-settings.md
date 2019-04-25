---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012128"
---
Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Платформа** выберите **Android**. Измените значение параметра **Build System** (Система сборки) на **Gradle** и выберите **Export Project** (Экспортировать проект).

Выберите **Switch Platform** (Изменить платформу), чтобы изменить платформу на **Android**. Если отсутствуют какие-то дополнительные компоненты Android, Unity может запросить их установку.

![Окно параметров сборки Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Закройте окно **Build Settings** (Параметры сборки).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>скачать и импортировать пакеты SDK с поддержкой ARCore для Unity;

Скачайте файл `unitypackage` со страницы [выпусков пакета SDK ARCore для Unity 1.7](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). В проекте Unity выберите **Assets** (Ресурсы) > **Import Package** (Импортировать пакет) > **Custom Package** (Пользовательский пакет), а затем выберите файл `unitypackage`, который вы только что скачали. В диалоговом окне **Import Unity Package** (Импорт пакета Unity) выберите все файлы и щелкните **Import** (Импортировать).
