---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: abe40898643fa2fd11952d2fb6626034fb408133
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505262"
---
## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=jre).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=jre).

## <a name="add-sample-code"></a>Добавление примеров кода

1. Выберите **Файл** > **Создать** > **Класс**, чтобы добавить пустой класс в проект Java.

1. В окне **New Java Class** (Новый класс Java) введите **speechsdk.quickstart** в поле **Пакет** и **Main** в поле **Имя**.

   ![Снимок экрана окна "Новый класс Java"](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Замените весь код в файле `Main.java` на следующий фрагмент:

   ```Java
   package speechsdk.quickstart;

   import java.util.concurrent.Future;
   import com.microsoft.cognitiveservices.speech.*;

   /**
    * Quickstart: recognize speech using the Speech SDK for Java.
    */
   public class Main {

       /**
        * @param args Arguments are ignored in this sample.
        */
       public static void main(String[] args) {
           try {
               // Replace below with your own subscription key
               String speechSubscriptionKey = "YourSubscriptionKey";
               // Replace below with your own service region (e.g., "westus").
               String serviceRegion = "YourServiceRegion";
               // Replace below with your own filename.
               String audioFileName = "helloworld.wav";
               // Replace below with your own filename.
               String text = "Hello world!";

               int exitCode = 1;
               SpeechConfig config = SpeechConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
               assert(config != null);

               AudioConfig audioOutput = AudioConfig.fromWavFileInput(audioFileName);
               assert(audioOutput != null);

               SpeechSynthesizer synth = new SpeechSynthesizer(config, audioOutput);
               assert(synth != null);

               Future<SpeechSynthesisResult> task = synth.SpeakTextAsync(text);
               assert(task != null);

               SpeechSynthesisResult result = task.get();
               assert(result != null);

               if (result.getReason() == ResultReason.SynthesizingAudioCompleted) {
                   System.out.println("Speech synthesized to [" + audioFilename + "] for text [" + text + "]");
                   exitCode = 0;
               }
               else if (result.getReason() == ResultReason.Canceled) {
                   SpeechSynthesisCancellationDetails cancellation = SpeechSynthesisCancellationDetails.fromResult(result);
                   System.out.println("CANCELED: Reason=" + cancellation.getReason());

                   if (cancellation.getReason() == CancellationReason.Error) {
                       System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                       System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                       System.out.println("CANCELED: Did you update the subscription info?");
                   }
               }

               result.close();
               synth.close();

               System.exit(exitCode);
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());

               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Замените строку `helloworld.wav` собственным именем файла.

1. Замените строку `Hello world!` собственным текстом.

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

Нажмите клавишу F11 или выберите **Запустить** > **Отладка**.
Текст преобразуется в речь и сохраняется в указанных звуковых данных.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
