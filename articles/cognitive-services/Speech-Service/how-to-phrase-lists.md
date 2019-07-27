---
title: Списки фраз — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как предоставить речевые службы со списком фраз с помощью `PhraseListGrammar` объекта, чтобы улучшить результаты распознавания речи в текст.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562774"
---
# <a name="phrase-lists-for-speech-to-text"></a>Списки фраз для преобразования речи в текст

Предоставляя речевые службы со списком фраз, вы можете улучшить точность распознавания речи. Списки фраз используются для определения известных фраз в звуковых данных, например имени пользователя или определенного местоположения.

Например, если у вас есть команда "переместить в" и потенциальное место назначения "назад", можно добавить запись "перейти к назад". Добавление фразы увеличит вероятность того, что при распознавании звукового сигнала будет располагаться "переместить в" назад "вместо" переместить в ".

В список фраз можно добавить отдельные слова или полные фразы. Во время распознавания в списке фраз используется запись, если точное совпадение включено в звук. При построении на предыдущем примере, если список фраз содержит слово «перейти к нам», а захваченная фраза «переходит к медленной», то результат распознавания будет «переход к началу медленно».

>[!Note]
> В настоящее время списки фраз поддерживают только английский язык для преобразования речи в текст.

## <a name="how-to-use-phrase-lists"></a>Использование списков фраз

В примерах ниже показано, как создать список фраз с помощью `PhraseListGrammar` объекта.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Максимальное число списков фраз, которое будет использоваться службой речи для сопоставления речи, — 1024 словосочетания.

Можно также очистить фразы, связанные с `PhraseListGrammar` , вызвав Clear ().

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> `PhraseListGrammar` Изменения объекта вступают в силу при следующем распознавании или после повторного подключения к голосовым службам.

## <a name="next-steps"></a>Следующие шаги

* [Справочная документация по пакету SDK для распознавания речи](speech-sdk.md)
