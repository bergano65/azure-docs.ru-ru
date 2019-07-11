---
title: Краткое руководство. Проверка содержимого текста с помощью Python в Content Moderator
titlesuffix: Azure Cognitive Services
description: Как анализировать содержимое текста на наличие различных нежелательных материалов с помощью пакета SDK Content Moderator для Python
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 0fef3bffd30c19d0313e5fce7eb610ae7f6349f5
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606992"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Краткое руководство. Анализ текста для выявления нежелательного содержимого с помощью Python

В этой статье содержатся сведения и примеры кода, которые помогут вам приступить к работе с пакетом SDK Content Moderator для Python. Вы узнаете, как выполнять фильтрацию по терминам и классифицировать содержимое текста с целью ограничения потенциально нежелательного материала.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования
- Ключ подписки Content Moderator. Следуйте инструкциям в статье [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ.
- [Python 2.7 или 3.5+](https://www.python.org/downloads/).
- Средство [PIP](https://pip.pypa.io/en/stable/installing/).

## <a name="get-the-content-moderator-sdk"></a>Получение пакета SDK Content Moderator

Установите пакет SDK Content Moderator для Python, открыв командную строку и выполнив следующую команду:

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Импорт модулей

Создайте новый скрипт Python с именем _ContentModeratorQS.py_ и добавьте следующий код, чтобы импортировать необходимые части пакета SDK.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=1-10)]

Можно также импортировать функцию "Качественная печать" для обработки окончательных выходных данных.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=12)]


## <a name="initialize-variables"></a>Инициализация переменных

Далее добавьте переменные для ключа подписки и URL-адреса конечной точки Content Moderator. Замените `<your subscription key>` фактическим значением ключа. Вам также может потребоваться изменить значение `endpoint_url`, чтобы использовать идентификатор региона, соответствующий ключу подписки. Ключи бесплатной пробной подписки создаются в регионе **westus**.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=14-16)]


Для простоты проанализируйте текст непосредственно из скрипта. Определите новую строку текстового содержимого для модерации:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=18-21)]


## <a name="query-the-moderator-service"></a>Отправка запроса к службе модератора

Создайте экземпляр **ContentModeratorClient** с использованием нужных ключа подписки и URL-адреса конечной точки. Затем используйте экземпляр **TextModerationOperations** (член предыдущего экземпляра) для вызова API модерации. Дополнительные сведения о том, как вызывать его, см. в документации с описанием метода **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** .

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=23-36)]

## <a name="print-the-response"></a>Вывод ответа

Наконец, проверьте, что вызов успешно завершен и возвращен экземпляр **Screen**. Далее выведите возвращенные данные на консоль.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=38-39)]


Пример текста, используемый в этом кратком руководстве, дает следующий результат:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы разработали простой скрипт Python, который использует службу Content Moderator для возврата релевантной информации о предоставленном примере текста. Затем узнайте больше о том, что означают разные флаги и классификации, чтобы решить, какие данные вам нужны и как ваше приложение должно их обрабатывать.

> [!div class="nextstepaction"]
> [Руководство по модерации текста](text-moderation-api.md)
