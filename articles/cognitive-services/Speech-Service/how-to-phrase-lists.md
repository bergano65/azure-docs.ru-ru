---
title: Списки фраз — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как предоставить речевой службе список фраз с помощью объекта `PhraseListGrammar`, чтобы улучшить результаты распознавания речи в текст.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f84ea6b2b0f1e8246e1d765e54f663cd01f29b1d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560853"
---
# <a name="phrase-lists-for-speech-to-text"></a>Списки фраз для преобразования речи в текст

Предоставляя службе распознавания речи список фраз, вы можете улучшить точность распознавания речи. Списки фраз используются для определения известных фраз в звуковых данных, например имени пользователя или определенного местоположения.

Например, если у вас есть команда "переместить в" и потенциальное место назначения "назад", можно добавить запись "перейти к назад". Добавление фразы увеличит вероятность того, что при распознавании звукового сигнала будет располагаться "переместить в" назад "вместо" переместить в ".

В список фраз можно добавить отдельные слова или полные фразы. Во время распознавания запись в списке фраз используется, если точное совпадение для целой фразы включено в звук в виде отдельной фразы. Если точное совпадение с фразой не найдено, распознавание не используется.

>[!Note]
> В настоящее время списки фраз поддерживают только английский язык для преобразования речи в текст.

## <a name="how-to-use-phrase-lists"></a>Использование списков фраз

В приведенных ниже примерах показано, как создать список фраз с помощью объекта `PhraseListGrammar`.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Максимальное число списков фраз, которое будет использоваться службой речи для сопоставления речи, — 1024 словосочетания.

Также можно удалить фразы, связанные с `PhraseListGrammar`, вызвав Clear ().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Изменения объекта `PhraseListGrammar` вступают в силу при следующем распознавании или после повторного подключения к службе речи.

## <a name="next-steps"></a>Следующие шаги

* [Справочная документация по пакету SDK для распознавания речи](speech-sdk.md)
