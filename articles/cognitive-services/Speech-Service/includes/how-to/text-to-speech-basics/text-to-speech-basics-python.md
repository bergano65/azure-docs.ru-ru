---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: be60a2f371148fabf73fc7fcdce114295775d71c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985989"
---
## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на службу "Речь". Если у вас нет учетной записи и подписки, [попробуйте службу "Речь" бесплатно](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Прежде чем выполнять какие-либо действия, необходимо установить пакет SDK для службы "Речь".

```Python
pip install azure-cognitiveservices-speech
```

Если вы работаете в macOS и сталкиваетесь с проблемами при установке, вам может понадобиться сначала запустить эту команду.

```Python
python3 -m pip install --upgrade pip
```

После установки пакета SDK добавьте в начале скрипта следующие инструкции импорта.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Создание конфигурации службы "Речь"

Чтобы вызвать службу "Речь" с помощью пакета SDK для службы "Речь", необходимо создать [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Этот класс содержит сведения о вашей подписке, такие как ключ и связанный регион, конечная точка, узел или маркер авторизации.

> [!NOTE]
> Независимо от того, используете ли вы распознавание речи, синтез речи, перевод или распознавание намерения, вы всегда создаете конфигурацию.

Существует несколько способов инициализации [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python).

* С помощью подписки: передайте ключ и связанный с ним регион.
* С помощью конечной точки: передайте конечную точку службы "Речь". Ключ или маркер авторизации являются необязательными.
* С помощью узла: передайте адрес узла. Ключ или маркер авторизации являются необязательными.
* С помощью маркера авторизации: передайте маркер авторизации и связанный регион.

В этом примере создается [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) с помощью ключа и региона подписки. Идентификатор региона можно узнать на [этой странице](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk).

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Синтезирование речи в файл

Затем вы создадите объект [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python), который выполняет преобразование текста в речь и выводит ее в динамики, файлы или другие потоки вывода. В качестве параметров объект [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) принимает созданный на предыдущем шаге объект [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) и объект [`AudioOutputConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python), определяющий, как должны обрабатываться выходные результаты.

Сначала создайте `AudioOutputConfig` для автоматической записи выходных данных в файл `.wav` с помощью параметра конструктора `filename`.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Затем создайте экземпляр `SpeechSynthesizer`, передав объект `speech_config` и объект `audio_config` в качестве параметров. После этого синтез речи и запись в файл выполняются так же просто, как запуск `speak_text_async()` с помощью строки текста.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Запустите программу, после чего синтезированный файл `.wav` будет записан в указанное вами расположение. Это хороший пример базового способа использования. Далее рассматривается настройка выходных данных и обработка выходного ответа в виде потока в памяти для работы в пользовательских сценариях.

## <a name="synthesize-to-speaker-output"></a>Синтезирование выходных данных для вывода в динамики

В некоторых случаях может потребоваться напрямую выводить синтезированную речь в динамики. Для этого используйте пример из предыдущего раздела, но измените `AudioOutputConfig`, удалив параметр `filename` и задав `use_default_speaker=True`. Это позволит передавать выходные данные на активное устройство вывода.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Получение результата в виде потока в памяти

Во многих сценариях разработки речевых приложений требуется получить звуковые данные в виде потока в памяти, а не в виде записи в файл. Это позволяет выполнять разные действия с выходными данными, в частности:

* Извлекать полученный массив байтов в виде потока с возможностью поиска для настраиваемых нижестоящих служб.
* Интегрировать полученные данные с другими API или службами.
* Изменять звуковые данные, записывать пользовательские заголовки `.wav` и т. д.

Это изменение легко реализовать в предыдущем примере. Сначала удалите `AudioConfig`, так как теперь вы будете вручную управлять выходными данными, чтобы получить более полный контроль. Затем передайте `None` для `AudioConfig` в конструкторе `SpeechSynthesizer`. 

> [!NOTE]
> Передача значения `None` для `AudioConfig`, а не его пропуск, как это сделано в приведенном выше примере с выводом в динамики, не позволит по умолчанию воспроизводить звук на активном устройстве вывода.

В этом случае результат сохраняется в переменной [`SpeechSynthesisResult`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?view=azure-python). Свойство `audio_data` содержит объект `bytes` выходных данных. Вы можете работать с этим объектом вручную или использовать класс [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) для управления потоком в памяти. В этом примере используется конструктор `AudioDataStream` для получения потока из результата.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Теперь можно настроить выполнение любых пользовательских действий, используя полученный объект `stream`.

## <a name="customize-audio-format"></a>Настройка формата аудио

В этом разделе показано, как задать атрибуты вывода звука, в том числе:

* тип звукового файла;
* частоту выборки;
* глубину в битах.

Чтобы изменить звуковой формат, используйте функцию `set_speech_synthesis_output_format()` для объекта `SpeechConfig`. Эта функция получает `enum` типа [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python) для выбора формата выходных данных. Список доступных звуковых форматов см. в [справочной документации](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python).

Для разных типов файлов предусмотрены разные возможности в зависимости от требований. Обратите внимание, что по определению форматы необработанных аудиоданных, такие как `Raw24Khz16BitMonoPcm`, не содержат заголовков. Используйте такие форматы, только если вы уверены, что нижестоящие службы способны декодировать необработанный битовый поток, или если вы планируете создавать заголовки вручную на основе глубины в битах, частоты выборки, количества каналов и т. д.

В этом примере мы выберем формат RIFF высокой точности воспроизведения `Riff24Khz16BitMonoPcm`, задав `SpeechSynthesisOutputFormat` для объекта `SpeechConfig`. Как и в примере из предыдущего раздела, мы будем использовать [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) для получения результата в виде потока в памяти, а затем записывать его в файл.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

При повторном запуске программы будет создан файл `.wav` в указанном расположении.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Настройка характеристик речи с помощью SSML

Язык разметки синтеза речи (SSML) позволяет точно настраивать тон, произношение, скорость речи, громкость и другие выходные данные при преобразовании текста в речь, отправляя запросы из схемы XML. В этом разделе показано несколько примеров использования этого языка, но, чтобы получить более полное представление о нем, прочтите [эту статью](../../../speech-synthesis-markup.md).

Чтобы освоить настройку с помощью SSML, для начала внесем простое изменение, которое позволяет менять голос.
Сначала создайте XML-файл для конфигурации SSML в корневом каталоге проекта. В нашем примере это `ssml.xml`. Корневым элементом всегда является `<speak>`, а перенос текста в элемент `<voice>` позволяет менять голос с помощью параметра `name`. В этом примере мы изменим голос на мужскую английскую речь (британский английский). Обратите внимание, что этот голос является **стандартным**, стоимость и доступность которого отличаются от аналогичных параметров голосов, **синтезированных с помощью нейронных сетей**. Ознакомьтесь с [полным списком](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) поддерживаемых **стандартных** голосов.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Далее необходимо изменить запрос синтеза речи, чтобы он ссылался на XML-файл. Запрос аналогичен использовавшемуся нами ранее, но вместо функции `speak_text_async()` содержит `speak_ssml_async()`. Эта функция принимает строку XML, поэтому сначала нужно передать конфигурацию SSML в виде строки. Теперь мы получим точно такой же объект, как и в предыдущих примерах.

> [!NOTE]
> Если `ssml_string` содержит `ï»¿` в начале строки, необходимо удалить метку порядка байтов, иначе служба вернет ошибку. Это можно сделать, задав параметру `encoding` такие параметры: `open("ssml.xml", "r", encoding="utf-8-sig")`.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Выходные данные поступают, но можно внести несколько простых изменений, чтобы добиться более естественного звучания. Общая скорость речи является несколько высокой, поэтому мы добавим тег `<prosody>`, чтобы снизить ее до **90 %** от заданной по умолчанию. Кроме того, пауза после запятой в предложении звучит коротковато и неестественно. Чтобы исправить это, добавьте тег `<break>`, что замедлит речь, а параметру времени задайте значение **200 мс**. Повторно запустите синтез, чтобы услышать, как эти настройки повлияли на выходные данные.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Синтезирование голоса с помощью нейронных сетей

Синтезированные с помощью нейронных сетей голоса — это алгоритмы синтеза речи на основе глубоких нейронных сетей. Во время использования нейронных голосовых моделей синтезированная речь почти ничем не отличается от записей человеческого голоса. Эти модели отличаются человекоподобной естественной интонацией и четким произношение слов, что значительно уменьшает усталость от прослушивания при взаимодействии с системами искусственного интеллекта.

Чтобы переключиться на такой голос, замените `name` одним из [доступных вариантов](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Затем добавьте пространство имен XML для `mstts` и заключите текст в тег `<mstts:express-as>`. Используйте параметр `style`, чтобы настроить стиль речи. В этом примере используется `cheerful`, но можно выполнить замену на `customerservice` или `chat`, чтобы оценить различия разговорного стиля.

> [!IMPORTANT]
> Голоса, синтезированные с помощью нейронных сетей, поддерживаются **только** речевыми ресурсами, созданными в регионах *Восточная часть США*, *Юго-Восточная Азия* и *Западная Европа*.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
