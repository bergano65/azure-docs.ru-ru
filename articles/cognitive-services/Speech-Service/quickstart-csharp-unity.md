---
title: Краткое руководство. Распознавание речи с помощью Unity — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве объясняется, как создать приложение для преобразования речи в текст, использующее Unity и пакет SDK службы "Речь" для Unity. После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: jhakulin
ms.openlocfilehash: a7ac6831fd21fcc0dc425b57f5d73d8c328f9350
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803466"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для Unity

Также доступны краткие руководства по [преобразованию текста в речь](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

В этом руководстве объясняется, как создать приложение для преобразования речи в текст, использующее [Unity](https://unity3d.com/) и пакет SDK службы "Речь" для Unity.
Изучив руководство, вы сможете преобразовывать речь в текст в режиме реального времени на своем устройстве.
Если вы не знакомы с Unity, рекомендуем изучить [руководство пользователя по Unity](https://docs.unity3d.com/Manual/UnityManual.html) перед разработкой приложения.

> [!NOTE]
> Пакет SDK службы "Речь" для Unity поддерживает Windows Desktop (x86 и x64) или универсальную платформу Windows (x86, x64, ARM и ARM64), Android (x86, ARM32/64) и iOS (симулятор x64, ARM32 и ARM64).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить этот проект, вам потребуется:

- [Unity 2018.3 или последующей версии](https://store.unity.com/). В [Unity 2019.1 добавлена поддержка UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Можно также использовать Visual Studio 2017 версии 15.9 или более поздней.
- Для поддержки Windows ARM64 установите [дополнительные инструменты сборки для ARM64 и пакет SDK Windows 10 для ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Ключ подписки для службы "Речь". [Получить бесплатно](get-started.md).
- Доступ к микрофону компьютера.

## <a name="create-a-unity-project"></a>Создание проекта Unity

1. Откройте Unity. Если вы используете Unity в первый раз, появится окно **Unity Hub** *<version number>* . (Можно непосредственно открыть Unity Hub, чтобы отобразилось это окно.)

   [![Окно Unity Hub](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Нажмите кнопку **Создать**. Отобразится окно **создания проекта Unity** *<version number>* .

   [![Создание проекта в Unity Hub](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. В поле **Project Name** (Имя проекта) введите **csharp-unity**.
1. В области **Templates** (Шаблоны) выберите **3D**.
1. В поле **Location** (Расположение) укажите или создайте папку, в которой будет сохранен проект.
1. Нажмите кнопку **Создать**.

Через некоторое время отобразится окно Unity Editor.

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Чтобы установить пакет SDK службы "Речь" для Unity, выполните следующие действия:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Скачайте и откройте [пакет SDK службы "Речь" для Unity](https://aka.ms/csspeech/unitypackage), который упакован как пакет ресурсов Unity (.unitypackage). При открытии пакета ресурса появляется диалоговое окно **импорта пакета Unity**.

   [![Диалоговое окно импорта пакета Unity в Unity Editor](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Убедитесь, что выбраны все файлы, и нажмите кнопку **Import** (Импортировать). Через некоторое время пакет ресурса Unity будет импортирован в проект.

Дополнительные сведения об импорте пакетов ресурсов в Unity см. в [документации по Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Добавление пользовательского интерфейса

Теперь добавим в сцену минимальный пользовательский интерфейс. Он состоит из кнопки для запуска распознавания речи и текстового поля для отображения результата. В [окне **иерархии**](https://docs.unity3d.com/Manual/Hierarchy.html) показан пример сцены, создаваемой в проекте Unity.

1. В верхней части окна **Hierarchy** (Иерархия) выберите **Create** > **UI** > **Button** (Создать > Пользовательский интерфейс > Кнопка).

   Будут созданы три игровых объекта, отображаемых в окне **Hierarchy** (Иерархия): объект **Button**, вложенный в объект **Canvas**, и объект **EventSystem**.

   [![Среда Unity Editor](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Переместитесь в представлении **сцены**](https://docs.unity3d.com/Manual/SceneViewNavigation.html) так, чтобы в [**этом представлении**](https://docs.unity3d.com/Manual/UsingTheSceneView.html) видеть холст и кнопку.

1. В [окне **Inspector**](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Инспектор), расположенном по умолчанию справа, задайте значение **0** свойствам **Pos X** и **Pos Y**, чтобы кнопка была расположена посередине холста.

1. В окне **Hierarchy** (Иерархия) выберите **Create** > **UI** > **Text** (Создать > Пользовательский интерфейс > Текст), чтобы создать объект **Text**.

1. В окне **Inspector** (Инспектор) свойствам **Pos X** и **Pos Y** задайте значения **0** и **120**, а свойствам **Width** (Ширина) и **Height** (Высота) — значения **240** и **120**. При таких значениях текстовое поле и кнопка не будут перекрываться.

По завершении представление **сцены** должно выглядеть примерно так:

[![Представление сцены в Unity Editor](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Добавление примеров кода

Чтобы добавить пример кода скрипта в проект Unity, выполните следующие действия:

1. В [окне проекта](https://docs.unity3d.com/Manual/ProjectView.html) выберите **Create** > **C# script** (Создать > Скрипт C#), чтобы добавить новый скрипт C#.

   [![Окно проекта в Unity Editor](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Назовите скрипт `HelloWorld`.

1. Дважды щелкните `HelloWorld`, чтобы изменить только что созданный скрипт.

   > [!NOTE]
   > Чтобы настроить редактор кода для использования в Unity для редактирования, выберите **Edit** > **Preferences** (Правка > Настройки) и перейдите к настройкам **External Tools** (Внешние инструменты). Дополнительные сведения см. в [руководстве пользователя по Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Замените имеющийся скрипт следующим кодом.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Найдите строку `YourSubscriptionKey` и замените ее ключом подписки службы "Речь".

1. Найдите строку `YourServiceRegion` и замените ее на [регион](regions.md) своей подписки. Например, если вы используете бесплатную пробную версию, ваш регион — `westus`.

1. Сохраните изменения в скрипте.

Теперь вернитесь в Unity Editor и добавьте скрипт в качестве компонента одного из игровых объектов:

1. В окне **Hierarchy** (Иерархия) выберите объект **Canvas**.

1. В окне **Inspector** (Инспектор) нажмите кнопку **Add Component** (Добавить компонент).

   [![Окно инспектора в Unity Editor](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. В раскрывающемся списке найдите скрипт `HelloWorld`, который мы создали ранее, и добавьте его. В окне **Inspector** (Инспектор) появится раздел скрипта **Hello World (Script)** с двумя неинициализированными свойствами **Output Text** и **Start Reco Button**. Эти свойства компонента Unity соответствуют открытым свойствам класса `HelloWorld`.

1. Щелкните средство выбора объектов у свойства **Start Reco Button** (маленький круглый значок справа от свойства) и выберите ранее созданный объект **Button**.

1. Щелкните средство выбора объектов у свойства **Output Text** и выберите ранее созданный объект **Text**.

   > [!NOTE]
   > Кнопка также имеет вложенный текстовый объект. Убедитесь, что вы случайно не выбрали его для вывода текста (или переименуйте один из текстовых объектов, используя поле **Name** (Имя) в окне **Inspector** (Инспектор), чтобы избежать этой путаницы).

## <a name="run-the-application-in-the-unity-editor"></a>Запуск приложения в Unity Editor

Теперь все готово для запуска приложения в Unity Editor.

1. Нажмите кнопку **Play** (Воспроизвести) на панели инструментов Unity Editor (треугольная стрелка вправо).

1. Перейдите к [**игровому** представлению](https://docs.unity3d.com/Manual/GameView.html) и дождитесь, пока объект **Text** отобразит **кнопку для распознавания речи**. Если приложение не запущено или не готово к работе, отображается надпись **New Text** (Новый текст).

1. Нажмите кнопку и произнесите в микрофон компьютера фразу или предложение по-английски. Ваша речь будет передана в службу "Речь" и преобразуется в текст, который отобразится в **игровом** представлении.

   [![Игровое представление в Unity Editor](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Проверьте [окно **консоли**](https://docs.unity3d.com/Manual/Console.html) на наличие сообщений отладки. Если окно **консоли** не отображается, в строке меню выберите **Window** > **General** > **Console** (Окно > Общее > Консоль), чтобы отобразить его.

1. По завершении распознавания речи нажмите кнопку **Play** (Воспроизвести) на панели инструментов Unity Editor, чтобы остановить приложение.

## <a name="additional-options-to-run-this-application"></a>Дополнительные параметры для запуска этого приложения

Это приложение также можно развернуть как приложение Android, автономное приложение Windows или приложение UWP.
Дополнительные сведения см. в [репозитории с примерами](https://aka.ms/csspeech/samples). В папке `quickstart/csharp-unity` содержится описание конфигурации для этих дополнительных целевых объектов.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Train a model for Custom Speech](how-to-custom-speech-train-model.md) (Обучение модели для Пользовательского распознавания речи)
