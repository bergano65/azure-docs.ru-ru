---
title: Краткое руководство. Определение языка текста с помощью API перевода текстов и Python
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как с помощью REST API перевода текстов и Python определить язык заданного текста.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 4400b466927253e6eb81cf2ec29994d0606beea6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176707"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-python"></a>Краткое руководство. Определение языка текста с помощью API перевода текстов и Python

Из этого краткого руководства вы узнаете, как с помощью REST API перевода текстов и Python определить язык заданного текста.

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом API перевода текстов. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Python 2.7.x или 3.x;
* ключ подписки Azure для API перевода текстов.

## <a name="create-a-project-and-import-required-modules"></a>Создание проекта и импорт обязательных модулей

Создайте проект Python, используя любую IDE или любой текстовый редактор. Затем скопируйте в файл проекта с именем `detect.py` этот фрагмент кода.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `pip install requests uuid`.

Первый комментарий сообщает вашему интерпретатору Python об использовании кодировки UTF-8. Затем требуемые модули импортируются для считывания ключа подписки из переменной среды, создания запроса HTTP, создания уникального идентификатора и обработки ответа JSON, возвращаемого с помощью API перевода текстов.

## <a name="set-the-subscription-key-base-url-and-path"></a>Задание ключа подписки, базового URL-адреса и пути

В этом примере будет предпринята попытка считать ключ подписки API перевода текстов из переменной среды `TRANSLATOR_TEXT_KEY`. Если вы не знакомы с переменными среды, можно задать `subscriptionKey` в виде строки и закомментировать условный оператор.

Скопируйте в проект следующий код:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

Конечная точка для перевода текстов задается как `base_url`. Параметр `path` задает маршрут `detect` и определяет, что нужно использовать версию 3 API.

>[!NOTE]
> Дополнительные сведения о конечных точках, маршрутах и параметрах запросов см. в руководстве по [определениям в API перевода текстов 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/detect?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Добавление заголовков

Самый простой способ выполнить проверку подлинности запроса — это передать ключ подписки как заголовок `Ocp-Apim-Subscription-Key`, который мы используем в этом примере. В качестве альтернативы вы можете обменять свой ключ подписки на маркер доступа и передать маркер доступа в виде заголовка `Authorization` для проверки своего запроса. Дополнительные сведения см. в разделе [Authenticate to the Speech API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) (Аутентификация в API речи).

Скопируйте в проект следующий фрагмент кода:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-detect-text-language"></a>Создание запроса на определение языка текста

Определите строки, в которых нужно определить язык.

```python
# You can pass more than one object in body.
body = [{
    'text': 'Salve, mondo!'
}]
```

Далее мы создадим запрос POST, используя модуль `requests`. Требуется три аргумента: сцепленный URL-адрес, заголовки запроса и текст запроса:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Вывод ответа

Последний шаг — вывести результаты. Этот фрагмент кода упорядочивает результаты, сортируя ключи, устанавливая отступы и объявляя разделители элементов и ключей.

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Сборка

Вот и все. Вы собрали простую программу, которая вызовет API перевода текстов и вернет ответ JSON. Теперь пришло время запустить ее.

```console
python detect.py
```

Если вы хотите сравнить свой код с нашей версией, см. [пример кода на сайте GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Пример ответа

Найдите сокращенное наименование страны в этом [списке языков](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support).

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы закодировали свой ключ подписки в программе, обязательно удалите его после завершения работы с этим кратким руководством.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Ознакомиться с примерами на Python на сайте GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>См. также

Узнайте, как с помощью API перевода текстов выполнять следующие задачи:

* [перевод текста](quickstart-python-translate.md);
* [транслитерация текста](quickstart-python-transliterate.md);
* [получение вариантов перевода](quickstart-python-dictionary.md);
* [получение списка поддерживаемых языков](quickstart-python-languages.md);
* [определение длины предложения на основе входных данных](quickstart-python-sentences.md).
