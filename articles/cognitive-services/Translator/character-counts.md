---
title: Подсчет символов — API перевода текстов
titlesuffix: Azure Cognitive Services
description: Как API перевода текстов считает символы.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649200"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Как API перевода текстов считает символы

API перевода текстов считает каждый символ входных данных. Подсчет ведется в символах Юникода, а не в байтах. Суррогатные символы Юникода подсчитываются как два символа. Пробелы и символы разметки подсчитываются как символы. Длина ответа не имеет значения.

Вызовы методов Detect и BreakSentence не учитываются при подсчете использованных символов. Тем не менее предполагается, что вызовы методов Detect и BreakSentence логически соотносятся с использованием других функций, которые учитываются. Корпорация Майкрософт оставляет за собой право начать подсчет символов в методах Detect и BreakSentence.

Дополнительные сведения о подсчете символов см. в разделе [часто задаваемых вопросов о Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
