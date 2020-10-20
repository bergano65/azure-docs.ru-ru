---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 36d12b29054f736b65af5ac411adbc26d870b982
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014157"
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

Дополнительные сведения см. в справочнике по [REST API преобразования речи в текст](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text).
