---
title: Параметры приложения
titleSuffix: Azure Cognitive Services
description: Понимаете, какие параметры приложения для приложений понимание языка.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: c19d328c3a5f4dd6dbe14ca94809e42c5655ea72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391148"
---
# <a name="application-settings"></a>Параметры приложения

Эти параметры приложения хранятся в [экспортировать](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) приложения и [обновлены](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) с REST API. Изменение параметров приложения версии сбрасывает состояние обучение приложения необученную.

|Параметр|Значение по умолчанию|Примечания|
|--|--|--|
|NormalizePunctuation|Истина|Удаляет знаки препинания.|
|NormalizeDiacritics|Истина|Удаляет диакритические знаки.|

## <a name="diacritics-normalization"></a>Нормализация диакритические знаки 

Включите нормализации utterance диакритических знаков в файл приложения LUIS JSON `settings` параметра.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Следующие фразы Показать, как диакритические знаки нормализации влияет на фразы:

|С диакритическими знаками, значение false|Значение true диакритические знаки|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Языковая поддержка диакритические знаки

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Португальский (Бразилия) `pt-br` диакритические знаки

|Диакритические знаки, значение false|Диакритические знаки, значение false|
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

#### <a name="dutch-nl-nl-diacritics"></a>Нидерландский `nl-nl` диакритические знаки

|Диакритические знаки, значение false|Диакритические знаки, значение false|
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

Сюда входят языки и региональные параметры как французский "и" Канады.

|Диакритические знаки, значение false|Диакритические знаки, значение false|
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

#### <a name="german-de-de-diacritics"></a>Немецкий `de-de` диакритические знаки

|Диакритические знаки, значение false|Диакритические знаки, значение false|
|--|--|
|`ä`|`a`|
|`ö `|`o`| 
|`ü `|`u`| 

#### <a name="italian-it-it-diacritics"></a>Итальянский `it-it` диакритические знаки

|Диакритические знаки, значение false|Диакритические знаки, значение false|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó `|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Испанский `es-` диакритические знаки

Сюда входят Мексика испанский и Канады.

|Диакритические знаки, значение false|Диакритические знаки, значение false|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó `|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Знаки препинания нормализации

Включите utterance нормализации для знаков препинания в файл приложения LUIS JSON `settings` параметра.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Следующие фразы Показать, как диакритические знаки влияет на фразы:

|С диакритическими знаками, значение False|С диакритическими знаками, присвоено значение True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Удаленными знаками пунктуации

Следующие знаки препинания удаляется с `NormalizePunctuation` задано значение true.

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
