---
title: Краткое руководство. Синтез речи в службе "Речь" для C# (Unity)
titleSuffix: Azure Cognitive Services
description: Используйте это руководство при создании приложения для преобразования речи в текст с помощью Unity и пакета Speech SDK для Unity. После завершения можно будет синтезировать речь из текста в режиме реального времени через микрофон устройства.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 0934738c557ac6d26867546783797cf5d2b75056
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275395"
---
> [!NOTE]
> Unity поддерживает Windows Desktop (x86 и x64) или универсальную платформу Windows (x86, x64, ARM и ARM64), Android (x86, ARM32/64) и iOS (симулятор x64, ARM32 и ARM64).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp).

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

[![Снимок экрана пользовательского интерфейса краткого руководства в Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. В [окне проекта](https://docs.unity3d.com/Manual/ProjectView.html) (по умолчанию слева внизу) нажмите кнопку **Создать**, а затем выберите **Скрипт C#** . Назовите скрипт `HelloWorld`.

1. Отредактируйте скрипт, дважды щелкнув его.

   > [!NOTE]
   > Вы можете настроить запуск редактора кода, выбрав **Редактировать** > **Параметры**. Дополнительные сведения см. в [руководстве пользователя по Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Замените весь код на приведенный ниже:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Найдите и замените строку `YourSubscriptionKey` на ключ подписки службы "Речь".

1. Найдите и замените строку `YourServiceRegion` на [регион](~/articles/cognitive-services/Speech-Service/regions.md), связанный с вашей подпиской. Например, если вы используете бесплатную пробную версию, ваш регион — `westus`.

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
* После запуска приложения введите текст в поле ввода и нажмите кнопку. Текст передается в службу "Речь" и синтезируется в речь, которая воспроизводится через динамик.

  [![Снимок экрана запущенного краткого руководства в окне Unity Game](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Проверьте [окно консоли](https://docs.unity3d.com/Manual/Console.html) на наличие сообщений отладки.

## <a name="additional-options-to-run-this-application"></a>Дополнительные параметры для запуска этого приложения

Это приложение также можно развернуть на Android как автономное приложение Windows или приложение UWP.
Ознакомьтесь с [примером репозитория](https://aka.ms/csspeech/samples) в папке quickstart/csharp-unity, который описывает конфигурацию этих дополнительных целей.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>См. также раздел

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
