---
title: Краткое руководство. Перевод речи с помощью Java (Windows, Linux) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы создадите простое приложение Java для записи речи пользователя, его преобразования в другой язык и вывода текста в командную строку. Это руководство предназначено для пользователей Windows и Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cbb86dd4b24cb325b8ea6708ebc2ffc89a697757
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553401"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Краткое руководство. Перевод речи с помощью пакета SDK службы "Речь" для Java

Также доступны краткие руководства по [преобразованию речи в текст](quickstart-java-jre.md) и [виртуальному помощнику для обработки голоса](quickstart-virtual-assistant-java-jre.md).

В этом кратком руководстве вы создадите простое приложение Java, которое захватывает речь пользователя с микрофона компьютера, преобразует речь и расшифровывает переведенный текст в командную строку в режиме реального времени. Это приложение предназначено для работы в 64-разрядной версии Windows или 64-разрядной версии Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9) или в macOS 10.13 или более поздней версии. Оно создано с использованием пакета SDK Maven службы "Речь" и Eclipse Java IDE.

Полный список языков для перевода речи см. в статье, посвященной [поддержке языков](language-support.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Операционная система: 64-разрядная версия Windows, 64-разрядная версия Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9) или macOS 10.13 или более поздней версии
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) или [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

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

> [!NOTE]
> Ознакомьтесь с пакетом SDK службы "Речь" и устройством Roobo в разделе [Сведения о пакете SDK для речевых устройств](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Создание и настройка проекта

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Выберите **Файл** > **Создать** > **Класс**, чтобы добавить пустой класс в проект Java.

1. В окне **New Java Class** (Новый класс Java) введите **speechsdk.quickstart** в поле **Пакет** и **Main** в поле **Имя**.

   ![Снимок экрана окна "Новый класс Java"](media/sdk/qs-java-jre-06-create-main-java.png)

1. Замените весь код в файле `Main.java` на следующий фрагмент:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

Нажмите клавишу F11 или выберите **Запустить** > **Отладка**.

Слова, произносимые в микрофон, будут переведены на немецкий язык и записаны в окне консоли. Нажмите клавишу ВВОД, чтобы остановить запись речи.

![Снимок экрана выходных данных консоли после успешного распознавания](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры, например чтение речи из звукового файла и вывод переведенного текста в виде синтезированной речи, доступны на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для Java на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Краткое руководство Распознавание речи с использованием Java в Windows или Linux с помощью пакета SDK для службы "Речь"](quickstart-java-jre.md)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
