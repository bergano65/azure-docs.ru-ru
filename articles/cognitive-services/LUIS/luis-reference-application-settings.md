---
title: Настройки приложения - LUIS
titleSuffix: Azure Cognitive Services
description: Настройки приложений для приложений для понимания языка Azure Cognitive Services хранятся в приложении и портале.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270804"
---
# <a name="application-settings"></a>Параметры приложения

Эти настройки приложения хранятся в [экспортируемом](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) приложении и [обновляются](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) с помощью APIs REST. Изменение настроек версии приложения сбрасывает статус обучения приложения на неподготовленный.

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
