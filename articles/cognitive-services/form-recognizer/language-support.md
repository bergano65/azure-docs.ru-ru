---
title: Поддержка языков — Распознаватель документов (предварительная версия)
titleSuffix: Azure Cognitive Services
description: Узнайте о языках, поддерживаемых в Распознавателе документов.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 182cf54f58b4fbb8b80fb4ff9d7c1a9c1e353470
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723917"
---
# <a name="language-support-for-form-recognizer"></a>Поддержка языков в Распознавателе документов

В этой статье перечислены языки, поддерживаемые службой Распознавателя документов (предварительная версия).


|Язык| Код языка | Распознаватель документов версии 2.0 | Распознаватель документов общедоступной предварительной версии 2.1*|
|:-----|:----:|:-----:|:---:|:---:|
|Китайский (упрощенное письмо) | `zh-Hans`| | ✔ |
|Нидерландский | `nl` | |✔ |
|Английский | `en` |✔ |
|Французский | `fr` | |✔ |
|Немецкий | `de` | |✔ |
|Итальянский | `it` | |✔ |
|Португальский | `pt` | |✔ |
|Испанский | `es` | |✔ |

* Распознаватель документов общедоступной предварительной версии 2.1 поддерживает эти языки для операций `Analyze Layout` и `Train Custom Model` (_обучения с помощью меток_ и _обучения без помощи меток_). Операции `Analyze Receipt` и `Analyze Business Card` поддерживаются только на английском языке.