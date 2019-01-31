---
title: Подсчет символов — API перевода текстов
titlesuffix: Azure Cognitive Services
description: Как API перевода текстов считает символы.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226440"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Как API перевода текстов считает символы

API перевода текстов считает каждый символ входных данных. Подсчет ведется в символах Юникода, а не в байтах. Суррогатные символы Юникода подсчитываются как два символа. Пробелы и символы разметки подсчитываются как символы. Длина ответа не имеет значения.

Вызовы методов Detect и BreakSentence не учитываются при подсчете использованных символов. Тем не менее предполагается, что вызовы методов Detect и BreakSentence логически соотносятся с использованием других функций, которые учитываются. Корпорация Майкрософт оставляет за собой право начать подсчет символов в методах Detect и BreakSentence.

Дополнительные сведения о подсчете символов см. в разделе [часто задаваемых вопросов о Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
