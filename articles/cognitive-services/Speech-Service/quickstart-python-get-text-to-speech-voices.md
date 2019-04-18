---
title: Краткое руководство. Список преобразования текста в речь голоса, Python — службы распознавания речи
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как получить полный список "стандартный" и нейронной голоса для региона и конечную точку с помощью Python. Список возвращается в виде JSON и доступность голоса зависит от региона.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: erhopf
ms.openlocfilehash: 66bda68b1313a7c172e273671bc3a03503d08e0d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876586"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Краткое руководство. Получите список преобразования текста в речь голоса с помощью Python

В этом кратком руководстве вы узнаете, как получить полный список "стандартный" и нейронной голоса для региона и конечную точку с помощью Python. Список возвращается в виде JSON и доступность голоса зависит от региона. Список поддерживаемых регионов см. в разделе [регионах](regions.md).

В этом кратком руководстве требуется [учетной записи Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом служб речи. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](get-started.md), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Технические условия

Для работы с этим кратким руководством вам понадобится:

* Python 2.7.x или 3.x;
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) или любой другой редактор кода;
* Ключ подписки Azure для служб речи

## <a name="create-a-project-and-import-required-modules"></a>Создание проекта и импорт обязательных модулей

Создайте проект Python, используя любую IDE или любой текстовый редактор. Затем скопируйте в файл проекта с именем `get-voices.py` этот фрагмент кода.

```python
import requests
```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `pip install requests`.

Запросы используется для HTTP-запросов к службе преобразования текста в речь.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Настройка ключа подписки и создание запроса для преобразования текста в речь

В следующих разделах описано, как создать методы для обработки авторизации, вызова API преобразования текста в речь и проверки ответа. Давайте начнем с создания класса. Именно в него мы поместим все методы для обмена маркерами и вызова API преобразования текста в речь.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Здесь `subscription_key` обозначает уникальный ключ, полученный на портале Azure.

## <a name="get-an-access-token"></a>Получение маркера доступа

Эта конечная точка требуется маркер доступа для проверки подлинности. Чтобы получить маркер доступа, нужно выполнить обмен. Этот пример меняет местами ключ подписки служб речи для маркера доступа с помощью `issueToken` конечной точки.

В этом примере предполагается, что подписку служб речи находится в западной части США. Если вы используете другой регион, измените значение `fetch_token_url`. См. [полный список регионов](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Скопируйте следующий код в класс `GetVoices`:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Дополнительные сведения о проверке подлинности см. в разделе [Проверка подлинности с помощью маркера доступа](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Выполнение запроса и сохранение ответа

Здесь вы собираетесь создать запрос и сохранить список возвращенных голоса. Прежде всего задайте значения `base_url` и `path`. В этом примере предполагается, что вы используете конечную точку в регионе "Западная часть США". Если ресурс зарегистрирован в другом регионе, обновите значение `base_url`. Дополнительные сведения см. в разделе [области служб речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Затем добавьте необходимые заголовки для запроса. И наконец, направьте запрос в службу. Если запрос выполнен успешно, и возвращается код состояния 200, ответ записывается в файл.

Скопируйте следующий код в класс `GetVoices`:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Сборка

Осталось совсем немного. Последним шагом будет создание экземпляра класса и вызов функций.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Таким образом, вы будете готовы к запуску примера. В командной строке или сеансе терминала перейдите к каталогу проекта и выполните следующую команду:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите из исходного кода примера приложения все конфиденциальные сведения, например ключи подписки.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Ознакомиться с примерами на Python на сайте GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>См. также

* [Справочник по API преобразования текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Создание настраиваемого голоса](how-to-customize-voice-font.md)
* [Запись примеров голоса для создания пользовательских голосовых моделей](record-custom-voice-samples.md)
