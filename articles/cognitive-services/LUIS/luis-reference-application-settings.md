---
title: Настройки приложения - LUIS
description: Настройки приложений для приложений для понимания языка Azure Cognitive Services хранятся в приложении и портале.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382200"
---
# <a name="application-settings"></a>Параметры приложения

Эти настройки приложения хранятся в [экспортируемом](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) приложении и [обновляются](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) с помощью APIs REST. Изменение настроек версии приложения сбрасывает статус обучения приложения на неподготовленный.

[Изучите концепции](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) диакритики и пунктуации.

|Параметр|Значение по умолчанию|Примечания|
|--|--|--|
|НормализацияПунктуация|True|Удаляет пунктуацию.|
|NormalizeDiacritics|True|Удаляет диакритику.|

## <a name="diacritics-normalization"></a>Нормализация диакритических

Включите нормализацию высказываний для диакритики в `settings` файл приложения LUIS JSON в параметре.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Следующие высказывания показывают, как нормализация диакритических веществ влияет на высказывания:

|С диакритикой установлен на ложные|С диакритики установить на истину|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Языковая поддержка диакритики

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Бразильский португальский `pt-br` диакритики

|Диакритика установлена на ложную|Диакритика установлена на истину|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Голландский `nl-nl` диакритика

|Диакритика установлена на ложную|Диакритика установлена на истину|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Французская `fr-` диакритика

Это включает в себя как французские, так и канадские субкультуры.

|Диакритика установлена на ложную|Диакритика установлена на истину|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Немецкая `de-de` диакритика

|Диакритика установлена на ложную|Диакритика установлена на истину|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Итальянская `it-it` диакритика

|Диакритика установлена на ложную|Диакритика установлена на истину|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Испанская `es-` диакритика

Это включает в себя как испанский и канадский мексиканский.

|Диакритика установлена на ложную|Диакритика установлена на истину|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Нормализация пунктуации

Включите нормализацию высказываний для пунктуации к файлу `settings` приложения LUIS JSON в параметре.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

Следующие высказывания показывают, как пунктуация влияет на высказывания:

|С пунктуацией, установленной на ложный|С пунктуацией, установленной на True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Пунктуация удалена

Следующие знаки препинания `NormalizePunctuation` удаляется с устанавливается на истину.

|Пунктуация|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

## <a name="next-steps"></a>Следующие шаги

* [Изучите концепции](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) диакритики и пунктуации.
