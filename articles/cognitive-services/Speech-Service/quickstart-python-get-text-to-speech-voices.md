---
title: Краткое руководство. Перечисление голосовых моделей для преобразований текста в речь с помощью Python — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как получить полный список стандартных и нейронных голосовых моделей для региона или конечной точки с использованием Python. Список возвращается как JSON, а доступность голосовой модели зависит от региона.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b20a8aabbd41c09efb6818cac2999a8c84b669fc
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816442"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Краткое руководство. Получение списка голосовых моделей для преобразований текста в речь с использованием Python

В этом кратком руководстве вы узнаете, как получить полный список стандартных и нейронных голосовых моделей для региона или конечной точки с использованием Python. Список возвращается как JSON, а доступность голосовой модели зависит от региона. Список поддерживаемых регионов см. в разделе [Регионы](regions.md).

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом службы "Речь". Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](get-started.md), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Python 2.7.x или 3.x;
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) или любой другой редактор кода;
* ключ подписки Azure для службы "Речь".

## <a name="create-a-project-and-import-required-modules"></a>Создание проекта и импорт обязательных модулей

Создайте проект Python, используя любую IDE или любой текстовый редактор. Затем скопируйте в файл проекта с именем `get-voices.py` этот фрагмент кода.

```python
import requests
```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `pip install requests`.

Запросы используются для HTTP-запросов к службе преобразования текста в речь.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Настройка ключа подписки и создание запроса для преобразования текста в речь

В следующих разделах описано, как создать методы для обработки авторизации, вызова API преобразования текста в речь и проверки ответа. Для начала давайте создадим класс. Именно в него мы поместим все методы для обмена маркерами и вызова API преобразования текста в речь.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Здесь `subscription_key` обозначает уникальный ключ, полученный на портале Azure.

## <a name="get-an-access-token"></a>Получение маркера доступа

Для этой конечной точки требуется предоставить маркер доступа для аутентификации. Чтобы получить маркер доступа, нужно выполнить обмен. Этот пример меняет ключ подписки службы "Речь" на маркер доступа через конечную точку `issueToken`.

В этом примере предполагается, что подписка службы "Речь" размещена в регионе "Западная часть США". Если вы используете другой регион, измените значение `fetch_token_url`. См. [полный список регионов](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

Здесь вы создадите запрос и сохраните список возвращенных голосовых моделей. Прежде всего задайте значения `base_url` и `path`. В этом примере предполагается, что вы используете конечную точку в регионе "Западная часть США". Если ресурс зарегистрирован в другом регионе, обновите значение `base_url`. Дополнительные сведения см. в [списке регионов службы "Речь"](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Теперь следует добавить в запрос обязательные заголовки. И наконец, направьте запрос в службу. Если запрос выполнен успешно, возвращается код состояния 200 и ответ сохраняется как файл.

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
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
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

Теперь все готово к запуску примера. В командной строке или сеансе терминала перейдите к каталогу проекта и выполните следующую команду:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите из исходного кода примера приложения все конфиденциальные сведения, например ключи подписки.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Ознакомиться с примерами на Python на сайте GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>См. также

* [Справочник по API преобразования текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Создание настраиваемого голоса](how-to-customize-voice-font.md)
* [Запись примеров голоса для создания пользовательских голосовых моделей](record-custom-voice-samples.md)
