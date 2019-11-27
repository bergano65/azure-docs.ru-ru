---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933473"
---
Выберите **Build** (Сборка). В открывшемся диалоговом окне выберите папку, в которую необходимо экспортировать проект Xcode.

После завершения экспорта появится папка, содержащая экспортированный проект Xcode.

> [!NOTE]
> Если появится окно с запросом на замену или добавление, мы рекомендуем выбрать **Append** (Добавить), так как это быстрее. Параметр **Replace** (Заменить) следует выбирать, только если вы заменяете ресурсы в сцене (например, если добавляете, удаляете, изменяете отношения "родительское — дочернее" или свойства). Если вы только вносите изменения в исходный код, параметра **Append** (Добавить) достаточно.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Преобразование проекта Xcode в проект xcworkspace, содержащего ссылки на Пространственные привязки Azure

В папке экспортированного проекта Xcode выполните следующую команду в окне терминала, чтобы установить необходимое для проекта приложение CocoaPods:

```bash
pod install --repo-update
```

Теперь вы можете открыть файл `Unity-iPhone.xcworkspace`, чтобы открыть проект в Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> См. [руководство по устранению неполадок с CocoaPod после обновления до macOS Catalina (10.15)](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015).

Выберите корневой узел **Unity iPhone**, чтобы просмотреть параметры проекта, а затем — вкладку **General** (Общие).

В разделе **Signing** (Подпись) проверьте, установлен ли флажок **Automatically manage signing** (Автоматическое управление подписями). Если нет, установите его, а затем в открывшемся диалоговом окне выберите **Enable Automatic** (Включить автоматическое), чтобы сбросить параметры сборки.

В разделе **Deployment Info** (Сведения о развертывании) убедитесь, что для **цели развертывания** установлено значение `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Развертывание приложения на устройстве iOS

Подключите устройство iOS к Mac и задайте **активную схему** для устройства iOS.

![Выбор устройства](./media/spatial-anchors-unity/select-device.png)

Выберите параметр **Build and then run the current scheme** (Сборка, а затем запуск текущей схемы).

![Развертывание и запуск](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Если у вас возникнет ошибка `library not found for -lPods-Unity-iPhone`, скорее всего, вы открыли файл `.xcodeproj` вместо `.xcworkspace`.
