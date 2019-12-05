---
title: Списки фраз — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как предоставить речевой службе список фраз с помощью объекта `PhraseListGrammar`, чтобы улучшить результаты распознавания речи в текст.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 052e02ef562da0637b6b5b9683120f0c397dbfd5
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805881"
---
# <a name="phrase-lists-for-speech-to-text"></a>Списки фраз для преобразования речи в текст

Предоставляя службе распознавания речи список фраз, вы можете улучшить точность распознавания речи. Списки фраз используются для определения известных фраз в звуковых данных, например имени пользователя или определенного местоположения.

Например, если у вас есть команда "переместить в" и потенциальное место назначения "назад", можно добавить запись "перейти к назад". Добавление фразы увеличит вероятность того, что при распознавании звукового сигнала будет располагаться "переместить в" назад "вместо" переместить в ".

В список фраз можно добавить отдельные слова или полные фразы. Во время распознавания в списке фраз используется запись, если точное совпадение включено в звук. При построении на предыдущем примере, если список фраз содержит слово «перейти к нам», а записываемый звук звучит достаточно для «перемещения в сторону» и «перейти к началу», то результат распознавания скорее всего будет располагаться как «переход на более медленное».

>[!Note]
> В настоящее время списки фраз поддерживают только английский язык для преобразования речи в текст.

## <a name="how-to-use-phrase-lists"></a>Использование списков фраз

В приведенных ниже примерах показано, как создать список фраз с помощью объекта `PhraseListGrammar`.

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

Также можно удалить фразы, связанные с `PhraseListGrammar`, вызвав Clear ().

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
> Изменения объекта `PhraseListGrammar` вступают в силу при следующем распознавании или после повторного подключения к службе речи.

## <a name="next-steps"></a>Дальнейшие действия

* [Справочная документация по пакету SDK для распознавания речи](speech-sdk.md)
