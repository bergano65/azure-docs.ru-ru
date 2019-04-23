---
title: Глоссарий — служба API распознавания лиц
titleSuffix: Azure Cognitive Services
description: В глоссарии объясняются термины, с которыми вы можете столкнуться при работе со службой API распознавания лиц.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: bd4285a2d0f882b8c766563f2304031f1f6e2898
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787952"
---
# <a name="glossary"></a>Глоссарий

## <a name="a"></a>A

#### <a name="attributes"></a>Атрибуты

Атрибуты являются функции необязательно лиц, которые могут быть обнаружены, такие как [возраст](#age-attribute), [Пол](#gender-attribute), [головного поза](#head-pose-attribute), [улыбка](#facial-hair-attribute)и [одобрения](#smile-attribute). Их можно получить из API обнаружения, указав _returnFaceAttributes_ параметр запроса.

Полный список лицевых атрибутов см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Возраст (атрибут)

Возраст — один из [атрибутов](#attributes), описывающий возраст конкретного лица. Атрибут возраста является необязательным в результатах обнаружения. Его можно настроить в запросе на обнаружение, указав параметр returnFaceAttributes.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="c"></a>C

#### <a name="candidate"></a>Кандидат

Кандидаты — это результаты [идентификации](#identification) (например, идентифицированные люди и уровень достоверности их определения). Кандидат представлен значением [PersonID](#person-id) и [достоверностью](#confidence), указывающей, что человек идентифицирован с высоким уровнем достоверности.

Дополнительные сведения см. в справочной документации: [Сталкиваются - Идентификация](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Достоверность

Достоверность — это мера сходства между [лицами](#face) или [человеком](#person) в числовом выражении, которое используется при [идентификации](#identification) и [проверке](#verification). Достоверность указывает схожесть результатов после поиска, идентификации и проверки.

Дополнительные сведения см. в справочной документации: [Face — Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face — Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Обнаружение, обнаружение лиц

Обнаружение лиц — это процесс обнаружения лиц на изображениях. Пользователи могут передать изображение и указать его URL-адрес в запросе. Обнаруженные лица возвращаются с [идентификаторами лиц](#face-id), обеспечивающими уникальную идентификацию в API распознавания лиц. Прямоугольные рамки указывают расположение лиц на изображении в пикселях, а также необязательные [атрибуты](#attributes) для каждого лица, такие как [возраст](#age-attribute), [пол](#gender-attribute), [положение головы](#head-pose-attribute), [растительность на лице](#facial-hair-attribute) и [улыбка](#smile-attribute).

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>Определение эмоций (атрибут)

Определение эмоций является одним из [лицевым атрибутам](#attributes). При запросе, возвращается список эмоции и их определение достоверности для данного лица. Нормализуются достоверности: оценок по всем эмоции добавит больше одного. Распознаваемые эмоции возвращаются, счастье, печаль, neutral, гнев, презрение, отвращение, неожиданные и страха.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="f"></a>F

#### <a name="face"></a>API распознавания лиц

Элемент Face — это общее название результатов, полученных от API распознавания лиц и связанных с обнаруженными лицами. В конечном счете, лиц представленного единой идентификации ([Face ID](#face-id)), указанную область в образы ([лицо в рамке](#face-rectangle)) и очень лиц-атрибуты, связанные с, таких как [возраст](#age-attribute), [Пол](#gender-attribute), ориентиров и [головного поза](#head-pose-attribute). Кроме того, лица могут быть возвращены в результате обнаружения.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>API распознавания лиц

API распознавания лиц — это облачный API, который предоставляет наиболее эффективные алгоритмы определения и распознавания лиц. Основные функции API распознавания лиц можно разделить на две категории: обнаружение лиц с атрибутами и [распознавание](#recognition) лиц.

Дополнительные сведения см. в справочной документации: [Что такое API распознавания лиц Azure?](./Overview.md), [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face — Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face — Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face — Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Атрибуты лица

Ознакомьтесь с [атрибутами](#attributes).

#### <a name="face-id"></a>Идентификатор лица

Идентификатор лица извлекается из результатов обнаружения, в которых строка представляет [лицо](#face) в [API распознавания лиц](#face-api).

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Ориентиры лица, лицевые ориентиры

Ориентиры не обязательно входят в результаты обнаружения. Это семантические точки лица, например глаза, нос и рот (как показано на следующем рисунке). Ориентирами можно управлять, указывая в запросе на обнаружение логическое значение returnFaceLandmarks. Если returnFaceLandmarks имеет значение true, то возвращаемые лица будут содержать атрибуты ориентиров.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Прямоугольная рамка вокруг лица

Прямоугольная рамка вокруг лица извлекается из результатов обнаружения. Это вертикальный прямоугольник (с указанием левой и верхней границ, ширины и высоты) на изображениях, указанный в пикселях. Левый верхний угол [лица](#face) (слева, вверху), помимо ширины и высоты, указывает размер лица в координатах X и Y соответственно.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Растительность на лице (атрибут)

Растительность на лице является одним из [атрибутов](#attributes), используемых для описания длины растительности на лице. Атрибут растительности на лице является необязательным в результатах обнаружения. Его можно настроить в запросе на обнаружение, указав параметр returnFaceAttributes. Если returnFaceAttributes содержит facialHair, возвращенные лица будут содержать атрибуты растительности на лице.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList — это коллекция [PersistedFace](#persistedface). FaceList представляет собой единицу в операции [поиска похожих лиц](#find-similar). FaceList имеет [идентификатор FaceList](#facelist-id) и другие атрибуты, такие как имя и данные пользователя.

Дополнительные сведения см. в справочной документации: [FaceList — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>Идентификатор FaceList

Идентификатор FaceList — это предоставляемая пользователем строка, используемая в качестве идентификатора [FaceList](#facelist). Идентификатор FaceList должен быть уникальным в пределах подписки.

Дополнительные сведения см. в справочной документации: [FaceList — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Поиск похожих лиц

API Find Similar используется для поиска и запроса похожих лиц в коллекции лиц. Запрашиваемые лица и коллекции лиц представлены в запросе [идентификаторами лиц](#face-id) или [идентификатором FaceList](#facelist-id)/[LargeFaceList](#largefacelist-id). В возвращенных результатах указываются найденные похожие лица, представленные с помощью [идентификаторов лиц](#face-id) или идентификаторов PersistedFace.

Дополнительные сведения см. в справочной документации: [Face — Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G.

#### <a name="gender-attribute"></a>Пол (атрибут)

Пол является одним из [атрибутов](#attributes), используемых для описания половой принадлежности лиц. Атрибут пола является необязательным в результатах обнаружения. Его можно настроить в запросе на обнаружение, указав параметр returnFaceAttributes. Если returnFaceAttributes содержит gender, возвращенные лица будут содержать атрибуты пола.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Группирование

Группирование лиц — это объединение коллекции лиц согласно сходству. Коллекции лиц в запросе задаются с помощью коллекций идентификаторов лиц. В результате группирования похожие лица объединяются в [группы](#groups), а лица, которые не похожи ни на какое другое лицо, объединяются в разрозненную группу. В результате группирования создается не более одной [разрозненной группы](#messy-group).

Дополнительные сведения см. в справочной документации: [Сталкиваются - группы](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Группы

Группы получаются в результате [группирования](#grouping). Каждая группа содержит коллекцию похожих лиц, указанных с помощью [идентификаторов лиц](#face-id).

Дополнительные сведения см. в справочной документации: [Сталкиваются - группы](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>Положение головы (атрибут)

Положение головы является одним из [атрибутов](#attributes), представляющим положение лица в трехмерном пространстве в соответствии с углами вращения, наклона и скоса, как показано на следующем рисунке. Диапазоны значений углов вращения и скоса составляют от –180 до 180 и от –90 до 90 градусов. В текущей версии значение наклона, возвращаемое после обнаружения, всегда равно 0. Атрибут положения головы является необязательным в результатах обнаружения. Его можно настроить в запросе на обнаружение, указав параметр returnFaceAttributes. Если параметр returnFaceAttributes содержит headPose, возвращенные лица будут содержать атрибуты положения головы.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Идентификация

Операция Identification означает определение одного или нескольких лиц в LargePersonGroup или PersonGroup.
[PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup) — это коллекция элементов [Person](#person).
Элементы Face, LargePersonGroup и PersonGroup представлены в запросе [идентификаторами лиц](#face-id) и [идентификаторами LargePersonGroup](#largepersongroup-id)/[PersonGroup](#persongroup-id) соответственно.
Идентифицированные результаты являются [кандидатами](#candidate), представленными элементами [Person](#person) с определенной степенью достоверности.
Несколько лиц во входных данных обрабатываются отдельно, и для каждого из них будет получен отдельный результат.

> [!NOTE]
> Перед идентификацией необходимо успешно выполнить обучение LargePersonGroup и PersonGroup. Если обучение LargePersonGroup и PersonGroup не выполнено или находится в [состоянии](#status-train), отличном от успешного (т. е. выполняется, произошла ошибка или истекло время ожидания), то ответом на запросу будет код 400.
> 

Дополнительные сведения см. в справочной документации: [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical — это логическое поле в результатах [проверки](#verification), указывающее, принадлежат ли два лица одному человеку.

Дополнительные сведения см. в справочной документации: [Сталкиваются - проверка](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="l"></a>L

#### <a name="landmarks"></a>Ориентиры

Ознакомьтесь с ориентирами лица.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList — это коллекция [PersistedFace](#persistedface). LargeFaceList представляет собой единицу в операции [поиска похожих лиц](#find-similar). LargeFaceList имеет [идентификатор LargeFaceList](#largefacelist-id) и другие атрибуты, такие как имя и данные пользователя.

Дополнительные сведения см. в справочной документации: [LargeFaceList — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList — List Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>Идентификатор LargeFaceList

Идентификатор LargeFaceList — это предоставляемая пользователем строка, используемая в качестве идентификатора [LargeFaceList](#largefacelist). Идентификатор LargeFaceList должен быть уникальным в пределах подписки.

Дополнительные сведения см. в справочной документации: [LargeFaceList — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup — это коллекция элементов [Person](#person). LargePersonGroup представляет собой единицу в операции [идентификации](#identification). LargePersonGroup имеет [идентификатор LargePersonGroup](#largepersongroup-id) и другие атрибуты, такие как имя и данные пользователя.

Дополнительные сведения см. в справочной документации: [LargePersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup Person — List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>Идентификатор LargePersonGroup

Идентификатор LargePersonGroup — это предоставляемая пользователем строка, используемая в качестве идентификатора [LargePersonGroup](#largepersongroup). Идентификатор LargePersonGroup должен быть уникальным в пределах подписки.

Дополнительные сведения см. в справочной документации: [LargePersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Разрозненная группа

Разрозненная группа создается на основе результатов [группирования](#grouping). Она содержит лица, не похожие ни на одно другое лицо. Каждое лицо в разрозненной группе обозначается [идентификатором лица](#face-id).

Дополнительные сведения см. в справочной документации: [Сталкиваются - группы](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>Нет

#### <a name="name-person"></a>Имя (элемент Person)

Имя является строкой понятное описательное для [Person](#person). В отличие от [идентификатора Person](#person-id), имена людей в пределах группы могут дублироваться.

Дополнительные сведения см. в справочной документации: [LargePersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Имя (LargePersonGroup или PersonGroup)

Имя также является понятным описательная строка для [LargePersonGroup](#largepersongroup)/[каждой группе людей](#persongroup). В отличие от [идентификатора LargePersonGroup](#largepersongroup-id)/[PersonGroup](#persongroup-id), имена LargePersonGroup и PersonGroup могут дублироваться в пределах подписки.

Дополнительные сведения см. в справочной документации: [LargePersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace — это структура данных в API распознавания лиц. Состав PersistedFace [PersistedFace идентификатор](#persisted-face-id), а также другие атрибуты, такие как имя и данные пользователя.

Дополнительные сведения см. в справочной документации: [LargeFaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="persisted-face-id"></a>Сохраненные Face ID

Сохраняются Face ID создается, если [PersistedFace](#persistedface) успешно создан. Создается строка для представления этого элемента Face в [API распознавания лиц](#face-api).

Дополнительные сведения см. в справочной документации: [LargeFaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Person

Элемент Person — это структура данных, управляемая в API распознавания лиц. Элемент Person имеет [идентификатор Person](#person-id) и другие атрибуты, такие как имя, коллекция [PersistedFace](#persistedface) и данные пользователя.

Дополнительные сведения см. в справочной документации: [LargePersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>Идентификатор Person

Идентификатор Person создается при успешном создании [Person](#person). Создается строка для представления этого элемента Person в [API распознавания лиц](#face-api).

Дополнительные сведения см. в справочной документации: [LargePersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup.

PersonGroup — это коллекция элементов [Person](#person). PersonGroup представляет собой единицу в операции [идентификации](#identification). PersonGroup имеет [идентификатор PersonGroup](#persongroup-id) и другие атрибуты, такие как имя и данные пользователя.

Дополнительные сведения см. в справочной документации: [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup Person — List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>Идентификатор PersonGroup

Идентификатор PersonGroup — это предоставляемая пользователем строка, используемая в качестве идентификатора [PersonGroup](#persongroup). Идентификатор PersonGroup должен быть уникальным в пределах подписки.

Дополнительные сведения см. в справочной документации: [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup — Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Положение (атрибут)

Ознакомьтесь с [положением головы](#head-pose-attribute).

## <a name="r"></a>R

#### <a name="recognition"></a>Свидетельство

Распознавание представляет собой популярную область приложений, использующих технологии распознавания лиц, такие как [поиск похожих лиц](#find-similar), [группирование](#grouping), [идентификация](#identification),[проверка двух лиц на принадлежность одному человеку](#verification).

Дополнительные сведения см. в справочной документации: [Face — Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face — Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face — Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Прямоугольник (Face)

Ознакомьтесь с [прямоугольной рамкой вокруг лица](#face-rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Поиск похожих лиц

Ознакомьтесь с [поиском похожих лиц](#find-similar).

#### <a name="smile-attribute"></a>Улыбка (атрибут)

Улыбка является одним из [атрибутов](#attributes), используемых для описания улыбающихся выражений на лицах. Атрибут улыбки является необязательным в результатах обнаружения. Его можно настроить в запросе на обнаружение, указав параметр returnFaceAttributes. Если returnFaceAttributes содержит smile, возвращенные лица будут содержать атрибуты улыбки.

Дополнительные сведения см. в справочной документации: [Лиц — обнаруживать](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Снимок

Моментальный снимок является временным удаленным хранилищем для определенных типов данных API распознавания лиц. Он функционирует как своего рода буфер обмена для копирования данных из одной подписки в другую. Сначала пользователь "делает" моментальный снимок данных в исходной подписке, а затем "применяет" его к новому объекту данных в целевой подписке. 

Дополнительные сведения см. в [руководстве по миграции службы "Распознавание лиц"](./face-api-how-to-topics/how-to-migrate-face-data.md), а также в справочной документации по [созданию](/rest/api/cognitiveservices/face/snapshot/take) и [применению моментальных снимков](/rest/api/cognitiveservices/face/snapshot/apply) (REST).

#### <a name="status-train"></a>Состояние (обучение)

Состояние является строкой, используемой для описания процедуры обучения LargeFaceList, LargePersonGroup или PersonGroup. Возможные состояния: notstarted, running, succeeded, failed.

Дополнительные сведения см. в справочной документации: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [каждой группе людей — Обучение](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Ключ подписки

Ключ подписки — это строка, которую необходимо указать в качестве параметра строки запроса для вызова любого API распознавания лиц. Ключ подписки можно найти на странице "My Subscriptions" (Мои подписки) после входа на портал Microsoft Cognitive Services. С каждой подпиской связаны два ключа: один первичный ключ и один вторичный ключ. Оба эти ключа можно использовать для вызова API. Необходимо обеспечить безопасность ключей подписки. Кроме того, в любой момент их можно создать повторно на странице "My Subscriptions" (Мои подписки).

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Обучение (LargePersonGroup, LargeFaceList, PersonGroup)

API Train используется для предварительной обработки [LargeFaceList](#largefacelist)/[LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup) для обеспечения производительности операций [поиска похожих лиц](#find-similar)/[идентификации](#identification). Если обучение не выполнено или [состояние обучения](#status-train) не является успешным, то идентификация с помощью этого объекта PersonGroup приведет к ошибке.

Дополнительные сведения см. в справочной документации: [LargeFaceList — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>Данные пользователя

Данные пользователя — это дополнительные сведения для элементов [Person](#person) и [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup). Данные пользователя задают пользователи, чтобы упростить использование, понимание и запоминание информации.

Дополнительные сведения см. в справочной документации: [LargePersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup — Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person — Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup — Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person — Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Проверка

API Verification используется для проверки двух лиц на принадлежность одному человеку. Оба лица представлены в запросе как идентификаторы лиц. Результаты проверки содержат логическое поле (isIdentical), указывающее, принадлежат ли лица одному человеку (если да, то поле имеет значение true), и числовое поле ([confidence](#confidence)), указывающее уровень достоверности.

Дополнительные сведения см. в справочной документации: [Сталкиваются - проверка](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
