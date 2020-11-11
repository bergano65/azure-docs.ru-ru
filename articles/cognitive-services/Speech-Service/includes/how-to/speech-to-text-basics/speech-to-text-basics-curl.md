---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425348"
---
В этом кратком руководстве показано, как преобразовать речь в текст с помощью службы "Речь" и cURL.

Общие сведения о концепциях преобразование речи в текст см. в [обзоре](../../../speech-to-text.md).

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки, [попробуйте службу "Речь" бесплатно](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Преобразование речи в текст

В командной строке выполните приведенную ниже команду. В команду потребуется вставить указанные ниже значения.
- Ключ подписки службы "Речь".
- Регион службы "Речь".
- Путь к входному аудиофайлу. Аудиофайлы можно создавать с помощью [преобразования текста в речь](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Вы получите примерно такой ответ:

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Дополнительные сведения см. в справочнике по [REST API преобразования речи в текст](../../../rest-speech-to-text.md).