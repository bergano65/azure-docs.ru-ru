---
title: Списки фраз - Речевая служба
titleSuffix: Azure Cognitive Services
description: Узнайте, как предоставить службе речи `PhraseListGrammar` список фраз с помощью объекта для улучшения результатов распознавания речи в тексте.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5a21358edae4c61f35993770c22634da9ac83633
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401968"
---
# <a name="phrase-lists-for-speech-to-text"></a>Списки фраз для речи к тексту

Предоставляя службе речи список фраз, вы можете повысить точность распознавания речи. Списки фраз используются для определения известных фраз в звуковых данных, например имени пользователя или определенного местоположения.

Например, если у вас есть команда "Движение" и возможный пункт назначения "Ward", который может быть произнесен, вы можете добавить запись "Движение к Уорду". Добавление фразы увеличит вероятность того, что при признании звука, "Движение к Уорду" будет распознано, а не "Движение к".

В список фраз можно добавить отдельные слова или полные фразы. Во время распознавания используется запись в списке фраз, если точная совпадение для всей фразы включено в аудио в качестве отдельной фразы. Если точное совпадение с фразой не найдено, распознавание не происходит.

>[!Note]
> В настоящее время фразовые списки поддерживают только английский язык для речевого текста.

## <a name="how-to-use-phrase-lists"></a>Как использовать списки фраз

Приведенные ниже примеры иллюстрируют, `PhraseListGrammar` как создать список фраз с помощью объекта.

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
> Максимальное количество фразовых списков, которые служба речевой связи будет использовать для сопоставления речи, составляет 1024 фразы.

Вы также можете очистить фразы, связанные `PhraseListGrammar` с вызовом ясно().

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
> Изменения в `PhraseListGrammar` объекте вступают в силу при следующем распознавании или после повторного подключения к службе речи.

## <a name="next-steps"></a>Дальнейшие действия

* [Справочная документация SDK речи](speech-sdk.md)
