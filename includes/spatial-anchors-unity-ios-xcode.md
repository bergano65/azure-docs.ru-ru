---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: d8b6b1bfcbceb1168d0f74c73e72bd42b41bb2ec
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562506"
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
> Если у вас возникнет ошибка `library not found for -lPods-Unity-iPhone`, скорее всего, вы открыли файл `.xcodeproj` вместо `.xcworkspace`. 

Выберите корневой узел **Unity iPhone**, чтобы просмотреть параметры проекта, а затем — вкладку **General** (Общие).

В разделе **Signing** (Подпись) проверьте, установлен ли флажок **Automatically manage signing** (Автоматическое управление подписями). Если нет, установите его, а затем в открывшемся диалоговом окне выберите **Enable Automatic** (Включить автоматическое), чтобы сбросить параметры сборки.

В разделе **Deployment Info** (Сведения о развертывании) убедитесь, что для **цели развертывания** установлено значение `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Развертывание приложения на устройстве iOS

Подключите устройство iOS к Mac и задайте **активную схему** для устройства iOS.

![Выбор устройства](./media/spatial-anchors-unity/select-device.png)

Выберите параметр **Build and then run the current scheme** (Сборка, а затем запуск текущей схемы).

![Развертывание и запуск](./media/spatial-anchors-unity/deploy-run.png)