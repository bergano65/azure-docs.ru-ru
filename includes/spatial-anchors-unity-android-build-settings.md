---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 9798e5f76881be38fb27e1f428565caba6e50bf2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135079"
---
Откройте **Build Settings** (Параметры сборки), выбрав пункты **File** > **Build Settings** (Файл > Параметры сборки).

В разделе **Платформа** выберите **Android**. Измените значение параметра **Build System** (Система сборки) на **Gradle** и убедитесь, что флажок **Экспорт проекта** не установлен.

Выберите **Switch Platform** (Изменить платформу), чтобы изменить платформу на **Android**. Если отсутствуют какие-то дополнительные компоненты Android, Unity может запросить их установку.

![Окно параметров сборки Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Закройте окно **Build Settings** (Параметры сборки).

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>скачать и импортировать пакеты SDK с поддержкой ARCore для Unity;

Скачайте файл `unitypackage` со страницы [выпусков пакета SDK ARCore для Unity 1.7](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). В проекте Unity выберите **Assets** (Ресурсы) > **Import Package** (Импортировать пакет) > **Custom Package** (Пользовательский пакет), а затем выберите файл `unitypackage`, который вы только что скачали. В диалоговом окне **Import Unity Package** (Импорт пакета Unity) выберите все файлы и щелкните **Import** (Импортировать).
