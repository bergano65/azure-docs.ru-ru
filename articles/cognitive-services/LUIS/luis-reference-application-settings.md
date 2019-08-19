---
title: Параметры приложения — LUIS
titleSuffix: Azure Cognitive Services
description: Общие сведения о параметрах приложений для определения языка приложения.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 6d516590b90efd937789fa0847b707d2521f6459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932583"
---
# <a name="application-settings"></a>Параметры приложения

Эти параметры приложения хранятся в [экспортированном](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) приложении и [обновляются](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) с помощью интерфейсов API. При изменении параметров версии приложения состояние обучения приложения сбрасывается на "не обучено".

|Параметр|Значение по умолчанию|Примечания|
|--|--|--|
|нормализепунктуатион|True|Удаляет знаки препинания.|
|NormalizeDiacritics|True|Удаляет диакритические знаки.|

## <a name="diacritics-normalization"></a>Нормализация диакритических знаков 

Включите нормализацию utterance для диакритических знаков в файле приложения Luis JSON в `settings` параметре.

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

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Португальская `pt-br` бразильская диакритические знаки

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

#### <a name="dutch-nl-nl-diacritics"></a>Голландская `nl-nl` диакритические знаки

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

#### <a name="french-fr--diacritics"></a>Канадские `fr-` диакритические знаки

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

#### <a name="german-de-de-diacritics"></a>Знаки `de-de` на немецком языке

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

#### <a name="spanish-es--diacritics"></a>Испанские `es-` диакритические знаки

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

Включите нормализацию utterance для знаков препинания в файле приложения Luis JSON в `settings` параметре.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

В следующем фразы продолжительностью показано, как диакритические знаки влияют на фразы продолжительностью:

|Если диакритические знаки имеют значение false|Если диакритические знаки имеют значение true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Пунктуация удалена

Следующие знаки пунктуации удаляются `NormalizePunctuation` с параметром, установленным в значение true.

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
