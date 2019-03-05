---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890986"
---
Выберите **Build** (Сборка), чтобы открыть диалоговое окно. Затем выберите папку для экспорта проекта Xcode.

После завершения экспорта появится папка, содержащая экспортированный проект Xcode.

### <a name="open-the-xcode-project"></a>Открытие проекта Xcode

В папке экспортированного проекта Xcode выполните следующую команду, чтобы установить CocoaPods, необходимый для проекта:

```bash
pod install --repo-update
```

Теперь вы можете открыть файл `Unity-iPhone.xcworkspace`, чтобы открыть проект в Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Если у вас возникнет ошибка `library not found for -lPods-Unity-iPhone`, скорее всего, вы открыли файл `.xcodeproj` вместо `.xcworkspace`. Откройте файл `.xcworkspace` и повторите попытку.

Выберите корневой узел **Unity iPhone**, чтобы просмотреть параметры проекта, а затем — вкладку **General** (Общие).

В разделе **Signing** (Подпись) выберите **Automatically manage signing** (Автоматическое управление подписями). В появившемся диалоговом окне выберите **Enable Automatic** (Включить автоматически), чтобы сбросить параметры сборки.

В разделе **Deployment Info** (Сведения о развертывании) убедитесь, что для **цели развертывания** установлено значение `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Развертывание приложения на устройстве iOS

Подключите устройство iOS к Mac и задайте **активную схему** для устройства iOS.

![Выбор устройства](./media/spatial-anchors-unity/select-device.png)

Выберите параметр **Build and then run the current scheme** (Сборка, а затем запуск текущей схемы).

![Развертывание и запуск](./media/spatial-anchors-unity/deploy-run.png)