---
title: Длинный аудио API (Предварительная версия) — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как API длинного аудио-интерфейса предназначен для асинхронного синтеза длинного текста в речь.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 0a538deb3b7da19261e1bc2b7c0d29f35315f786
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015419"
---
# <a name="long-audio-api-preview"></a>Длинный аудио API (Предварительная версия)

Длинный аудио API предназначен для асинхронного синтеза текста длинной формы в речь (например, в аудио книгах, новостных статьях и документах). Этот API не возвращает синтезированный звук в режиме реального времени, а предполагает, что вы запрашиваете ответ и используете выходные данные, так как они становятся доступными из службы. В отличие от API преобразования текста в речь, используемого речевым пакетом SDK, длинный аудио API может создавать синтезированный звук дольше 10 минут, что делает его идеальным для издателей и платформ звуковых данных.

Дополнительные преимущества для длинного аудио API:

* Синтезированный речевой ввод, возвращенный службой, использует лучшие нейронные голоса.
* Нет необходимости развертывать голосовую конечную точку, так как она позволяет объединять голоса в режиме без реального времени.

> [!NOTE]
> Длинный аудио API теперь поддерживает как [открытые нейронные голоса](./language-support.md#neural-voices) , так и [пользовательские нейронные голоса](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Рабочий процесс

Как правило, при использовании длинного аудио API вы отправляете текстовый файл или файлы для синтезирования, опрашиваете состояние, а затем, если состояние прошло успешно, можно загрузить выходные данные звука.

На этой схеме представлен общий обзор рабочего процесса.

![Схема рабочего потока для длинного звука API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Подготовка содержимого для синтеза

При подготовке текстового файла убедитесь, что он:

* Является обычным текстом (txt) или SSML Text (. txt)
* Кодируется как [UTF-8 с меткой порядка байтов (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Является отдельным файлом, а не ZIP
* Содержит более 400 символов для обычного текста или 400 [оплачиваемых символов](./text-to-speech.md#pricing-note) для текста SSML и менее 10 000 абзацев
  * Для обычного текста каждый абзац отделяется нажатием клавиши **Ввод/возврат** -просмотр. [пример входного текста с открытым текстом](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Для текста SSML каждая часть SSML считается абзацом. SSML части должны быть разделены с помощью различных абзацев. [Пример текстового ввода](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) для просмотра SSML
> [!NOTE]
> В китайском (континентальная часть), китайском (Гонконг), китайском (Тайвань), японском и корейском языках одно слово будет считаться двумя символами. 

## <a name="python-example"></a>Пример на Python

В этом разделе содержатся примеры Python, демонстрирующие базовое использование длинного аудио API. Создайте проект Python, используя любую IDE или любой текстовый редактор. Затем скопируйте этот фрагмент кода в файл с именем `voice_synthesis_client.py` .

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

Эти библиотеки используются для анализа аргументов, формирования HTTP-запроса и вызова длинных аудио-REST API текста в речь.

### <a name="get-a-list-of-supported-voices"></a>Получение списка поддерживаемых голосов

Этот код позволяет получить полный список голосов для определенного региона или конечной точки, которые можно использовать. Добавьте код в `voice_synthesis_client.py` :

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

Выполните сценарий с помощью команды `python voice_synthesis_client.py --voices -key <your_key> -region <region>` и замените следующие значения:

* Замените `<your_key>` на ваш ключ подписки службы "Речь". Эти сведения доступны на вкладке **Обзор** ресурса в [портал Azure](https://aka.ms/azureportal).
* Замените на `<region>` регион, в котором был создан ваш речевой ресурс (например: `eastus` или `westus` ). Эти сведения доступны на вкладке **Обзор** ресурса в [портал Azure](https://aka.ms/azureportal).

Вы увидите выходные данные следующего вида:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Если параметр **публиквоице** имеет **значение true**, речь является общедоступным нейронным голоса. В противном случае это пользовательский нейронный счет.

### <a name="convert-text-to-speech"></a>Преобразование текста в речь

Подготовьте входной текстовый файл, как обычный текст, так и SSML текст, а затем добавьте следующий код в `voice_synthesis_client.py` :

> [!NOTE]
> "Конкатенатересулт" является необязательным параметром. Если этот параметр не задан, выходные данные будут формироваться для каждого абзаца. Вы также можете объединить звуковые данные в 1 выход, задав параметр. По умолчанию для вывода звука задано значение Metallica-16khz-16-разрядный-Mono-PCM. Дополнительные сведения о поддерживаемых звуковых выходах см. в разделе [форматы выходных данных звука](#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

Выполните сценарий с помощью команды `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` и замените следующие значения:

* Замените `<your_key>` на ваш ключ подписки службы "Речь". Эти сведения доступны на вкладке **Обзор** ресурса в [портал Azure](https://aka.ms/azureportal).
* Замените на `<region>` регион, в котором был создан ваш речевой ресурс (например: `eastus` или `westus` ). Эти сведения доступны на вкладке **Обзор** ресурса в [портал Azure](https://aka.ms/azureportal).
* Замените на `<input>` путь к текстовому файлу, подготовленному для преобразования текста в речь.
* Замените на `<locale>` нужный языковой стандарт вывода. Дополнительные сведения см. в разделе [Поддержка языков](language-support.md#neural-voices).
* Замените на `<voice_guid>` нужный выходной поток. Используйте один из голосов, возвращенных предыдущим вызовом к `/voicesynthesis/voices` конечной точке.

Вы увидите выходные данные следующего вида:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Результат содержит входной и аудиофайлный выходные файлы, создаваемые службой. Эти файлы можно загрузить в ZIP-файл.

> [!NOTE]
> При наличии более 1 входных файлов необходимо отправить несколько запросов. Существуют некоторые ограничения, которые необходимо учитывать. 
> * Клиент может отправлять до **5** запросов на сервер в секунду для каждой учетной записи подписки Azure. Если оно превышает ограничение, клиент получает код ошибки 429 (слишком много запросов). Сократите количество запросов в секунду.
> * Сервер может запуститься и поставить в очередь до **120** запросов для каждой учетной записи подписки Azure. Если оно превышает ограничение, сервер возвратит код ошибки 429 (слишком много запросов). Подождите и не отправляйте новый запрос, пока не будут завершены некоторые запросы

### <a name="remove-previous-requests"></a>Удалить предыдущие запросы

Служба будет обслуживать до **20 000** запросов для каждой учетной записи подписки Azure. Если сумма запроса превышает это ограничение, удалите предыдущие запросы перед созданием новых. Если не удалить существующие запросы, вы получите уведомление об ошибке.

Добавьте в `voice_synthesis_client.py` следующий код:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

Выполните команду, `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` чтобы получить список сделанных запросов синтеза. Вы увидите следующий результат:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Чтобы удалить запрос, выполните команду `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` и замените `<synthesis_id>` значением идентификатора запроса, возвращенным из предыдущего запроса.

> [!NOTE]
> Запросы с состоянием "работает" или "ожидание" не могут быть удалены или удалены.

Завершенный `voice_synthesis_client.py` доступ доступен на [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>Коды состояния HTTP

В следующей таблице описаны коды и сообщения HTTP-ответов из REST API.

| API | Код состояния HTTP | Описание | Решение |
|-----|------------------|-------------|----------|
| Создание | 400 | Синтез голоса не включен в этом регионе. | Измените ключ подписки на речь с поддерживаемым регионом. |
|        | 400 | Допустима только **Стандартная** подписка на речь для этого региона. | Измените ключ подписки на речь на ценовую категорию "Стандартный". |
|        | 400 | Превышает ограничение в 20 000 запросов для учетной записи Azure. Перед отправкой новых запросов удалите некоторые из них. | Сервер будет иметь до 20 000 запросов для каждой учетной записи Azure. Перед отправкой новых запросов удалите некоторые из них. |
|        | 400 | Эта модель не может использоваться в синтезе речи: {modelID}. | Убедитесь, что состояние {modelID} указано правильно. |
|        | 400 | Регион для запроса не соответствует региону для модели: {modelID}. | Убедитесь, что регион {modelID} соответствует региону запроса. |
|        | 400 | Синтез голоса поддерживает только текстовый файл в кодировке UTF-8 с маркером порядка следования байтов. | Убедитесь, что входные файлы находятся в кодировке UTF-8 с маркером порядка следования байтов. |
|        | 400 | В запросе синтеза голоса допускаются только допустимые входные данные SSML. | Убедитесь, что входные выражения SSML верны. |
|        | 400 | Голосовое имя {Воиценаме} не найдено во входном файле. | Имя входного голоса SSML не соответствует ИДЕНТИФИКАТОРу модели. |
|        | 400 | Число абзацев во входном файле должно быть меньше 10 000. | Убедитесь, что число абзацев в файле меньше 10 000. |
|        | 400 | Длина входного файла должна составлять более 400 символов. | Убедитесь, что входной файл содержит больше 400 символов. |
|        | 404 | Не удается найти модель, объявленную в определении синтеза голоса: {modelID}. | Проверьте правильность {modelID}. |
|        | 429 | Превышает ограничение активного синтеза речи. Дождитесь завершения некоторых запросов. | Сервер может запуститься и поставить в очередь до 120 запросов для каждой учетной записи Azure. Подождите и не отправляйте новые запросы, пока не будут завершены некоторые запросы. |
| Все       | 429 | Слишком много запросов. | Клиент может отправлять до 5 запросов на сервер в секунду для каждой учетной записи Azure. Сократите количество запросов в секунду. |
| Удалить    | 400 | Задача синтеза голоса по-прежнему используется. | Можно удалить только **Завершенные** или **Невыполненные** запросы. |
| жетбид   | 404 | Не удается найти указанную сущность. | Убедитесь, что идентификатор синтеза правильный. |

## <a name="regions-and-endpoints"></a>Регионы и конечные точки

Длинный аудио API доступен в нескольких регионах с уникальными конечными точками.

| Регион | Конечная точка |
|--------|----------|
| Восточная Австралия | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Центральная Канада | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| Восточная часть США | `https://eastus.customvoice.api.speech.microsoft.com` |
| Центральная Индия | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Центрально-южная часть США | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Юго-Восточная Азия | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| южная часть Соединенного Королевства | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Западная Европа | `https://westeurope.customvoice.api.speech.microsoft.com` |
| западная часть США 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Форматы вывода звука

Поддерживаются гибкие форматы вывода звука. Вы можете создавать выходные данные для каждого абзаца или объединять звуковые выходные данные в один выход, задав параметр "Конкатенатересулт". API длинного звука поддерживает следующие форматы вывода звука:

> [!NOTE]
> Формат аудио по умолчанию — Metallica-16khz-16-разрядный-Mono-PCM.

* Metallica-8khz-16-разрядный-Mono-PCM
* Metallica-16khz-16-разрядный-Mono-PCM
* Metallica-24khz-16-разрядный-Mono-PCM
* Metallica-48khz-16-разрядный-Mono-PCM
* Audio-16khz-32kbitrate-Mono-MP3
* Audio-16khz-64kbitrate-Mono-MP3
* Audio-16khz-128kbitrate-Mono-MP3
* Audio-24khz-48kbitrate-Mono-MP3
* Audio-24khz-96kbitrate-Mono-MP3
* Audio-24khz-160kbitrate-Mono-MP3

## <a name="sample-code"></a>Образец кода
Пример кода для длинного аудио API доступен на сайте GitHub.

* [Пример кода: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Пример кода: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Пример кода: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)