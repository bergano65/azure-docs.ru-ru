---
title: Краткое руководство. Синтез речи с помощью службы Unity — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Используйте это руководство при создании приложения для преобразования речи в текст с помощью Unity и пакета Speech SDK для Unity. После завершения можно будет синтезировать речь из текста в режиме реального времени через микрофон устройства.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: b4d329d9d3c2a259fb90b0278c54ba8590590995
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675481"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Краткое руководство. Синтез речи с помощью пакета SDK службы "Речь" для Unity

Доступны также краткие руководства по [распознаванию речи](quickstart-csharp-unity.md).

Используйте этот учебник при создании приложения для преобразования речи в текст с помощью [Unity](https://unity3d.com/) и пакета SDK службы "Речь" в Cognitive Services для Unity.
После завершения можно будет синтезировать речь из текста в режиме реального времени через микрофон устройства.
Если вы не знакомы с Unity, изучите [Unity User Manual (2019.2)](https://docs.unity3d.com/Manual/UnityManual.html) (Руководство пользователя Unity (2019.2)) перед началом разработки приложения.

> [!NOTE]
> Unity поддерживает Windows Desktop (x86 и x64) или универсальную платформу Windows (x86, x64, ARM и ARM64), Android (x86, ARM32/64) и iOS (симулятор x64, ARM32 и ARM64).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить этот проект, вам потребуется:

* [Unity 2018.3 или последующей версии](https://store.unity.com/). В [Unity 2019.1 добавлена поддержка UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Можно также использовать Visual Studio 2017 версии 15.9 или более поздней.
* Для поддержки Windows ARM64 установите [дополнительные инструменты сборки для ARM64 и пакет SDK Windows 10 для ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
* Ключ подписки для службы "Речь". [Получить бесплатно](get-started.md).

## <a name="create-a-unity-project"></a>Создание проекта Unity

* Запустите Unity и на вкладке **Проекты** выберите **Создать**.
* В поле **Имя проекта** введите значение **csharp-unity**, а в поле **Шаблон** — **3D** и выберите расположение.
  Затем щелкните **Создать проект**.
* Через некоторое время должно появиться окно Unity Editor.

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Пакет SDK службы "Речь" для Unity (бета-версия) упакован как пакет ресурсов Unity (.unitypackage). Скачайте его с [этого веб-сайта](https://aka.ms/csspeech/unitypackage).
* Импортируйте пакет SDK службы "Речь", выбрав **Ресурсы-контейнеры** > **Импорт пакета** > **Custom Package** (Пользовательский пакет). Дополнительные сведения см. в [документации по Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* В средстве выбора файлов выберите скачанный файл пакета SDK службы "Речь" формата UNITYPACKAGE.
* Убедитесь, что выбраны все файлы, и нажмите кнопку **Import** (Импортировать).

  ![Снимок экрана с Unity Editor при импортировании пакета ресурсов SDK службы "Речь" для Unity](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-a-ui"></a>Добавление пользовательского интерфейса

В нашу сцену мы добавим минимальный пользовательский интерфейс, состоящий из поля ввода текста для синтеза речи, кнопки активации синтеза речи и текстового поля для отображения результата.

* В [окне иерархии](https://docs.unity3d.com/Manual/Hierarchy.html) (по умолчанию слева) показан пример сцены создания проекта Unity.
* В верхней части окна **Hierarchy** (Иерархия) выберите **Create** (Создать), а затем **UI** (Пользовательский интерфейс)  > **Input Field** (Поле ввода).
* Параметр создает три игровых объекта, которые можно увидеть в окне **Hierarchy** (Иерархия): объект **Input Field** (Поле ввода), вложенный в объект **Canvas** (Полотно) и объект **EventSystem**.
* [Перемещайтесь по представлению сцены](https://docs.unity3d.com/Manual/SceneViewNavigation.html) для удобного просмотра панели холста и поля ввода в [представлении сцены](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Выберите объект **Input Field** (Поле ввода) в окне **Hierarchy** (Иерархия), чтобы отобразить его параметры в окне [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Инспектор) (по умолчанию справа).
* Задайте для свойств **Pos X** и **Pos Y** значение **0**, чтобы поле ввода было расположено в середине панели холста.
* В верхней части окна **Hierarchy** (Иерархия) щелкните **Create** (Создать). Выберите **UI** (Пользовательский интерфейс) > **Button** (Кнопка), чтобы создать кнопку.
* Выберите объект **Button** (Кнопка) в окне **Hierarchy** (Иерархия), чтобы отобразить его параметры в окне [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Инспектор) (по умолчанию справа).
* Задайте для свойств **Pos X** и **Pos Y** значения **0** и **-48**. Задайте для свойств **Width** (Ширина) и **Height** (Высота) значения **160** и **30**, чтобы убедиться, что кнопка и поле ввода не перекрываются.
* В верхней части окна **Hierarchy** (Иерархия) щелкните **Create** (Создать). Выберите **UI** (Пользовательский интерфейс)  > **Text** (Текст), чтобы создать текстовое поле.
* Выберите объект **Text** (Текст) в окне **Hierarchy** (Иерархия), чтобы отобразить его параметры в окне [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Инспектор) (по умолчанию справа).
* Задайте для свойств **Pos X** и **Pos Y** значения **0** и **80**. Задайте для свойств **Width** (Ширина) и **Height** (Высота) значения **320** и **80**, чтобы убедиться, что текстовое поле и поле ввода не перекрываются.
* В верхней части окна **Hierarchy** (Иерархия) щелкните **Create** (Создать). Выберите **Audio** (Аудио)  > **Audio Source** (Источник звука), чтобы создать источник звука.

По завершении пользовательский интерфейс должен выглядеть примерно так:

[![Снимок экрана пользовательского интерфейса краткого руководства в Unity Editor](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. В [окне проекта](https://docs.unity3d.com/Manual/ProjectView.html) (по умолчанию слева внизу) нажмите кнопку **Создать**, а затем выберите **Скрипт C#** . Назовите скрипт `HelloWorld`.

1. Отредактируйте скрипт, дважды щелкнув его.

   > [!NOTE]
   > Вы можете настроить запуск редактора кода, выбрав **Редактировать** > **Параметры**. Дополнительные сведения см. в [руководстве пользователя по Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Замените весь код на приведенный ниже:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Найдите строку `YourSubscriptionKey` и замените ее ключом подписки службы "Речь".

1. Найдите и замените строку `YourServiceRegion` на [регион](regions.md), связанный с вашей подпиской. Например, если вы используете бесплатную пробную версию, ваш регион — `westus`.

1. Сохраните изменения в скрипте.

1. Вернитесь в Unity Editor и добавьте скрипт в качестве компонента одного из игровых объектов:

   * Выберите объект **Text** (Текст) в окне **Hierarchy** (Иерархия), чтобы открыть параметр в окне [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Инспектор) (по умолчанию справа).
   * В окне **Inspector** (Инспектор) нажмите кнопку **Add Component** (Добавить компонент). Затем найдите созданный ранее сценарий `HelloWorld` и добавьте его.
   * Компонент HelloWorld имеет четыре неинициализированных свойства: **Output Text** (Выходной текст), **Input Field** (Поле ввода), **Speak Button** (Кнопка "Произнести") и **Audio Source** (Источник аудио), соответствующие общедоступным свойствам класса `HelloWorld`.
     Чтобы связать их, выберите средство выбора объектов (маленький значок круга справа от свойства). Выберите созданные ранее объекты Text (Текст) и Button (Кнопка).

     > [!NOTE]
     > Поле ввода и кнопка также имеют вложенный текстовый объект. Убедитесь, что вы не выберете его случайно для вывода текста. Кроме того, можно переименовать текстовые объекты, использующие поле **Name** (Имя) в окне **Inspector** (Инспектор), чтобы избежать этой путаницы.

## <a name="run-the-application-in-the-unity-editor"></a>Запуск приложения в Unity Editor

* Нажмите кнопку **Play** (Воспроизвести) на панели инструментов Unity Editor, которая находится под строкой меню.
* После запуска приложения введите текст в поле ввода и нажмите кнопку. Введенный текст передается в службы "Речь" и синтезируется в речь, которая воспроизводится через динамик.

  [![Снимок экрана запущенного краткого руководства в окне Unity Game](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Проверьте [окно консоли](https://docs.unity3d.com/Manual/Console.html) на наличие сообщений отладки.
* По завершении синтеза речи нажмите кнопку **Play** (Воспроизвести) на панели инструментов Unity Editor, чтобы остановить работу приложения.

## <a name="additional-options-to-run-this-application"></a>Дополнительные параметры для запуска этого приложения

Это приложение также можно развернуть на Android как автономное приложение Windows или приложение UWP.
Ознакомьтесь с [примером репозитория](https://aka.ms/csspeech/samples) в папке quickstart/csharp-unity, который описывает конфигурацию этих дополнительных целей.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для C# на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка голосов](how-to-customize-voice-font.md)
- [Запись образцов голосов](record-custom-voice-samples.md)
