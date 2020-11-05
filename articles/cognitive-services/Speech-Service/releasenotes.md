---
title: Заметки о выпуске — служба речи
titleSuffix: Azure Cognitive Services
description: Выполняемый журнал выпусков функций речевой службы, улучшений, исправлений ошибок и известных проблем.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 01c9cbe0438ee0efeece4c7e6b17e9607db4c4cc
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356694"
---
# <a name="speech-service-release-notes"></a>Заметки о выпуске

## <a name="speech-sdk-1140-2020-october-release"></a>Пакет SDK для распознавания речи 1.14.0:2020 — выпуск от октября

**Примечание**. РЕЧЕВОЙ пакет SDK в Windows зависит от общего Microsoft Visual C++ распространяемого пакета для Visual Studio 2015, 2017 и 2019. Его можно скачать [здесь](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Новые функции**
- **Linux** : добавлена поддержка Debian 10 и Ubuntu 20,04 LTS.
- **Python/объектив-C** : добавлена поддержка `KeywordRecognizer` API. Документация будет [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-basics).
- **C++/Жава/к #** : добавлена поддержка для установки любых `HttpHeader` ключей и значений через `ServicePropertyChannel::HttpHeader` .
- **JavaScript** : добавлена поддержка `ConversationTranscriber` API. Ознакомьтесь с документацией [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-conversation-transcription?pivots=programming-language-javascript). 
- **C++/c #** : добавлен новый `AudioDataStream FromWavFileInput` метод (для чтения. Файлы WAV) [здесь (C++)](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) и [здесь (C#)](
https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet).
-  **C++/c #/Жава/Писон/обжективе-к/Свифт** : добавлен `stopSpeakingAsync()` метод для отмены синтеза текста в речь. Прочитайте справочную документацию [здесь (C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace)), здесь ( [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet)), здесь ( [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech?view=azure-java-stable)), здесь (на языке [Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech?view=azure-python)и [здесь (цель-C/SWIFT)](https://docs.microsoft.com/objectivec/cognitive-services/speech/).
- **C#, C++, Java** : в `FromDialogServiceConnector()` класс добавлена функция `Connection` , которая может использоваться для наблюдения за подключениями и событиями отключения `DialogServiceConnector` . Ознакомьтесь с справочной документацией [здесь (C#)](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection?view=azure-dotnet), [здесь (C++)](https://docs.microsoft.com/cpp/cognitive-services/speech/connection)и [здесь (Java)](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.connection?view=azure-java-stable).
<!-- - **C++/C#/Java/Python/Objective-C/Swift**: Added support for Pronunciation Assessment, which evaluates speech pronunciation and gives speakers feedback on the accuracy and fluency of spoken audio. Read the documentation [here](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-pronunciation-assessment). -->

**Критическое изменение**
- **JavaScript** : пуллаудиуутпутстреам. Read () имеет тип возврата из внутреннего обещания в собственное обещание JavaScript.

**Исправления ошибок**
- **Все** : Исправлена регрессия 1,13 в `SetServiceProperty` местах, где значения с определенными специальными символами были пропущены.
- **C#** : Исправлены примеры консоли Windows в Visual Studio 2019, которые не могут найти собственные библиотеки DLL.
- **C#** : фиксированный сбой с управлением памятью, если поток используется в качестве `KeywordRecognizer` входных данных.
- **ObjectiveC/SWIFT** : устранено аварийное завершение работы с управлением памятью, если поток используется в качестве входных данных распознавателя.
- **Windows** : Исправлена проблема совместного существования с BT ХФП/A2DP в UWP.
- **JavaScript** : Исправлено сопоставление идентификаторов сеансов для улучшения ведения журнала и поддержки внутренних взаимосвязей отладки и служб.
- **JavaScript** : Добавлено исправление для `DialogServiceConnector` отключения `ListenOnce` вызовов после первого вызова.
- **JavaScript** : Исправлена проблема, при которой выходные данные будут иметь значение "Simple".
- **JavaScript** : Исправлена проблема непрерывного распознавания в Safari на macOS.
- **JavaScript** : устранение нагрузки ЦП для сценария высокой пропускной способности запросов.
- **JavaScript** : разрешение доступа к сведениям о результатах регистрации речевого профиля.
- **JavaScript** : Добавлено исправление для постоянного распознавания в `IntentRecognizer` .
- **C++/c #/Жава/Писон/Свифт/обжективек** : исправлен неверный URL-адрес для australiaeast и бразилсаус в `IntentRecognizer` .
- **C++/c #** : добавляется `VoiceProfileType` в качестве аргумента при создании `VoiceProfile` объекта.
- **C++/c #/Жава/Писон/Свифт/обжективек** : фиксированный потенциал `SPX_INVALID_ARG` при попытке чтения `AudioDataStream` из заданной должности.
- **IOS** : Исправлена аварийное завершение работы с распознаванием речи в Unity

**Примеры**
- **ObjectiveC** : Добавлен пример для распознавания ключевого слова [здесь](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples).
- **C#/жаваскрипт** : Добавлено краткое руководство по транскрипции беседы [(c#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) и [здесь (JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription).
<!-- - **C++/C#/Java/Python/Swift/ObjectiveC**: Added sample for pronunciation assessment [here](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples) -->
- **Xamarin** : обновлен краткий руководства по последнему [шаблону](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin)Visual Studio.

**Известная проблема**
- DigiCert глобальный корневой сертификат G2 не поддерживается по умолчанию в HoloLens 2 и Android 4,4 (KitKat) и должен быть добавлен в систему, чтобы обеспечить работоспособность пакета SDK для распознавания речи. Сертификат будет добавлен в образы ОС HoloLens 2 в ближайшем будущем. Пользователям Android 4,4 необходимо добавить обновленный сертификат в систему.

