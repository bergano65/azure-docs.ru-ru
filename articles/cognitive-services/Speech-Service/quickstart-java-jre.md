---
title: Краткое руководство. Распознавание речи с помощью Java (Windows, Linux, macOS) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как создать простое приложение Java, которое записывает речь пользователя с микрофона компьютера и транскрибирует ее.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9438cd1345d324b068db8837216afb39241cb2a2
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802464"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для Java

Кроме того, доступны краткие руководства по [переводу речи](quickstart-translate-speech-java-jre.md), [виртуальному помощнику для обработки голоса](quickstart-virtual-assistant-java-jre.md) и [синтезу речи](quickstart-text-to-speech-java-jre.md).

При необходимости переключитесь на другой язык программирования и (или) среду:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

В этой статье вы создадите консольное приложение Java с помощью [пакета SDK службы "Речь"](speech-sdk.md). Вы преобразуете речь с микрофона вашего компьютера в текст в реальном времени. Приложение создается с помощью пакета SDK Maven службы "Речь" и Eclipse Java IDE (версии 4.8) на 64-разрядной версии Windows, 64-разрядной версии Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9), а также на macOS 10.13 или более поздней версии. Оно работает в 64-разрядной среде выполнения Java 8 (JRE).

> [!NOTE]
> Ознакомьтесь с пакетом SDK службы "Речь" и устройством Roobo в разделе [Сведения о пакете SDK для речевых устройств](speech-devices-sdk.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Операционная система: 64-разрядная версия Windows, 64-разрядная версия Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9) или macOS 10.13 или более поздней версии
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) или [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Ключ подписки Azure для службы "Речь". [Получить бесплатно](get-started.md).

Если вы используете Linux, убедитесь, что эти зависимости установлены до запуска Eclipse.

* Для Ubuntu.

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Для Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Если вы используете Windows (64-разрядная версия), убедитесь, что у вас установлен распространяемый компонент Microsoft Visual C++ для вашей платформы.
* [Скачать распространяемый компонент Microsoft Visual C++ для Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Создание и настройка проекта

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Выберите **Файл** > **Создать** > **Класс**, чтобы добавить пустой класс в проект Java.

1. В окне **New Java Class** (Новый класс Java) введите **speechsdk.quickstart** в поле **Пакет** и **Main** в поле **Имя**.

   ![Снимок экрана окна "Новый класс Java"](media/sdk/qs-java-jre-06-create-main-java.png)

1. Замените весь код в файле `Main.java` на следующий фрагмент:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

Нажмите клавишу F11 или выберите **Запустить** > **Отладка**.
Слова, произносимые в микрофон, в течение следующих 15 секунд будут распознаны и записаны в окне консоли.

![Снимок экрана выходных данных консоли после успешного распознавания](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, такие как чтение речи из аудиофайла, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для Java на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Краткое руководство по переводу речи с помощью Java (Windows, Linux, macOS)](quickstart-translate-speech-java-jre.md)
- [Краткое руководство по синтезу речи с помощью Java (Windows, Linux, macOS)](quickstart-text-to-speech-java-jre.md)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
