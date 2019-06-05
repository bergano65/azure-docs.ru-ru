---
title: Списки фразу - службы распознавания речи
titlesuffix: Azure Cognitive Services
description: Узнайте, как для предоставления служб речи со фразы список с помощью `PhraseListGrammar` объекта, чтобы улучшить результаты распознавания речи в текст.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: a3be5d28ebe394771a2d8b492f1f6a9c8a82fb9e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515302"
---
# <a name="phrase-lists-for-speech-to-text"></a>Списки фразы для распознавания речи в текст

Предоставляя служб речи со списком фразы, можно повысить точность распознавания речи. Списки фразы используются для поиска известных фраз в звуковых данных, такие как имя пользователя или определенную папку.

Например, если у вас есть команда «Перейти к» и возможных назначение «Ворд», который может быть произнесена, можно добавить запись «Переместить в Ворд». Добавление фразы увеличит вероятность того, когда аудио распознается распознавания «Переместить в Ворд» вместо «Движение в сторону».

Отдельных слов и фраз можно добавить в список фраз. Во время распознавания запись в списке фразу используется, если точное соответствие включается в аудио. Построение в предыдущем примере, если список фразу включает «Переместить в Ворд» и записанные фразу «Продвинуться медленно», а затем результат распознавания будет «Переместить Ворд медленно».

## <a name="how-to-use-phrase-lists"></a>Как использовать списки фразы

В приведенных ниже примерах демонстрируется построение списка фраз с помощью `PhraseListGrammar` объекта.

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
> Максимальное число фразу списков, будет использовать служба распознавания речи в соответствии с речи — 1024 фраз.

Также можно очищать фраз, связанных с `PhraseListGrammar` путем вызова clear().

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
> Изменения в `PhraseListGrammar` объекта силу на следующий распознавания или после повторного подключения служб речи.

## <a name="next-steps"></a>Дальнейшие действия

* [Справочная документация Speech SDK](speech-sdk.md)
