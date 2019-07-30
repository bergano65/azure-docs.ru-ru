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
ms.openlocfilehash: bb0e44f83e2101a7b21e7b7ec6fdc75974c6d6d8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333612"
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

Создайте новый скрипт Python с именем _ContentModeratorQS.py_ и добавьте следующий код, чтобы импортировать необходимые части пакета SDK. Модуль печати pretty включен, чтобы было удобнее читать ответ JSON.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>Инициализация переменных

Далее добавьте переменные для ключа подписки и URL-адреса конечной точки Content Moderator. Добавьте имя `CONTENT_MODERATOR_SUBSCRIPTION_KEY` в переменные среды, используя в качестве значения ключ подписки. Для базового URL-адреса добавьте `CONTENT_MODERATOR_ENDPOINT` в переменные среды, используя в качестве значения URL-адрес конкретного региона, например `https://westus.api.cognitive.microsoft.com`. Ключи бесплатной пробной подписки создаются в регионе **westus**.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

Строка многострочного текста из файла будет модерироваться. Включите файл [content_moderator_text_moderation.txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) в локальную корневую папку и добавьте имя файла в переменные:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Отправка запроса к службе модератора

Создайте экземпляр **ContentModeratorClient** с использованием нужных ключа подписки и URL-адреса конечной точки. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

Затем используйте клиент с экземпляром **TextModerationOperations** его члена для вызова API модерации с помощью функции `screen_text`. Дополнительные сведения о том, как вызывать его, см. в документации с описанием метода **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** .

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>Проверка напечатанного ответа

Запустите пример и подтвердите ответ. После успешного завершения будет получен экземпляр **Screen**. Успешный результат показан ниже:

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
