---
title: Краткое руководство. Получение списка поддерживаемых языков с помощью Python и API перевода текстов
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как получить список языков, для которых поддерживается перевод, транслитерация и поиск по словарю, с помощью API перевода текстов и Python.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 74577b0a589ed0b717f8685704b92672f108939b
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606320"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>Краткое руководство. Получение списка поддерживаемых языков с помощью Python и API перевода текстов

Из этого краткого руководства вы узнаете, как отправить запрос GET, который возвращает список поддерживаемых языков, с помощью Python и REST API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Python 2.7.x или 3.x;

## <a name="create-a-project-and-import-required-modules"></a>Создание проекта и импорт обязательных модулей

Создайте проект Python, используя любую IDE или любой текстовый редактор. Затем скопируйте в файл проекта с именем `get-languages.py` этот фрагмент кода.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `pip install requests uuid`.

Первый комментарий сообщает вашему интерпретатору Python об использовании кодировки UTF-8. Затем требуемые модули импортируются для считывания ключа подписки из переменной среды, создания запроса HTTP, создания уникального идентификатора и обработки ответа JSON, возвращаемого с помощью API перевода текстов.

## <a name="set-the-base-url-and-path"></a>Задание базового URL-адреса и пути

Конечная точка для перевода текстов задается как `base_url`. Параметр `path` задает маршрут `languages` и определяет, что нужно использовать версию 3 API.

>[!NOTE]
> Дополнительные сведения о конечных точках, маршрутах и параметрах запросов см. в статье [API перевода текстов 3.0: API перевода текстов 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Добавление заголовков

Запрос на получение сведений о поддерживаемых языках не требует проверки подлинности. Задайте `Content-type` как `application/json` и добавьте `X-ClientTraceId` для уникальной идентификации вашего запроса.

Скопируйте в проект следующий фрагмент кода:

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Создание запроса на получение списка поддерживаемых языков

Давайте создадим запрос GET, используя модуль `requests`. Он принимает два аргумента: сцепленный URL-адрес и заголовки запроса.

```python
request = requests.get(constructed_url, headers=headers)
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
python get-languages.py
```

Если вы хотите сравнить свой код с нашей версией, см. [пример кода на сайте GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Пример ответа

Найдите сокращенное наименование страны или региона в этом [списке языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Этот пример мы сократили, чтобы показать фрагмент результата:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
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
* [определение языка по входным данным](quickstart-python-detect.md);
* [получение вариантов перевода](quickstart-python-dictionary.md);
* [определение длины предложения на основе входных данных](quickstart-python-sentences.md).
