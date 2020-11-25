---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015503"
---
В этом руководстве показаны базовые шаблоны разработки для Распознавания говорящего с помощью пакета SDK службы Речи. В статье рассматриваются следующие темы:

* Проверка, зависящая от текста и не зависящая от текста.
* Идентификация говорящего для идентификации образца голоса в группе голосов.
* Удаление речевых профилей.

Общие сведения о концепциях распознавания речи см. в статье [Обзор](../../../speaker-recognition-overview.md).

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки, [попробуйте службу "Речь" бесплатно](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Распознавание говорящего в настоящее время поддерживается *только* в ресурсах распознавания речи Azure, созданных в регионе `westus`.

## <a name="text-dependent-verification"></a>Проверка, зависящая от текста

Проверка говорящего является подтверждением того, что говорящий соответствует известному или **зарегистрированному** голосу. В первую очередь необходимо **зарегистрировать** речевой профиль, чтобы службе было с чем сравнивать будущие примеры голоса. В этом примере вы регистрируете профиль, используя стратегию **text-dependent**, при применении которой нужно указывать парольную фразу как для регистрации, так и для проверки. Список поддерживаемых парольных фраз см. в [справочной документации](/rest/api/speakerrecognition/).

Для начала [создайте голосовой профиль](/rest/api/speakerrecognition/verification/textdependent/createprofile). Необходимо вставить ключ и регион подписки службы "Речь" в каждую из этих команд в этой статье.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Обратите внимание, что существует три типа речевого профиля:

- Текстозависимая проверка
- Текстонезависимая проверка
- Текстонезависимое распознавание

В этом случае вы создадите речевой профиль с зависимой от текста проверкой. Вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Затем вам нужно [зарегистрировать речевой профиль](/rest/api/speakerrecognition/verification/textdependent/createenrollment). В качестве значения параметра `--data-binary` укажите звуковой файл на компьютере, который содержит одну из поддерживаемых парольных фраз, например my voice is my passport, verify me (мой голос — мой паспорт, проверь меня). Вы можете записать такой звуковой файл с помощью приложения, например [Запись голоса Windows](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab), или создать его с помощью [преобразования текста в речь](../../../index-text-to-speech.yml).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Этот ответ указывает на то, что вам нужно зарегистрировать еще два примера звуковых данных.

После регистрации всех трех примеров звуковых данных вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Теперь вы готовы [сверить пример звуковых данных с речевым профилем](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Этот пример звуковых данных должен содержать ту же парольную фразу, что и примеры, использованные для регистрации речевого профиля.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept` означает, что парольная фраза сопоставлена и проверка прошла успешно. Ответ также содержит показатель подобия в диапазоне от 0,0 до 1,0.

Чтобы завершить, [удалите речевой профиль](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Нет ответа.

## <a name="text-independent-verification"></a>Проверка, не зависящая от текста

Проверка, **не зависящая от текста**, отличается от проверки, **зависящей от текста**, в следующих аспектах.

* Определенная парольная фраза не требуется, можно произносить что угодно.
* Не требуются три примера звуковых данных, но *требуется*, чтобы длительность звуковых данных составляла 20 секунд.

Начните с [создания профиля проверки, не зависящего от текста](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Затем [зарегистрируете речевой профиль](/rest/api/speakerrecognition/verification/textindependent/createenrollment). Опять же, вместо отправки трех примеров звуковых данных необходимо отправить только те примеры, длительность звуковых данных которых составляет 20 секунд.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Отправив достаточное количество примеров звуковых данных, вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Теперь вы готовы [сверить пример звуковых данных с речевым профилем](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Опять же, этот пример звуковых данных не обязательно должен содержать парольную фразу. Он может содержать любое речевое сопровождение, однако в целом длительность речи не может быть менее чем четыре секунды.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept` означает, что проверка прошла успешно. Ответ также содержит показатель подобия в диапазоне от 0,0 до 1,0.

Чтобы завершить, [удалите речевой профиль](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Нет ответа.

## <a name="speaker-identification"></a>Идентификация говорящего

Идентификация говорящего используется для определения, **кто** именно говорит из определенной группы зарегистрированных голосов. Этот процесс похож на проверку, **не зависящую от текста**. Основное отличие заключается в том, что можно сравнивать не с одним, а сразу с несколькими речевыми профилями.

Начните с [создания профиля идентификации, не зависящего от текста](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Затем вам нужно [зарегистрировать речевой профиль](/rest/api/speakerrecognition/identification/textindependent/createenrollment). Опять же, вам необходимо отправить примеры звуковых данных, длительность которых составляет 20 секунд. Эти примеры не обязательно должны содержать парольную фразу.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Отправив достаточное количество примеров звуковых данных, вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Теперь все готово к [определению примера звуковых данных с помощью речевого профиля](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). Команда определения принимает список возможных идентификаторов речевых профилей, разделенных запятыми. В этом случае вы просто передадите идентификатор созданного ранее речевого профиля. Однако при необходимости можно передать несколько идентификаторов речевого профиля, каждый из которых зарегистрирован с помощью примеров звуковых данных другого голоса.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Вы получите следующий ответ.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

Ответ содержит идентификатор речевого профиля, который наиболее полно соответствует отправленному примеру звуковых данных. Он также содержит список потенциальных речевых профилей, упорядоченных в порядке сходства.

Чтобы завершить, [удалите речевой профиль](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Нет ответа.