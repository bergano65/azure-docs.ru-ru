---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940755"
---
Из этого краткого руководства вы узнаете, как преобразовать текст в речь с помощью службы "Речь" и cURL.

Основные понятия, касающиеся преобразования текста в речь, см. в [обзорной](../../../text-to-speech.md) статье.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки, [попробуйте службу "Речь" бесплатно](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Преобразование текста в речь

В командной строке выполните приведенную ниже команду. В команду потребуется вставить указанные ниже значения.
- Ключ подписки службы "Речь".
- Регион службы "Речь".

Также может потребоваться изменить следующие значения.
- Значение заголовка `X-Microsoft-OutputFormat`, которое определяет формат аудиовыхода. Список поддерживаемых форматов аудиовыхода см. в [справочнике по REST API для преобразования текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs).
- Голос речевого вывода. Список голосов, доступных для вашей конечной точки службы "Речь", см. в следующем разделе.
- Выходной файл. В этом примере мы направляем ответ от сервера в файл `output.wav`.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Список доступных голосов для конечной точки службы "Речь"

Чтобы получить список доступных голосов для вашей конечной точки службы "Речь", выполните следующую команду.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Вы получите примерно такой ответ:

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
