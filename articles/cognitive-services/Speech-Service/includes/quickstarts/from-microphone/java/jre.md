---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: cd00fb2df0063bd8bc753f9078194648f6745f1b
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156040"
---
## <a name="prerequisites"></a>Предварительные требования

Необходимые условия:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?tabs=jre)
> * Убедитесь, что у вас есть доступ к микрофону для аудиозахвата.

## <a name="add-sample-code"></a>Добавление примеров кода

1. Выберите **Файл** > **Создать** > **Класс**, чтобы добавить пустой класс в проект Java.

1. В окне **New Java Class** (Новый класс Java) введите **speechsdk.quickstart** в поле **Пакет** и **Main** в поле **Имя**.

   ![Снимок экрана окна "Новый класс Java"](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Замените весь код в файле `Main.java` на следующий фрагмент:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` параметром пакета SDK для службы "Речь" на основе [региона](https://aka.ms/speech/sdkregion), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

> [!NOTE]
> Пакет SDK для распознавания речи по умолчанию распознает использование языкового стандарта en-us. Дополнительные сведения о выборе исходного языка см. в разделе [Specify source language for speech to text](../../../../how-to-specify-source-language.md) (Указание исходного языка для преобразования речи в текст).

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

Нажмите клавишу F11 или выберите **Запустить** > **Отладка**.
Слова, произносимые в микрофон, в течение следующих 15 секунд будут распознаны и записаны в окне консоли.

![Снимок экрана выходных данных консоли после успешного распознавания](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]
