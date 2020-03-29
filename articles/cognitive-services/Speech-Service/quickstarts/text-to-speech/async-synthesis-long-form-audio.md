---
title: 'Быстрый запуск: Асинхронный синтез для длинноформатного аудио (Preview) - Речевая служба'
titleSuffix: Azure Cognitive Services
description: Используйте Long Audio API для асинхронного преобразования текста в речь и получения аудиовыхода из URI, предоставляемого службой. Этот REST API идеально подходит для поставщиков контента, которым необходимо преобразовать текстовые файлы размером более 10 000 символов или 50 абзацев в синтезированную речь.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331082"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Быстрый запуск: Асинхронный синтез для длинноформатного звука в Python (Предварительный просмотр)

В этом quickstart вы будете использовать Long Audio API для асинхронного преобразования текста в речь и получения аудиовыхода из URI, предоставляемого службой. Этот REST API идеально подходит для контент-провайдеров, которым необходимо синтезировать звук из текста размером более 5000 символов (или более 10 минут в длину). Для получения дополнительной информации [см.](../../long-audio-api.md)

> [!NOTE]
> Асинхронный синтез для звуков в длинной форме может быть использован только с [пользовательскими нейронными голосами.](../../how-to-custom-voice.md#custom-neural-voices)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Python 2.7.x или 3.x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), или ваш любимый текстовый редактор.
* Подписка Azure и ключ подписки на речевой сервис. [Создайте учетную запись Azure](../../get-started.md#new-resource) и [создайте речевой ресурс](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) для получения ключа. При создании ресурса Speech убедитесь, что ваш ценовой уровень установлен на **S0,** а местоположение настроено на [поддерживаемый регион.](../../regions.md#standard-and-neural-voices)

## <a name="create-a-project-and-import-required-modules"></a>Создание проекта и импорт обязательных модулей

Создайте проект Python, используя любую IDE или любой текстовый редактор. Затем скопируйте фрагмент этого кода `voice_synthesis_client.py`в файл с именем.

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

> [!NOTE]
> Если вы еще не использовали эти модули, вам нужно установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `pip install requests urllib3`.

Эти модули используются для разбора аргументов, построения запроса HTTP и вызова долгого аудио-REST API между текстом к речи.

## <a name="get-a-list-of-supported-voices"></a>Получить список поддерживаемых голосов

Этот код получает список доступных голосов, которые можно использовать для преобразования текста в речи. Добавьте код `voice_synthesis_client.py`в:

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

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

### <a name="test-your-code"></a>Тестирование кода

Давайте проверим, что вы сделали до сих пор. Вам нужно обновить несколько вещей в запросе ниже:

* Замените `<your_key>` на ваш ключ подписки службы "Речь". Эта информация доступна во вкладке **«Обзор»** для вашего ресурса на [портале Azure](https://aka.ms/azureportal).
* Заменить `<region>` область, в которой был создан `eastus` ресурс `westus`Speech (например: или ). Эта информация доступна во вкладке **«Обзор»** для вашего ресурса на [портале Azure](https://aka.ms/azureportal).

Выполните следующую команду:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Вы увидите выход, который выглядит следующим образом:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Подготовка входных файлов

Подготовьте текстовый файл ввода. Это может быть либо простой текст, либо текст SSML. Для требований к файлу [prepare content for synthesis](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis)ввода см.

## <a name="convert-text-to-speech"></a>Преобразование текста в речь

После подготовки файла ввода текста, добавьте этот код для синтеза речи: `voice_synthesis_client.py`

> [!NOTE]
> 'concatenateResult' является необязательным параметром. Если этот параметр не установлен, аудиовыходы будут сгенерированы в пункте. Вы также можете совмещеть аудио в 1 выход, установив параметр. По умолчанию аудиовыход установлен на riff-16khz-16bit-моно-пкм. Для получения дополнительной информации о поддерживаемых аудио выходов, см [Аудио выходные форматы](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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

### <a name="test-your-code"></a>Тестирование кода

Давайте сделаем запрос на синтез текста с помощью файла ввода в качестве источника. Вам нужно обновить несколько вещей в запросе ниже:

* Замените `<your_key>` на ваш ключ подписки службы "Речь". Эта информация доступна во вкладке **«Обзор»** для вашего ресурса на [портале Azure](https://aka.ms/azureportal).
* Заменить `<region>` область, в которой был создан `eastus` ресурс `westus`Speech (например: или ). Эта информация доступна во вкладке **«Обзор»** для вашего ресурса на [портале Azure](https://aka.ms/azureportal).
* Замените `<input>` путь к текстовому файлу, который вы подготовили для текста к речи.
* Заменить `<locale>` нужным локалью вывода. Для получения дополнительной информации смотрите [языковую поддержку](../../language-support.md#neural-voices).
* Замените `<voice_guid>` нужным голосом вывода. Используйте один из голосов, возвращенных [Получить список поддерживаемых голосов](#get-a-list-of-supported-voices).

Преобразование текста в речь с помощью этой команды:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Если у вас есть более 1 входных файлов, вам нужно будет отправить несколько запросов. Есть некоторые ограничения, которые должны быть в курсе. 
> * Клиенту разрешается отправлять до **5** запросов на сервер в секунду для каждой учетной записи подписки Azure. Если он превышает ограничение, клиент получит код ошибки 429 (слишком много запросов). Пожалуйста, уменьшите сумму запроса в секунду
> * Сервер управляется к запуску и очереди до **120** запросов для каждой учетной записи подписки Azure. Если он превышает ограничение, сервер вернет код ошибки 429 (слишком много запросов). Пожалуйста, подождите и избегайте отправки нового запроса до тех пор, пока не будут выполнены некоторые запросы

Вы увидите выход, который выглядит следующим образом:

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

Результат содержит текст ввода и аудио выводфайлы, которые генерируются службой. Вы можете скачать эти файлы на молнии.

## <a name="remove-previous-requests"></a>Удаление предыдущих запросов

Сервер будет хранить до **20 000** запросов на каждую учетную запись подписки Azure. Если сумма запроса превышает это ограничение, пожалуйста, удалите предыдущие запросы, прежде чем делать новые. Если вы не удалите существующие запросы, вы получите уведомление об ошибке.

Добавьте код `voice_synthesis_client.py`в:

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

### <a name="test-your-code"></a>Тестирование кода

Теперь давайте проверим, какие запросы вы ранее отправили. Прежде чем продолжить работу, необходимо обновить несколько вещей в этом запросе:

* Замените `<your_key>` на ваш ключ подписки службы "Речь". Эта информация доступна во вкладке **«Обзор»** для вашего ресурса на [портале Azure](https://aka.ms/azureportal).
* Заменить `<region>` область, в которой был создан `eastus` ресурс `westus`Speech (например: или ). Эта информация доступна во вкладке **«Обзор»** для вашего ресурса на [портале Azure](https://aka.ms/azureportal).

Выполните следующую команду:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Это вернет список запросов синтеза, которые вы сделали. Вы увидите выход следующим образом:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Теперь удалим ранее представленный запрос. Вам нужно обновить несколько вещей в коде ниже:

* Замените `<your_key>` на ваш ключ подписки службы "Речь". Эта информация доступна во вкладке **«Обзор»** для вашего ресурса на [портале Azure](https://aka.ms/azureportal).
* Заменить `<region>` область, в которой был создан `eastus` ресурс `westus`Speech (например: или ). Эта информация доступна во вкладке **«Обзор»** для вашего ресурса на [портале Azure](https://aka.ms/azureportal).
* Заменить `<synthesis_id>` значение, возвращенное в предыдущем запросе.

> [!NOTE]
> Запросы со статусом 'Бегущий'/'Ожидание' не могут быть удалены или удалены.

Выполните следующую команду:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Вы увидите выход следующим образом:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Получить полный клиент

Завершено `voice_synthesis_client.py` доступно для скачивания на [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте больше о Длинном аудио-ам-инаке](../../long-audio-api.md)
