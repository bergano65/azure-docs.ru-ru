---
title: Параметры приложения — LUIS
titleSuffix: Azure Cognitive Services
description: Параметры приложений для языка Cognitive Services Azure "Общие сведения о приложениях" хранятся в приложении и на портале.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: bae4f09b539e26ca8c0d4ce97999776dc0911601
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961788"
---
# <a name="application-settings"></a>Параметры приложения

Эти параметры приложения хранятся в [экспортированном](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) приложении и [обновляются](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) с помощью интерфейсов API. При изменении параметров версии приложения состояние обучения приложения сбрасывается на "не обучено".

|Настройка|Значение по умолчанию|Примечания|
|--|--|--|
|нормализепунктуатион|True,|Удаляет знаки препинания.|
|NormalizeDiacritics|True,|Удаляет диакритические знаки.|

## <a name="diacritics-normalization"></a>Нормализация диакритических знаков 

Включите нормализацию utterance для диакритических знаков в файле приложения LUIS JSON в параметре `settings`.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

В следующем фразы продолжительностью показано, как нормализация диакритических знаков влияет на фразы продолжительностью:

|Если диакритические знаки имеют значение false|Если диакритические знаки имеют значение true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Языковая поддержка диакритических знаков

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Португальский (Бразилия) `pt-br` диакритические знаки

|Диакритические знаки имеют значение false|Диакритические знаки имеют значение true|
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

#### <a name="dutch-nl-nl-diacritics"></a>Голландский `nl-nl` диакритические знаки

|Диакритические знаки имеют значение false|Диакритические знаки имеют значение true|
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

#### <a name="french-fr--diacritics"></a>Французский `fr-` диакритические знаки

К ним относятся французские и канадские языки и региональные параметры.

|Диакритические знаки имеют значение false|Диакритические знаки имеют значение true|
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

#### <a name="german-de-de-diacritics"></a>`de-de` знаков на немецком языке

|Диакритические знаки имеют значение false|Диакритические знаки имеют значение true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Итальянские `it-it` диакритические знаки

|Диакритические знаки имеют значение false|Диакритические знаки имеют значение true|
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

#### <a name="spanish-es--diacritics"></a>Испанский `es-` диакритические знаки

Сюда входит испанский и канадский Мексики.

|Диакритические знаки имеют значение false|Диакритические знаки имеют значение true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Нормализация знаков препинания

Включите нормализацию utterance для знаков препинания в файле приложения LUIS JSON в параметре `settings`.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

В следующем фразы продолжительностью показано, как пунктуация влияет на фразы продолжительностью:

|Если для пунктуации задано значение false|Если для пунктуации задано значение true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Пунктуация удалена

Следующие знаки пунктуации удаляются с `NormalizePunctuation` имеет значение true.

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