**Ковид-19 краткие тестирование:** Благодаря удаленной работе за последние несколько недель мы не смогли выполнить проверку вручную, как и обычно. Мы не внесли изменения, которые могли бы привести к нарушению, и наши автоматические тесты прошли успешно. В маловероятном случае, если мы пропустили что-нибудь, сообщите нам об этом на [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Оставайтесь в курсе событий!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Речевой интерфейс командной строки (также известный как SPX): 2020 — выпуск от октября
SPX — это интерфейс командной строки для использования службы распознавания речи Azure без написания кода. Скачайте последнюю версию [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics). <br>

**Новые функции**
- `spx csr dataset upload --kind audio|language|acoustic` — Создание наборов данных из локальных, а не только URL-адресов.
- `spx csr evaluation create|status|list|update|delete` — Сравнение новых моделей с базовыми показателями истинности или другими моделями.
- `spx * list` — поддерживает нестраничный интерфейс (не требуется--Top X--Skip X).
- `spx * --http header A=B` — Поддержка пользовательских заголовков (добавленных для Office для пользовательской проверки подлинности). 
- `spx help` — Улучшенный цвет текста с текстом и обратными импульсами (синий).


## <a name="text-to-speech-2020-august-release"></a>Преобразование текста в речь 2020-выпуск за Август

### <a name="new-features"></a>новые функции;

* **Нейронный TTS: новый стиль речи для `en-US` Aria Voice**. При чтении новостей Арианеурал может показаться новостной. Стиль "невскаст-formal" звучит более серьезным, а стиль "невскаст-необычный" является более нестрогим и неформальным. См. раздел [Использование стилей произнесений в SSML](speech-synthesis-markup.md).

* **Настраиваемый Voice: новая функция выпущена для автоматической проверки качества данных для обучения**. При передаче данных система проверяет различные аспекты данных аудио и транскрипции и автоматически устраняет или отфильтровывает проблемы, чтобы улучшить качество голосовой модели. Это касается громкости звука, уровня шума, точности произношения речи, выравнивания речи с нормализованным текстом, тишины в аудио, а также формата аудио и сценария. 

* **Создание звукового содержимого. набор новых функций, обеспечивающих более широкие возможности голосовой настройки и управления звуком**.

    * Произношение: функция настройки произношения обновляется до последнего набора phoneme. Вы можете выбрать нужный элемент phoneme из библиотеки и уточнить произношение выбранных слов. 

    * Загрузка. функция загрузки и экспорта звука улучшена для поддержки создания звука по абзацам. Вы можете изменять содержимое в том же файле или SSML, создавая несколько звуковых выходов. Файловая структура "Download" также уточнена. Теперь вы можете легко получить все звуковые файлы в одной папке. 

    * Состояние задачи. Улучшен процесс экспорта в несколько файлов. При экспорте нескольких файлов в прошлом, если произошел сбой одного из файлов, произойдет сбой всей задачи. Но теперь все остальные файлы будут успешно экспортированы. Отчет о задачах дополняется более подробными и структурированными сведениями. Вы можете проверить журналы на наличие в отчете всех файлов с ошибками и предложений. 

    * Документация по SSML: связанная с документом SSML, помогающая проверить правила использования всех функций настройки.

* **API списка голосов обновляется для включения понятного отображаемого имени и стилей речи, поддерживаемых для нейронных голосов**.

### <a name="general-tts-voice-quality-improvements"></a>Усовершенствования общего качества голоса TTS

* Сокращена ошибка произношения уровня слова% для `ru-RU` (ошибок, уменьшенных на 56%) и `sv-SE` (ошибки уменьшаются на 49%)

* Улучшено поддельное чтение слов `en-US` нейронных голосов на 40%. Примеры слов с предельным предельным числом: "чтение", "динамический", "содержимое", "запись", "объект" и т. д. 

* Повышена естественнка вопроса в `fr-FR` . МОС (среднее мнение) прибыль: + 0,28

* Обновлен вокодерс для следующих голосов с улучшенными качествами и общей скоростью производительности на 40%.

    | Языковой стандарт | Голосовая связь |
    |---|---|    
    | `en-GB` | Миа |
    | `es-MX` | далиа |
    | `fr-CA` | силвие |
    | `fr-FR` | Дэнис |
    | `ja-JP` | нанами |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Исправления ошибок

* Исправлен ряд ошибок с помощью средства создания звукового содержимого. 
    * Исправлена проблема с автоматическое обновлением. 
    * Исправлены проблемы со стилями голоса в параметре zh-CN в Южной Восточная Азия регионе.
    * Исправлена проблема стабильности, включая ошибку экспорта с тегом break и ошибки в пунктуации.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Новые языки перевода речи в текст: 2020 — выпуск от августа
Преобразование речи в текст 26 новых национальных стандартов в августе: 2 европейских языка `cs-CZ` и `hu-HU` , 5 национальных стандартов и 19 испанских языков, охватывающие большинство стран Южной Америки. Ниже приведен список новых языковых стандартов. См [. полный](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support)список языков.

| Локаль  | Язык                          |
|---------|-----------------------------------|
| `cs-CZ` | Чешский (Чешская Республика)            | 
| `en-HK` | Английский (Гонконг)               | 
| `en-IE` | Английский (Ирландия)                 | 
| `en-PH` | Английский (Филиппины)             | 
| `en-SG` | Английский (Сингапур)               | 
| `en-ZA` | Английский (Южная Африка)            | 
| `es-AR` | Испанский (Аргентина)               | 
| `es-BO` | Испанский (Боливия)                 | 
| `es-CL` | Испанский (Чили)                   | 
| `es-CO` | Испанский (Колумбия)                | 
| `es-CR` | Испанский (Коста-Рика)              | 
| `es-CU` | Испанский (Куба)                    | 
| `es-DO` | Испанский (Доминиканская Республика)      | 
| `es-EC` | Испанский (Эквадор)                 | 
| `es-GT` | Испанский (Гватемала)               | 
| `es-HN` | Испанский (Гондурас)                | 
| `es-NI` | Испанский (Никарагуа)               | 
| `es-PA` | Испанский (Панама)                  | 
| `es-PE` | Испанский (Перу)                    | 
| `es-PR` | Испанский (Пуэрто-Рико)             | 
| `es-PY` | Испанский (Парагвай)                | 
| `es-SV` | Испанский (Эль-Сальвадор)             | 
| `es-US` | Испанский (США)                     | 
| `es-UY` | Испанский (Уругвай)                 | 
| `es-VE` | Испанский (Венесуэла)               | 
| `hu-HU` | Венгерский (Венгрия)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Пакет SDK для распознавания речи 1.13.0:2020 — июльская версия

**Примечание**. РЕЧЕВОЙ пакет SDK в Windows зависит от общего Microsoft Visual C++ распространяемого пакета для Visual Studio 2015, 2017 и 2019. Скачайте и установите приложение [отсюда](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Новые функции**
- **C#** : добавлена поддержка асинхронной записи диалога. См. документацию [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-async-conversation-transcription).  
- **JavaScript** : добавлена поддержка распознавание говорящего как для [браузера](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) , так и для [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **JavaScript** : добавлена поддержка автоматического определения языка и идентификатора языка. См. документацию [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-javascript).
- **Цель-C** : добавлена поддержка обмена [беседами](https://docs.microsoft.com/azure/cognitive-services/speech-service/multi-device-conversation) [и разговора](https://docs.microsoft.com/azure/cognitive-services/speech-service/conversation-transcription)для нескольких устройств. 
- **Python** : добавлена поддержка сжатого звука для Python в Windows и Linux. См. документацию [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). 

**Исправления ошибок**
- **Все** : Исправлена проблема, из-за которой кэйвордрекогнизер не переместит потоки после распознавания.
- **Все** : Исправлена проблема, из-за которой поток, полученный из кэйвордрекогнитионресулт, не содержал ключевое слово.
- **Все** : Исправлена проблема, при которой сендмессажеасинк не отправляет сообщение по сети, после того как пользователи завершат его ожидание.
- **Все** : устранение сбоя в распознавание говорящего API, когда пользователи вызывают метод воицепрофилеклиент:: спеакерреценроллпрофилеасинк несколько раз и не ожидают завершения вызовов.
- **Все** : исправлено включение ведения журнала файлов в классах Воицепрофилеклиент и спеакеррекогнизер.
- **JavaScript** : Исправлена [проблема](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) с регулированием при сворачивании браузера.
- **JavaScript** : Исправлена [проблема](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) с утечкой памяти в потоках.
- **JavaScript** : Добавлено кэширование для ответов OCSP из NodeJS.
- **Java** : Исправлена проблема, которая привела к тому, что BigInteger поля всегда возвращали 0.
- **iOS** : Исправлена [проблема](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) с публикацией приложений на основе речевого пакета SDK в магазине приложений iOS.

**Примеры**
- **C++** : Добавлен пример кода для распознавание говорящего [здесь](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**Ковид-19 краткие тестирование:** Благодаря удаленной работе за последние несколько недель мы не смогли выполнить проверку вручную, как и обычно. Мы не внесли изменения, которые могли бы привести к нарушению, и наши автоматические тесты прошли успешно. В маловероятном случае, если мы пропустили что-нибудь, сообщите нам об этом на [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Оставайтесь в курсе событий!

## <a name="text-to-speech-2020-july-release"></a>Выпуски преобразования текста в речь 2020 — Июль

### <a name="new-features"></a>новые функции;

* **Нейронный TTS, 15 новых нейронных голосов**. новые голоса, добавленные в портфель НЕЙРОНного TTS, Салма на `ar-EG` арабском языке (Египет), зарийах на `ar-SA` арабском языке (Саудовская Аравия), Алба в `ca-ES` каталанского (Испания), Чристел в `da-DK` датском (Дания), ниржа на `es-IN` английском языке (Индия), Нура в `fi-FI` Финляндии (Финляндия), свара в `hi-IN` хинди (Индия), колетте в нидерландском (Нидерланды), зофиа в польском (Польша), Фернанда на португальском языке (Португалия), дарийа в России (Россия), Хиллеви в `nl-NL` Швеции ( `pl-PL` Швеция), Ачара `pt-PT` `ru-RU` `sv-SE` в `th-TH` тайском (Таиланд), HiuGaai в `zh-HK` китайском (кантонский, традиционном) и HsiaoYu в `zh-TW` китайском (тайваньский мандаринский диалект). Проверьте все [Поддерживаемые языки](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices).  

* **Пользовательское голосовое и упрощенное тестирование речи с помощью потока обучения для упрощения взаимодействия с пользователем**. Благодаря новой функции тестирования каждый речевой процесс будет автоматически протестирован с помощью предопределенного набора тестов, оптимизированного для каждого языка, чтобы охватить общие и речевые сценарии. Эти наборы тестов тщательно выбираются и тестируются для включения типовых вариантов использования и фонемы на языке. Кроме того, пользователи по-прежнему могут выбрать отправку собственных сценариев тестирования при обучении модели.

* **Создание звукового содержимого. выпущен набор новых функций, обеспечивающих более широкие возможности голосовой настройки и управления звуком.**

    * `Pitch`, `rate` и `volume` улучшены для поддержки настройки с помощью предопределенного значения, такого как "медленный", "средний" и "быстрый". Теперь пользователи легко могут выбрать значение "Constant" для редактирования звука.

    ![Настройка звука](media/release-notes/audio-tuning.png)

    * Теперь пользователи могут просматривать `Audio history` для своих рабочих файлов. С помощью этой функции пользователи могут легко отслеживание всех созданных звуковых файлов, связанных с рабочим файлом. Они могут проверить версию журнала и сравнить качество во время настройки одновременно. 

    ![История аудио](media/release-notes/audio-history.png)

    * Эта `Clear` функция стала более гибкой. Пользователи могут очистить конкретный параметр настройки, сохранив другие параметры для выбранного содержимого.  

    * На [целевую страницу](https://speech.microsoft.com/audiocontentcreation) было добавлено учебное видео, помогающее пользователям быстро начать работу с голосовыми настройками TTS и управлением звуками. 

### <a name="general-tts-voice-quality-improvements"></a>Усовершенствования общего качества голоса TTS

* Улучшена функция TTS вокодер для повышения точности и снижения задержки.

    * Обновленный Елса в `it-IT` Новый вокодер, который достигает + 0,464 CMOS (сравнительная оценка очков), повышает качество голоса, 40% быстрее в синтезе и 30% сокращает задержку в первых байтах. 
    * Обновлена Ксиаоксиао в `zh-CN` новую вокодер с усилением + 0148 CMOS для общего домена, + 0,348 для стиля невскаст и + 0,195 для стиля лирикал. 

* Обновленные `de-DE` и `ja-JP` голоса модели, чтобы сделать вывод в формате TTS более естественным.
    
    * Обновленный Катжа в `de-DE` с последним методом моделирования Prosody, МОС (среднее мнение) получает значение + 0,13. 
    * Обновленная Нанами в `ja-JP` с новым Prosody моделью с акцентом на шаг, МОС (среднее мнение) — + 0,19;  

* Улучшена точность произношения на уровне слов на пяти языках.

    | Язык | Сокращение ошибок произношения |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39 % |
    | `pt-PT` | 77 % |
    | `id-ID` | 46 % |

### <a name="bug-fixes"></a>Исправления ошибок

* Чтение валюты
    * Исправлена проблема с чтением валюты для `es-ES` и `es-MX`
     
    | Язык | Входные данные | Цветовая подмодели после улучшения |
    |---|---|---|
    | `es-MX` | $1,58 | уменьшение песо Цинкуента y Очо ЦЕНТАВОС |
    | `es-ES` | $1,58 | дóлар Цинкуента y Очо ЦЕНТАВОС |

    * Поддержка отрицательной валюты (например, "-€325") в следующих языковых стандартах: `en-US` ,, `en-GB` ,, `fr-FR` `it-IT` `en-AU` , `en-CA` .

* Улучшено чтение адресов в `pt-PT` .
* Исправлены `en-AU` проблемы произношения Наташа () и Либби ( `en-UK` ) по словам "для" и "четыре".  
* Исправлены ошибки в средстве создания звукового содержимого
    * После фиксирования второго абзаца происходит дополнительная и непредвиденная пауза.  
    * Функция "без перерыва" добавляется обратно из ошибки регрессии. 
    * Проблема случайного обновления в Speech Studio исправлена.  

### <a name="samplessdk"></a>Примеры и пакеты SDK

* JavaScript: устраняет проблему воспроизведения в Firefox и Safari в macOS и iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>Пакет SDK для распознавания речи 1.12.1:2020 — выпуск от июня
**Речевой интерфейс командной строки (также известный как SPX)**
-   Добавлены функции поиска справки в CLI:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Обновлены для работы с новыми развернутыми пакетными API версии 3.0 и Пользовательское распознавание речи.
    -   `spx help batch examples`
    -   `spx help csr examples`

**Новые функции**
-   **C \# , C++** : распознавание говорящего Preview: Эта функция включает идентификацию докладчика (кто говорит?) и верификацию докладчика (является докладчиком, которому они заявляют?). Начните с [обзора](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/speaker-recognition-overview), ознакомьтесь со [статьей распознавание говорящего основы](https://docs.microsoft.com/azure/cognitive-services/speech-service/speaker-recognition-basics)или [справочной документацией по API](https://docs.microsoft.com/rest/api/speakerrecognition/).

**Исправления ошибок**
-   **C \# , C++** : фиксированная запись с микрофона не работала в 1,12 при распознавании речи.
-   **JavaScript** : исправления для преобразования текста в речь в Firefox и Safari в MacOS и iOS.
-   Устранение ошибки нарушения прав доступа для средства проверки приложений Windows при записи разговора при использовании потока с восемью каналами.
-   Устранение ошибки нарушения прав доступа для средства проверки приложений Windows при преобразовании бесед на нескольких устройствах.

**Примеры**
-   **C#** : [пример кода](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) для распознавания докладчика.
-   **C++** : [пример кода](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) для распознавания докладчика.
-   **Java** : [пример кода](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) для распознавания намерения в Android. 

**Ковид-19 краткие тестирование:** Благодаря удаленной работе за последние несколько недель мы не смогли выполнить проверку вручную, как и обычно. Мы не внесли изменения, которые могли бы привести к нарушению, и наши автоматические тесты прошли успешно. В маловероятном случае, если мы пропустили что-нибудь, сообщите нам об этом на [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Оставайтесь в курсе событий!


## <a name="speech-sdk-1120-2020-may-release"></a>Пакет SDK для распознавания речи 1.12.0:2020 — выпуск может быть выпущен
**Речевой интерфейс командной строки (также известный как SPX)**
- **SPX** — это новое средство командной строки, которое позволяет выполнять распознавание, синтез, перевод, отправку пакетов и пользовательские средства управления распознаванием речи из командной строки. Используйте его для тестирования службы распознавания речи или написания сценариев для задач службы распознавания речи, которые необходимо выполнить. Загрузите средство и прочитайте документацию [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-overview).

**Новые функции**
- **Go** : Новая поддержка языка Go для [распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) и [пользовательского голосового помощника](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go). Настройте среду разработки [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-go). Пример кода см. в разделе примеры ниже. 
- **JavaScript** : добавлена поддержка браузера для преобразования текста в речь. См. документацию [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech-audio-file?pivots=programming-language-JavaScript).
- **C++, C#, Java** : новые `KeywordRecognizer` объекты и API, поддерживаемые на платформах Windows, Android, Linux & iOS. Ознакомьтесь с документацией [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-overview). Пример кода см. в разделе примеры ниже. 
- **Java** : Добавлено обсуждение с несколькими устройствами с поддержкой перевода. См. справочный документ [здесь](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.transcription).

**Улучшения & оптимизации**
- **JavaScript**. Оптимизированная реализация микрофона браузера улучшает точность распознавания речи.
- **Java** : рефакторингы привязок с использованием прямой реализации JNI без Свиг. Это изменение сокращает размер привязок для всех пакетов Java, используемых для Windows, Android, Linux и Mac, а также упрощает разработку реализации Java для речевого пакета SDK.
- **Linux** : обновленная [Документация](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux) по технической поддержке с последними заметками RHEL 7.
- Улучшена логика подключения для многократной попытки подключения при возникновении ошибок в службе и сети.
- Обновлена страница "Быстрый запуск [Portal.Azure.com](https://portal.azure.com) Speech", которая поможет разработчикам перейти к следующему этапу в пути распознавания речи Azure.

**Исправления ошибок**
- **C#, Java** : Исправлена [проблема](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) с загрузкой библиотек SDK на ARM Linux (как 32, так и 64 бит).
- **C#** : Исправлена явная реализация собственных дескрипторов для Транслатионрекогнизер, интентрекогнизер и объектов соединения.
- **C#** : фиксированное управление временем жизни звукового ввода для объекта конверсатионтранскрибер.
- Исправлена проблема, `IntentRecognizer` при которой причина результата не была правильно задана при распознавании целей из простых фраз.
- Исправлена проблема, при которой `SpeechRecognitionEventArgs` не было задано правильное смещение результата.
- Исправлено состояние гонки, когда пакет SDK пытался отправить сетевое сообщение перед открытием подключения WebSocket. Было воспроизводимо за `TranslationRecognizer` время добавления участников.
- Фиксированные утечки памяти в подсистеме распознавателя ключевых слов.

**Примеры**
- **Go** : добавлены краткие руководства для [распознавания речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) и [пользовательского голосового помощника](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go). Найдите пример кода [здесь](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples). 
- **JavaScript** : добавлены краткие руководства для преобразования [текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech?pivots=programming-language-javascript), [перевода](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-speech-translation?tabs=script&pivots=programming-language-csharp)и [Распознавание намерений](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition?pivots=programming-language-javascript).
- Примеры распознавания ключевых слов [для \# C](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) и [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**Ковид-19 краткие тестирование:** Благодаря удаленной работе за последние несколько недель мы не смогли выполнить проверку вручную, как и обычно. Мы не внесли изменения, которые могли бы привести к нарушению, и наши автоматические тесты прошли успешно. Если мы пропустили что-нибудь, сообщите нам об этом на [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Оставайтесь в курсе событий!

## <a name="speech-sdk-1110-2020-march-release"></a>Пакет SDK для распознавания речи 1.11.0:2020 — выпуск, Март
**Новые функции**
- Linux: добавлена поддержка Red Hat Enterprise Linux (RHEL)/Центос 7 x64 с [инструкциями](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) по настройке системы для работы с ГОЛОСОВыми пакетами SDK.
- Linux: добавлена поддержка .NET Core C# в Linux ARM32 и ARM64. Дополнительные сведения см. [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux). 
- C#, C++: добавлен `UtteranceId` в `ConversationTranscriptionResult` , согласованный идентификатор для всех промежуточных и конечных результатов распознавания речи. Сведения для [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet&preserve-view=true), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: добавлена поддержка `Language ID` . См. раздел speech_sample. корректировка в [репозитории GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: добавлена поддержка сжатого звукового входного формата на платформе Windows для всех консольных приложений Win32. Подробные сведения см. [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). 
- JavaScript: поддержка синтеза речи (преобразование текста в речь) в NodeJS. Дополнительные сведения см. [здесь](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript. Добавьте новый API, чтобы включить проверку всех сообщений об отправке и полученных сообщениях. Дополнительные сведения см. [здесь](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Исправления ошибок**
- C#, C++: Исправлена проблема, поэтому `SendMessageAsync` теперь отправляет двоичное сообщение как двоичный тип. Сведения для [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection).
- C#, C++: Исправлена проблема, из-за которой использование `Connection MessageReceived` события может вызвать сбой `Recognizer` , если удаляется перед `Connection` объектом. Сведения для [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet&preserve-view=true), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived).
- Android: размер звукового буфера с микрофона уменьшился с 800ms до 100 мс для улучшения задержки.
- Android: Исправлена [проблема](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) с эмулятором Android для платформы x86 в Android Studio.
- JavaScript: добавлена поддержка регионов в Китае с помощью `fromSubscription` API. Подробные сведения см. [здесь](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#fromsubscription-string--string-). 
- JavaScript: Добавление дополнительных сведений об ошибках для ошибок подключения из NodeJS.
        
**Примеры**
- Unity: Открытый пример распознавания намерения является фиксированным, где не удалось импортировать LUIS JSON. Подробные сведения см. [здесь](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: Добавлен пример для `Language ID` . Подробные сведения см. [здесь](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Тестирование Covid19 краткие:** Из-за последних нескольких недель мы не смогли выполнить тестирование проверки устройств вручную, как обычно. Например, нам не удалось протестировать входные данные с микрофона и динамика в Linux, iOS и macOS. Мы не внесли изменения, которые могли бы привести к нарушению данных на этих платформах, а также выполнению всех автоматических тестов. В маловероятном случае, если мы пропустили что-нибудь, сообщите нам об этом на [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Благодарим вас за продолжающуюся поддержку. Вы всегда можете отправить вопросы или отзывы на [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) или [Stack overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Оставайтесь в курсе событий!

## <a name="speech-sdk-1100-2020-february-release"></a>Пакет SDK для распознавания речи 1.10.0:2020 — выпуск, Февраль

**Новые функции**

 - Добавлены пакеты Python для поддержки нового выпуска Python 3,8.
 - Поддержка 64-разрядных версий Red Hat Enterprise Linux (RHEL)/Центос 8 (C++, C#, Java, Python).
   > [!NOTE] 
   > Клиенты должны настроить OpenSSL в соответствии с [этими инструкциями](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Поддержка Debian и Ubuntu в Linux ARM32.
 - Диалогсервицеконнектор теперь поддерживает необязательный параметр "Bot ID" в Ботфрамеворкконфиг. Этот параметр позволяет использовать несколько прямых голосовых программы-роботы с одним ресурсом речи Azure. Если параметр не указан, будет использоваться программа-робот по умолчанию (как определено на странице конфигурации канала обработки речи прямой строки).
 - Диалогсервицеконнектор теперь имеет свойство Спичактивититемплате. Содержимое этой строки JSON будет использоваться прямым голосовым голосом для предварительного заполнения самых разнообразных поддерживаемых полей во всех действиях, которые достигают прямой роботной программы перевода речи, включая действия, автоматически формируемые в ответ на события, такие как распознавание речи.
 - Теперь функция TTS использует ключ подписки для проверки подлинности, уменьшая первую задержку в байтах первого синтеза результатов после создания синтезатора.
 - Обновлены модели распознавания речи для 19 национальных стандартов для среднего числа ошибок в Word, уменьшенных на 18,6% (ES-ES). es-MX, fr-CA, fr-FR, ИТ-IT, ja-JP, ko-KR, PT-BR, zh-CN, zh, HK, NetBIOS-нет, Fi-FL, ru-RU, PL-PL, CA-ES, zh-TW, th-TH, PT-PT, tr-TR. Новые модели обеспечивают значительные улучшения в нескольких доменах, в том числе диктовку Call-Center и сценарии индексирования видео.

**Исправления ошибок**

 - Исправлена ошибка, при которой диалог Транскрибер не был правильно ожидать в API-интерфейсах JAVA. 
 - Исправление для проблемы с эмулятором Android x86 для Xamarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Добавление недостающего (Get | Set) методы свойств для Аудиоконфиг
 - Исправление ошибки TTS, в которой не удалось остановить Аудиодатастреам в случае сбоя подключения
 - Использование конечной точки без региона вызовет сбой USP для транслятора диалога
 - Создание идентификатора в универсальных приложениях Windows теперь использует алгоритм правильного уникального идентификатора GUID; он ранее и непреднамеренно использовал по умолчанию реализацию создана, которая часто создавала конфликт над большими наборами взаимодействий.
 
 **Примеры**
 
 - Пример Unity для использования речевого пакета SDK с помощью [потоковой передачи Unity и режима push-уведомлений](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Другие изменения**

 - [Документация по конфигурации OpenSSL обновлена для Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Пакет SDK для распознавания речи 1.9.0:2020-выпуск за Январь

**Новые функции**

- Беседа с несколькими устройствами. Подключение нескольких устройств к одному речевому или текстовому диалогу и при необходимости преобразование передаваемых сообщений. Дополнительные сведения см. в [этой статье](multi-device-conversation.md). 
- Добавлена поддержка распознавания ключевых слов для пакета Android. AAR и добавлена поддержка для версий x86 и x64. 
- Цель-C: `SendMessage` и `SetMessageProperty` методы, добавленные в `Connection` объект. См. документацию [здесь](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- API-интерфейс TTS C++ теперь поддерживает `std::wstring` Ввод текста синтеза, избавляя от необходимости преобразовывать wstring в строку перед передачей их в пакет SDK. Дополнительные сведения см. [здесь](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [идентификатор языка](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) и [Конфигурация языка исходного кода](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) теперь доступны.
- JavaScript: в объект добавлена функция `Connection` для передачи пользовательских сообщений из службы речевого вызова в качестве обратного вызов `receivedServiceMessage` .
- JavaScript: добавлена поддержка `FromHost API` для упрощения использования с локальными контейнерами и облаками независимых. См. документацию [здесь](speech-container-howto.md).
- JavaScript: Теперь мы соблюдаем `NODE_TLS_REJECT_UNAUTHORIZED` за вкладом из [оргадс](https://github.com/orgads). Дополнительные сведения см. [здесь](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Критические изменения**

- `OpenSSL` был обновлен до версии 1.1.1 b и статически связан с основной библиотекой пакета SDK для обработки речи для Linux. Это может привести к сбою, если папка "Входящие" `OpenSSL` не была установлена в `/usr/lib/ssl` каталог в системе. Чтобы решить эту задачу, обратитесь [к документации по](how-to-configure-openssl-linux.md) РЕЧЕВому пакету SDK.
- Мы изменили тип данных, возвращаемый для C#, с на, чтобы `WordLevelTimingResult.Offset` `int` `long` Разрешить доступ, `WordLevelTimingResults` когда речевые данные больше 2 минут.
- `PushAudioInputStream``PullAudioInputStream`теперь отправляйте данные заголовка WAV в службу распознавания речи на основе `AudioStreamFormat` , при необходимости заданной при их создании. Теперь клиенты должны использовать [поддерживаемый входной формат аудио](how-to-use-audio-input-streams.md). Любые другие форматы будут получать неоптимальные результаты распознавания или могут вызвать другие проблемы. 

**Исправления ошибок**

- См `OpenSSL` . раздел обновление в разделе критические изменения выше. Мы исправили как временные сбои, так и проблемы с производительностью (состязание за блокировку при высокой нагрузке) в Linux и Java. 
- Java: внесены улучшения в замыкание объектов в сценариях с высоким уровнем параллелизма.
- Реструктуризация пакета NuGet. Мы удалили три копии `Microsoft.CognitiveServices.Speech.core.dll` и `Microsoft.CognitiveServices.Speech.extension.kws.dll` в папках lib, сделав пакет NuGet меньше и быстрее, а также добавили заголовки, необходимые для компиляции некоторых собственных приложений C++.
- Исправлены примеры краткого [руководства.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Они вышли без отображения исключения "микрофон не найден" в Linux, macOS, Windows.
- Исправлен сбой пакета SDK с длительными результатами распознавания речи по определенным путям кода, таким как [Этот пример](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Исправлена ошибка развертывания пакета SDK в среде веб-приложения Azure для решения [этой проблемы клиента](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Исправлена ошибка TTS при использовании нескольких `<voice>` тегов или `<audio>` тегов для решения [этой проблемы клиента](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Исправлена ошибка TTS 401 при восстановлении пакета SDK из приостановленного.
- JavaScript: Исправлена циклическое импортирование звуковых данных благодаря вкладу из [еуирим](https://github.com/euirim). 
- JavaScript: добавлена поддержка настройки свойств службы, как Добавлено в 1,7.
- JavaScript: Исправлена проблема, из-за которой ошибка подключения может привести к появлению непрерывных неудачных попыток повторного подключения WebSocket.

**Примеры**

- Добавлен пример распознавания ключевых слов для [Android.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)
- В [этой статье](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)добавлен пример TTS для серверного сценария.
- Добавлены краткие руководства по беседам с несколькими устройствами для C# [и C++.](quickstarts/multi-device-conversation.md)

**Другие изменения**

- Размер оптимизированной библиотеки SDK Core в Android.
- Пакет SDK в 1.9.0 и onward поддерживает как `int` типы, так и `string` в поле версии голосовой подписи для диалога транскрибер.

## <a name="speech-sdk-180-2019-november-release"></a>Пакет SDK для распознавания речи 1.8.0:2019 – Ноябрьский выпуск

**Новые функции**

- Добавлен `FromHost()` API для упрощения использования с локальными контейнерами и облаками независимых.
- Добавлены автоматические распознавание языка источника для распознавания речи (в Java и C++)
- Добавлен `SourceLanguageConfig` объект для распознавания речи, используемый для указания ожидаемых исходных языков (в Java и C++).
- Добавлена `KeywordRecognizer` Поддержка в Windows (UWP), Android и iOS через пакеты NuGet и Unity.
- Добавлен удаленный диалог Java API для записи разговора в асинхронных пакетах.

**Критические изменения**

- Функции диалога Транскрибер перемещены в пространстве имен `Microsoft.CognitiveServices.Speech.Transcription` .
- Части методов диалога Транскрибер перемещаются в новый `Conversation` класс.
- Удалена поддержка 32-bit (ARMv7 и x86) iOS

**Исправления ошибок**

- Исправить ошибку, если локальная `KeywordRecognizer` служба используется без допустимого ключа подписки на службу речи

**Примеры**

- Пример Xamarin для `KeywordRecognizer`
- Пример Unity для `KeywordRecognizer`
- Примеры C++ и Java для автоматического распознавание языка источника.

## <a name="speech-sdk-170-2019-september-release"></a>Пакет SDK для распознавания речи 1.7.0:2019 – сентябрь выпуска

**Новые функции**

- Добавлена поддержка бета-версии для Xamarin на универсальная платформа Windows (UWP), Android и iOS.
- Добавлена поддержка iOS для Unity.
- Добавлена `Compressed` Поддержка ввода для Алав, мулав, FLAC в Android, iOS и Linux.
- Добавлен `SendMessageAsync` в `Connection` класс для отправки сообщения в службу.
- Добавлен `SetMessageProperty` в `Connection` класс для задания свойства сообщения
- Добавленные в TTS привязки для Java (JRE и Android), Python, SWIFT и цели-C
- Добавлена поддержка воспроизведения в режиме TTS для macOS, iOS и Android.
- Добавлены сведения о границах слов для TTS.

**Исправления ошибок**

- Исправлена проблема сборки IL2CPP в Unity 2019 для Android.
- Исправлена ошибка неправильной обработки неправильно сформированных заголовков в входных файлах WAV
- Исправлена проблема с UUID, которая не уникальна в некоторых свойствах соединения
- Исправлено несколько предупреждений о спецификаторах допустимости значений NULL в привязках SWIFT (может потребоваться внести небольшие изменения в код).
- Исправлена ошибка, которая привела к некорректному закрытию подключений WebSocket при сетевой нагрузке.
- Исправлена проблема с Android, которая иногда приводит к дублированию идентификаторов воссозданий, используемых `DialogServiceConnector`
- Улучшения стабильности подключений при множественном взаимодействии и отчеты об ошибках (через `Canceled` события) при возникновении `DialogServiceConnector`
- `DialogServiceConnector` сеансы запуска будут правильно предоставлять события, включая вызов `ListenOnceAsync()` во время активного `StartKeywordRecognitionAsync()`
- Устранены сбои, связанные с `DialogServiceConnector` получением действий

**Примеры**

- Краткое руководство по Xamarin
- Обновленное краткое руководство по CPP с информацией о ARM64 Linux
- Руководство по обновлению Unity с информацией о iOS

## <a name="speech-sdk-160-2019-june-release"></a>Пакет SDK для распознавания речи 1.6.0:2019 — выпуск от июня

**Примеры**

- Примеры быстрого запуска для преобразования текста в речь об UWP и Unity
- Пример краткого руководства для SWIFT в iOS
- Примеры Unity для & распознавание намерений и перевода речи
- Обновленные примеры краткого руководства для `DialogServiceConnector`

**Улучшения и изменения**

- Пространство имен диалогового окна:
  - `SpeechBotConnector` был переименован в `DialogServiceConnector`.
  - `BotConfig` был переименован в `DialogServiceConfig`.
  - `BotConfig::FromChannelSecret()` был повторно сопоставлен с `DialogServiceConfig::FromBotSecret()`
  - Все существующие клиентские голосовые программы в прямом направлении продолжают поддерживаться после переименования
- Обновление адаптера TTS RESTFUL для поддержки прокси-сервера, постоянного подключения
- Улучшить сообщение об ошибке при передаче недопустимого региона
- SWIFT/цель-C:
  - Улучшенные отчеты об ошибках. методы, которые могут привести к ошибке, теперь существуют в двух версиях: одной, предоставляющей `NSError` объект для обработки ошибок, и одна вызывает исключение. Первый из них предоставляется SWIFT. Это изменение требует адаптации к существующему коду SWIFT.
  - Улучшенная обработка событий

**Исправления ошибок**

- Исправление для TTS: Если `SpeakTextAsync` будущее возвращается без ожидания завершения отрисовки звука
- Исправление для упаковки строк в C# для обеспечения полной поддержки языка
- Исправление проблемы с приложением .NET Core для загрузки основной библиотеки с помощью net461 Target Framework в примерах
- Устранение редких проблем при развертывании собственных библиотек в выходной папке в примерах
- Надежное исправление для закрытия веб-сокета
- Устранение возможных сбоев при загрузке подключения при высокой нагрузке в Linux
- Исправление отсутствующих метаданных в пакете платформы для macOS
- Устранение неполадок `pip install --user` в Windows

## <a name="speech-sdk-151"></a>Пакет SDK для распознавания речи 1.5.1

Это выпуск исправления ошибки, который влияет только на пакет SDK для машинного или управляемого кода. Это не влияет на версию пакета SDK для JavaScript.

**Исправления ошибок**

- Исправление Фромсубскриптион при использовании записи разговора.
- Исправление ошибки в ключевом слове для помощников по голосовым сообщениям.

## <a name="speech-sdk-150-2019-may-release"></a>Пакет SDK для распознавания речи 1.5.0:2019 — выпуск может быть выпущен

**Новые функции**

- Теперь для Windows и Linux доступен поиск по ключевым словам (КВС). Функции КВС могут работать с любым типом микрофона, официальная поддержка КВС, в настоящее время, ограничена только массивами микрофонов, находящихся в оборудовании Azure Kinect DK или в пакете SDK для речевых устройств.
- Функциональность подсказки по фразам доступна в пакете SDK. Дополнительные сведения см. [здесь](how-to-phrase-lists.md).
- Функции записи беседы доступны через пакет SDK. См. [здесь](conversation-transcription-service.md).
- Добавление поддержки голосовых помощников с помощью канала голосовой речи.

**Примеры**

- Добавлены образцы для новых функций или новых служб, поддерживаемых пакетом SDK.

**Улучшения и изменения**

- Добавлены различные свойства распознавателя для настройки поведения службы или ее результатов (например, ненормативная лексика и другие).
- Теперь можно настроить распознаватель с помощью стандартных свойств конфигурации, даже если вы создали распознаватель `FromEndpoint` .
- Цель-C: `OutputFormat` свойство добавлено в `SPXSpeechConfiguration` .
- Пакет SDK теперь поддерживает Debian 9 в качестве дистрибутива Linux.

**Исправления ошибок**

- Исправлена проблема, из-за которой ресурс динамика был слишком ранней структуры в тексте для перевода в речь.

## <a name="speech-sdk-142"></a>Пакет SDK для распознавания речи 1.4.2

Это выпуск исправления ошибки, который влияет только на пакет SDK для машинного или управляемого кода. Это не влияет на версию пакета SDK для JavaScript.

## <a name="speech-sdk-141"></a>Пакет SDK для распознавания речи 1.4.1

Это выпуск только для JavaScript. В нем нет никаких новых возможностей. Устранены следующие проблемы:

- Запретить веб-пакету загрузку HTTPS-Proxy-Agent.

## <a name="speech-sdk-140-2019-april-release"></a>Пакет SDK для распознавания речи 1.4.0:2019 — выпуск от апреля

**Новые функции**

- Пакет SDK теперь поддерживает службу преобразования текста в речь в качестве бета-версии. Он поддерживается на рабочем столе Windows и Linux из C++ и C#. Дополнительные сведения см. в [обзоре преобразования текста в речь](text-to-speech.md#get-started).
- Пакет SDK теперь поддерживает звуковые файлы MP3 и опус/OGG в качестве потоковых входных файлов. Эта функция доступна только в Linux из C++ и C# и сейчас находится в бета-версии (Дополнительные сведения см. [здесь](how-to-use-codec-compressed-audio-input-streams.md)).
- Пакет SDK для распознавания речи для Java, .NET Core, C++ и цели-C получил поддержку macOS. Поддержка цели-C для macOS в настоящее время находится в бета-версии.
- iOS: речевой пакет SDK для iOS (цель-C) теперь также опубликован как Кокоапод.
- JavaScript: поддержка микрофона, отличного от используемого по умолчанию, в качестве устройства ввода.
- JavaScript: поддержка прокси-сервера для Node.js.

**Примеры**

- Добавлены примеры использования пакета SDK для распознавания речи с C++ и with macOS с уровнем "цель-C".
- Добавлены примеры, демонстрирующие использование службы преобразования текста в речь.

**Улучшения и изменения**

- Python: теперь доступны дополнительные свойства результатов распознавания через `properties` свойство.
- Для дополнительной поддержки разработки и отладки можно перенаправить журналы и данные диагностики пакета SDK в файл журнала (Дополнительные сведения см. [здесь](how-to-use-logging.md)).
- JavaScript: повышение производительности обработки звука.

**Исправления ошибок**

- Mac/iOS: ошибка, которая привела к длительному ожиданию, когда не удалось установить подключение к службе распознавания речи.
- Python: улучшение обработки ошибок для аргументов в обратных вызовах Python.
- JavaScript: Исправлена неправильная отчетность о состоянии для речи, завершенных в Рекуестсессион.

## <a name="speech-sdk-131-2019-february-refresh"></a>Пакет SDK для распознавания речи 1.3.1:2019-февральское обновление

Это выпуск исправления ошибки, который влияет только на пакет SDK для машинного или управляемого кода. Это не влияет на версию пакета SDK для JavaScript.

**Исправление ошибок**

- Исправлена утечка памяти при использовании ввода с микрофона. Не влияет на потоковый ввод или на файловый вход.

## <a name="speech-sdk-130-2019-february-release"></a>Пакет SDK для распознавания речи 1.3.0:2019 — выпуск, Февраль

**Новые функции**

- Пакет SDK для распознавания речи поддерживает выбор входного микрофона через `AudioConfig` класс. Это позволяет передавать звуковые данные в службу распознавания речи с микрофона, не используемого по умолчанию. Дополнительные сведения см. в документации, описывающей [Выбор устройства ввода звука](how-to-select-audio-input-devices.md). Эта функция пока недоступна в JavaScript.
- Пакет SDK для службы "Речь" теперь поддерживает Unity в бета-версии. Оставьте отзыв в разделе "проблемы" в [репозитории примера GitHub](https://aka.ms/csspeech/samples). Текущий выпуск поддерживает Unity на 32- или 64-разрядной ОС Windows (классическое приложение или приложение универсальной платформы Windows) и Android (32- или 64-разрядная архитектура ARM). Дополнительные сведения можно найти в [кратком руководстве по Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- Файл `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (поставляются в предыдущих выпусках) больше не требуется. Теперь функциональные возможности интегрированы в пакет SDK для Core.

**Примеры**

В нашем [репозитории с примерами](https://aka.ms/csspeech/samples) размещено следующее новое содержимое:

- Дополнительные примеры для `AudioConfig.FromMicrophoneInput` .
- дополнительные примеры Python для распознавания намерений и перевода;
- Дополнительные примеры использования `Connection` объекта в iOS.
- дополнительные примеры на Java для перевода с выводом звуковых данных;
- новый пример использования [REST API пакетного транскрибирования](batch-transcription.md).

**Улучшения и изменения**

- Python
  - Улучшена проверка параметров и сообщений об ошибках в `SpeechConfig` .
  - Добавьте поддержку для `Connection` объекта.
  - Поддержка 32-разрядной версии Python (x86) на Windows.
  - Выпущена бета-версия пакета SDK службы "Речь" для Python.
- iOS
  - Пакет SDK теперь компилируется с пакетом SDK для iOS версии 12.1.
  - Пакет SDK теперь поддерживает версию iOS 9.2 и более поздние версии.
  - Улучшена справочная документация и исправлены несколько имен свойств.
- JavaScript
  - Добавьте поддержку для `Connection` объекта.
  - Добавлены файлы определений типов для объединенного файла JavaScript.
  - Первичная поддержка и реализация подсказок для фраз.
  - Возвращается коллекция свойств с шаблоном JSON для службы распознавания.
- Библиотеки DLL для Windows теперь содержат ресурс версии.
- При создании распознавателя `FromEndpoint` можно добавлять параметры непосредственно в URL-адрес конечной точки. `FromEndpoint`Вы не можете настроить распознаватель с помощью стандартных свойств конфигурации.

**Исправления ошибок**

- Неправильно обрабатывались пустые значения для имени пользователя и пароля прокси-сервера. В этом выпуске имя пользователя и пароль для прокси-сервера не будут отправляться при подключении к прокси-серверу, если эти строки оставлены пустыми.
- Создаваемый пакетом SDK идентификатор сессии (SessionId) в некоторых языках&nbsp;и средах не был достаточно случайным. Добавлена инициализация генератора случайных чисел для устранения этой проблемы.
- Улучшена обработка маркера авторизации. Если вы хотите использовать маркер авторизации, укажите в параметре `SpeechConfig` и оставьте ключ подписки пустым. После этого создайте распознаватель обычным образом.
- В некоторых случаях `Connection` объект был освобожден неправильно. Теперь эта проблема устранена.
- Исправлен пример JavaScript для поддержки аудиовыхода для синтеза перевода в браузере Safari.

## <a name="speech-sdk-121"></a>Пакет SDK для службы "Речь" 1.2.1

Это выпуск только для JavaScript. В нем нет никаких новых возможностей. Устранены следующие проблемы:

- Срабатывание завершения потока по turn.end вместо speech.end.
- Исправлена ошибка в конвейере аудио, которая мешала запланировать новую отправку при сбое текущей отправки.
- Исправлено непрерывное распознавание с маркером проверки подлинности.
- Исправлены ошибки для некоторых распознавателей и конечных точек.
- Улучшена документация.

## <a name="speech-sdk-120-2018-december-release"></a>Пакет SDK для распознавания речи 1.2.0:2018 — Декабрь

**Новые функции**

- Python
  - В этом выпуске доступна бета-версия поддержки Python (3.5 и более поздних версий). Дополнительные сведения см. здесь] (краткое руководство — python.md).
- JavaScript
  - Пакет SDK для службы "Речь" для JavaScript преобразован в ресурс с открытым исходным кодом. Исходный код доступен на сайте [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Добавлена поддержка Node.js. Дополнительные сведения можно найти [здесь](quickstart-js-node.md).
  - Ограничение длины для аудиосеансов снято, повторное подключение происходит автоматически.
- Объект `Connection`.
  - Из можно `Recognizer` получить доступ к `Connection` объекту. Этот объект позволяет явно инициировать подключение к службе и подписаться на события подключения и отключения.
    (Эта функция еще не доступна в JavaScript и Python.)
- Поддержка Ubuntu 18.04.
- Android
  - Включена поддержка ProGuard во время создания пакета APK.

**Улучшения**

- Улучшения в использовании внутреннего потока, сокращение числа потоков, блокировок, взаимных исключений.
- Улучшены сведения и сообщения об ошибках. В некоторых случаях сообщения об ошибках не были распространены полностью.
- Изменены зависимости разработки в JavaScript, чтобы использовать модули последних версий.

**Исправления ошибок**

- Фиксированные утечки памяти из-за несоответствия типов в `RecognizeAsync` .
- В некоторых случаях возникала утечка исключений.
- Исправление утечки памяти в аргументах событий перевода.
- Исправлена проблема с блокировкой при повторном подключении в длительных сеансах.
- Исправлена проблема, которая может привести к отсутствию конечного результата для переводов, завершившихся сбоем.
- C#: Если `async` операция не ожидалась в основном потоке, возможно, распознаватель был удален до завершения асинхронной задачи.
- Java: Исправлена проблема, приводящая к сбою виртуальной машины Java.
- Цель-C: фиксированное сопоставление перечислений; Рекогнизединтент был возвращен вместо `RecognizingIntent` .
- JavaScript: задайте для параметра формат вывода по умолчанию значение SIMPLE в `SpeechConfig` .
- JavaScript: удаление несогласованности между свойствами объекта конфигурации на JavaScript и других языках.

**Примеры**

- Обновленные и исправлены несколько примеров (например, выходные голоса для перевода и т. д.).
- Добавлены примеры для Node.js в [репозиторий примеров](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Пакет SDK для службы "Речь" 1.1.0

**Новые функции**

- Поддержка Android x86/x64.
- Поддержка прокси: в `SpeechConfig` объекте теперь можно вызвать функцию, чтобы задать сведения о прокси-сервере (имя узла, порт, имя пользователя и пароль). Эта функция недоступна в iOS.
- Улучшенный код ошибки и сообщения. Если распознавание возвращает ошибку, это означает, что для `Error` уже установлен набор `Reason` (в отмененное событие) или `CancellationDetails` (в результат распознавания). Отмененное событие теперь содержит два дополнительных элемента: `ErrorCode` и `ErrorDetails`. Если сервер вернул дополнительные сведения об ошибке вместе с сообщаемой ошибкой, то она будет доступна в новых элементах.

**Улучшения**

- В конфигурацию распознавателя добавлена дополнительная проверка и сообщение об ошибке.
- Улучшена обработка долгого периода тишины в середине звукового файла.
- Пакет NuGet: не допускает компиляцию с конфигурацией AnyCPU для проектов .NET Framework.

**Исправления ошибок**

- Исправлено несколько исключений из распознавателей. Кроме того, исключения перехватываются и преобразуются в `Canceled` событие.
- Утечка памяти в управлении свойствами устранена.
- Ошибка, в которой входной файл аудио мог стать причиной аварийного завершения распознавателя, исправлена.
- Исправлена ошибка, где события могут быть получены после остановки сеанса.
- Исправлены некоторые состояния гонки в потоке.
- Проблема совместимости, которая может привести к аварийному завершению iOS, исправлена.
- Улучшение стабильности для поддержки микрофона на Android.
- Исправлена ошибка, где распознаватель в JavaScript игнорирует распознавание языка.
- Исправлена ошибка, препятствующая заданию `EndpointId` (в некоторых случаях) в JavaScript.
- Изменен порядок параметров в Аддинтент в JavaScript и добавлена отсутствующая `AddIntent` подпись JavaScript.

**Примеры**

- Добавлены примеры C++ и C# для использования потока извлечения и отправки в [примере репозитория](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Пакет SDK для службы "Речь" 1.0.1

Повышение надежности и устранение ошибок:

- Исправлена потенциальная неустранимая ошибка из-за состояния гонки в удаленном распознавателе
- Исправлена потенциальная Неустранимая ошибка при возникновении незавершенных свойств.
- Добавлены дополнительные ошибки и проверки параметров.
- Objective-C: исправлена потенциальная неустранимая ошибка, возникающая из-за переопределения имени в NSString.
- Objective-C: скорректирована видимость API
- JavaScript: исправления относительно событий и их полезных данных.
- Улучшена документация.

В нашем [репозитории примеров](https://aka.ms/csspeech/samples) добавлен новый пример для JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Пакет SDK для службы "Речь" 1.0.0 в Cognitive Services: выпуск за сентябрь 2018 г.

**Новые функции**

- Поддержка Objective-C в iOS. Ознакомьтесь с нашим [кратким руководством по Objective-C для iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Поддержка JavaScript в браузере. Ознакомьтесь с нашим [кратким руководством по JavaScript](quickstart-js-browser.md).

**Критические изменения**

- В этом выпуске введено несколько критических изменений.
  Дополнительные сведения см. на [этой странице](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Пакет SDK 0.6.0 для службы "Речь" в Cognitive Services: выпуск за август 2018 г.

**Новые функции**

- Теперь приложения UWP, созданные с использованием пакета SDK для службы "Речь", могут пройти сертификацию WACK (комплект сертификации приложений для Windows).
  Ознакомьтесь с [кратким руководством по UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Поддержка платформы .NET Standard 2.0 в Linux (Ubuntu 16.04 x 64).
- Экспериментальная функция: поддержка Java 8 в 64-разрядной версии Windows и Linux (Ubuntu 16.04 x 64).
  Ознакомьтесь с [кратким](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)руководством по среда выполнения Java.

**Функциональное изменение**

- Предоставление дополнительных сведений об ошибках подключения.

**Критические изменения**

- В Java (Android) функция `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` больше не требует параметр пути. Теперь путь автоматически определяется на всех поддерживаемых платформах.
- Функция доступа get для свойства `EndpointUrl` в Java и C# была удалена.

**Исправления ошибок**

- Теперь в Java реализован результат аудиосинтеза на распознавателе перевода.
- Исправлена ошибка, которая могла привести к появлению неактивных потоков и увеличению числа открытых и неиспользуемых сокетов.
- Исправлена проблема, при которой длительное распознавание могло завершиться в середине передачи.
- Исправлено состояние гонки при завершении работы распознавателя.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Пакет SDK для службы "Речь" в Cognitive Services 0.5.0: выпуск за июль 2018 г.

**Новые функции**

- Поддержка платформы Android (API 23: Android 6.0 Marshmallow или более поздней версии). Ознакомьтесь с [кратким руководством для Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Поддержка платформы .NET Standard 2.0 в Windows. Ознакомьтесь с [кратким руководством для .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Экспериментальная функция: поддержка UWP в Windows (версия 1709 или более поздняя версия).
  - Ознакомьтесь с [кратким руководством по UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Примечание. Приложения UWP, созданные с использованием пакета SDK для службы "Речь", еще не прошли сертификацию WACK (комплект сертификации приложений для Windows).
- Поддержка длительного распознавания с автоматическим переподключением.

**Функциональные изменения**

- `StartContinuousRecognitionAsync()` поддерживает длительное распознавание.
- Результат распознавания содержит дополнительные поля. Это смещение от начала звука и длительность (в тактах) распознанного текста, а также дополнительные значения, представляющие состояние распознавания, например `InitialSilenceTimeout` и `InitialBabbleTimeout`.
- Поддержка AuthorizationToken для создания экземпляров фабрики.

**Критические изменения**

- События распознавания: `NoMatch` Тип события был объединен с `Error` событием.
- Спичаутпутформат в C# был переименован в `OutputFormat` , чтобы остаться в соответствии с C++.
- Тип возвращаемого значения некоторых методов интерфейса `AudioInputStream` немного изменен.
  - В Java метод `read` теперь возвращает `long` вместо `int`.
  - В C# метод `Read` теперь возвращает `uint` вместо `int`.
  - В C++ методы `Read` и `GetFormat` теперь возвращают `size_t` вместо `int`.
- C++: экземпляры входных аудиопотоков теперь могут передаваться только как `shared_ptr`.

**Исправления ошибок**

- Исправлены неправильные возвращаемые значения в результате после истечения времени ожидания `RecognizeAsync()`.
- Удалена зависимость от библиотек Media Foundation в Windows. Теперь пакет SDK использует интерфейсы API Core Audio.
- Исправление документации: добавлена страница с описанием поддерживаемых [регионов](regions.md).

**Известная проблема**

- Пакет SDK для службы "Речь" не передает результаты синтеза речи для перевода. Эта проблема будет устранена в следующем выпуске.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Пакет SDK для службы речи в Cognitive Services 0.4.0: выпуск за июнь 2018 г.

**Функциональные изменения**

- AudioInputStream

  Распознаватель теперь может использовать поток в качестве источника звука. Дополнительные сведения см. в соответствующем [практическом руководстве](how-to-use-audio-input-streams.md).

- Подробный формат выходных данных

  При создании `SpeechRecognizer` можно запросить формат выходных данных `Detailed` или `Simple`. `DetailedSpeechRecognitionResult` содержит оценку достоверности, распознанный текст, необработанную лексическую форму, нормализованную форму и нормализованную форму с маской ненормативной лексики.

**Критическое изменение**

- В C# `SpeechRecognitionResult.RecognizedText` заменено на `SpeechRecognitionResult.Text`.

**Исправления ошибок**

- Устранена проблема с возможным обратным вызовом в слое USP во время завершения работы.
- Если распознаватель использует входной аудиофайл, он удерживает дескриптор файла дольше, чем необходимо.
- Устранены различные взаимоблокировки между генератором сообщений и распознавателем.
- Срабатывает результат `NoMatch` по истечении времени ожидания ответа от службы.
- Библиотеки Media Foundation в Windows загружаются с задержкой. Эта библиотека требуется только для ввода с микрофона.
- Скорость передачи для аудиоданных ограничена примерно в два раза относительно исходной скорости.
- В Windows сборки .NET для C# теперь имеют строгие имена.
- Исправление документации: `Region` — обязательная информация для создания распознавателя.

Добавлены дополнительные примеры, которые постоянно обновляются. Актуальный набор примеров доступен в [репозитории GitHub с примерами пакета SDK для службы "Речь"](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Пакет SDK для службы речи в Cognitive Services 0.2.12733: выпуск за май 2018 г.

Это первый выпуск общедоступной предварительной версии пакета SDK для службы "Речь" в Cognitive Services.
