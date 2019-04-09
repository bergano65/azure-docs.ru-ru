---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 33c932c36cd6de730d3768d596a214c442d74ae1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632900"
---
Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Платформа** выберите **Android**. Измените значение параметра **Build System** (Система сборки) на **Gradle** и выберите **Export Project** (Экспортировать проект).

Выберите **Switch Platform** (Изменить платформу), чтобы изменить платформу на **Android**. Если отсутствуют какие-то дополнительные компоненты Android, Unity может запросить их установку.

![Окно параметров сборки Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Закройте окно **Build Settings** (Параметры сборки).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>скачать и импортировать пакеты SDK с поддержкой ARCore для Unity;

Скачайте файл `unitypackage` со страницы [выпусков пакета SDK ARCore для Unity 1.5](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). В проекте Unity выберите **Assets** (Ресурсы) > **Import Package** (Импортировать пакет) > **Custom Package** (Пользовательский пакет), а затем выберите файл `unitypackage`, который вы только что скачали. В диалоговом окне **Import Unity Package** (Импорт пакета Unity) выберите все файлы и щелкните **Import** (Импортировать).
