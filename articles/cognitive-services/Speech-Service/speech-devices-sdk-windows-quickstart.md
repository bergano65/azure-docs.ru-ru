---
title: Краткое руководство. Запуск пакета SDK для устройств, подключаемых к службе "Речь", в Windows — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Предварительные требования и инструкции по началу работы с Windows Speech Devices SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: ad90a6443cc1c94bcdb730e783b82dfdd4798676
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553008"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Краткое руководство. Запустите пример приложения Speech Devices SDK в Windows

В этом кратком руководстве вы узнаете, как использовать пакет Speech Devices SDK для Windows для создания устройства с поддержкой речевых функций или его использования в качестве устройства [Транскрипция диалога](conversation-transcription-service.md) (Conversation Transcription). Сейчас поддерживается только [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/).

Приложение создается с использованием пакета SDK для распознавания речи и интегрированной среды разработки Eclipse Java (версия 4) в 64-разрядной версии Windows. Оно работает в 64-разрядной среде выполнения Java 8 (JRE).

Для этого руководства требуется [учетная запись Azure Cognitive Services](get-started.md) и ресурс службы "Речь". Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.

Исходный код [примера приложения](https://aka.ms/sdsdk-download-JRE) входит в состав пакета SDK для речевых устройств. Он также [доступен на веб-сайте GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Операционная система: 64-разрядная версия Windows
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Только [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) или [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [распространяемые компоненты Microsoft Visual C++.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).
* Скачайте последнюю версию [пакета SDK для речевых устройств](https://aka.ms/sdsdk-download-JRE) для Java и извлеките ZIP-файл в рабочую папку.
   > [!NOTE]
   > В ZIP-файл JRE-Sample-Release входит пример приложения JRE. В рамках этого краткого руководства допускается следующий путь для извлечения приложения: "C:\SDSDK\JRE-Sample-Release".

Сейчас транскрибирование бесед доступно для американского английского и китайского языков в регионах "центральная часть США" и "Восточная Азия". Для использования транскрибирования бесед вам понадобится речевой ключ в одном из упомянутых регионов.

Если вы планируете использовать намерения, вам понадобится подписка на [службу "Распознавания речи" (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription). Дополнительные сведения о службе распознавания речи и распознавании намерений см. в статье [Распознавание намерений в речи с помощью пакета SDK службы распознавания речи для C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). [Модель примера LUIS](https://aka.ms/sdsdk-luis) доступна для этого приложения.

## <a name="create-and-configure-the-project"></a>Создание и настройка проекта

1. Запустите Eclipse.

1. В **средстве запуска интегрированной среды разработки Eclipse** в поле **Workspace** (Рабочая область) введите имя каталога новой рабочей области. Затем выберите **Запустить**.

   ![Снимок экрана средства запуска Eclipse](media/speech-devices-sdk/eclipse-launcher.png)

1. После этого отобразится главное окно интегрированной среды разработки Eclipse. Если отобразится экран приветствия, закройте его.

1. В строке меню Eclipse создайте новый проект, выбрав **File** > **New** > **Java Project** (Файл > Создать > Проект Java). Если последнего пункта нет, выберите **Project** (Проект), а затем — **Java Project** (Проект Java).

1. После этого запустится мастер **создания проектов Java**. **Перейдите** к расположению примера проекта. Выберите **Готово**.

   ![Снимок экрана с изображением мастера создания проекта Java](media/speech-devices-sdk/eclipse-new-java-project.png)

1. В **обозревателе пакетов** щелкните проект правой кнопкой мыши. В контекстном меню выберите **Настроить** > **Convert to Maven Project** (Преобразовать в проект Maven). Выберите **Готово**.

   ![Снимок экрана обозревателя пакетов](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Скопируйте `kws.table`,`participants.properties` и `Microsoft.CognitiveServices.Speech.extension.pma.dll` в папку проекта **target/classes**.

## <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Добавьте свой ключ подписки на речевые службы в исходный код. Если вы хотите попробовать распознать намерения, также добавьте свой ключ подписки [службы "Распознавание речи"](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) и идентификатор приложения.

   Для распознавания речи и LUIS ваши данные записываются в файл `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Если вы пользуетесь транскрибированием бесед, информация о вашем речевом ключе и регионе также должна быть в файле `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. По умолчанию слово для пробуждения (ключевое слово) — "Компьютер". Можно также попробовать одно из других предоставленных слов для пробуждения — "Машина" или "Помощник". Файлы ресурсов для этих альтернативных слов пробуждения находятся в пакете SDK для речевых устройств в папке "keyword". Например, `C:\SDSDK\JRE-Sample-Release\keyword\Computer` содержит файлы, используемые для слова активации "Computer".

   > [!TIP]
   > Кроме того, вы можете [создать пользовательское слово пробуждения](speech-devices-sdk-create-kws.md).

    Для использования нового слова активации обновите следующие две строки в файле `FunctionsList.java` и скопируйте пакет слов активации в приложение. Например, для использования слова активации "Machine" из пакета слов активации `kws-machine.zip`:

   * Скопируйте пакет слов активации в папку проекта **target/classes**.

   * Укажите в файле `FunctionsList.java` ключевое слово и имя пакета:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Запуск примера приложения из Eclipse

1. В строке меню Eclipse нажмите**Run** > **Run As** > **приложения Java**. Затем выберите **FunctionsList** и **ОК**.

   ![Снимок экрана: выбор приложения Java](media/speech-devices-sdk/eclipse-run-sample.png)

1. Запускается пример приложения пакета SDK для речевых устройств, отображающий следующие параметры:

   ![Пример приложения пакета SDK для речевых устройств и параметры](media/speech-devices-sdk/java-sample-app-windows.png)

1. Попробуйте новую демоверсию **транскрибирования бесед**. Запустите транскрибирование, выбрав **Session** (Сеанс) > **Start** (Запустить). По умолчанию все являются гостями. Тем не менее, если у вас есть голосовые подписи участников, вы можете добавить их в файл `participants.properties` в папке проекта **target/classes**. Сведения о том, как создать голосовую подпись, см. в статье о [транскрибировании бесед (пакет SDK)](how-to-use-conversation-transcription-service.md).

   ![Демоверсия приложения транскрибирования бесед](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Создание и запуск автономного приложения

1. В **обозревателе пакетов** щелкните проект правой кнопкой мыши. Выберите **Export** (Экспорт). 

1. Появится окно **Export** (Экспорт). Разверните узел **Java** и выберите **Runnable JAR file** (Готовый к запуску JAR-файл), а затем нажмите кнопку **Next** (Далее).

   ![Снимок экрана: окно экспорта](media/speech-devices-sdk/eclipse-export-windows.png) 

1. Появится окно **Runnable JAR File Export** (Экспорт готовых к запуску JAR-файлов). Выберите **назначение экспорта** для приложения, а затем нажмите кнопку **Finish** (Готово).
 
   ![Снимок экрана: экспорт готовых к запуску JAR-файлов](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Поместите `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` и `Microsoft.CognitiveServices.Speech.extension.pma.dll` в папку назначения, выбранную выше, так как эти файлы необходимы для приложения.

1. Чтобы запустить автономное приложение, выполните следующую команду:

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Просмотр заметки о выпуске](devices-sdk-release-notes.md)
