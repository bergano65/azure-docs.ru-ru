---
title: Руководство по Распознавание эмоций лица на изображении — API распознавания эмоций с использованием Python
titlesuffix: Azure Cognitive Services
description: Чтобы научиться использовать API распознавания эмоций с Python, используйте записную книжку Jupyter. Визуализируйте полученные результаты с помощью популярных библиотек.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 9e03e5d534fa741def2010a21120edc3d7169964
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237473"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Руководство по Использование API распознавания эмоций с помощью Jupyter Notebook и Python

> [!IMPORTANT]
> 15 февраля 2019 г. API распознавания эмоций будет отмечен как нерекомендуемый. Сейчас функция распознавания эмоций является общедоступной в составе [API распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/). 

Чтобы упростить начало работы с API распознавания эмоций, в записной книжке Jupyter ниже показано, как использовать этот API в Python и как визуализировать результаты с помощью популярных библиотек.

[Ссылка на записную книжку в GitHub](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Использование Jupyter Notebook

Для интерактивного использования записной книжки необходимо клонировать ее и запустить в Jupyter. Чтобы узнать, как приступить к работе с интерактивными записными книжками Jupyter, следуйте инструкциям на странице http://jupyter.readthedocs.org/en/latest/install.html.

Для использования этой записной книжки вам потребуется ключ подписки на API распознавания эмоций. Чтобы зарегистрироваться, перейдите на [страницу подписки](https://azure.microsoft.com/try/cognitive-services/). Выполните вход с помощью учетной записи Майкрософт, после чего вы сможете оформить подписку и получить бесплатные ключи. После завершения процедуры регистрации вставьте ключ в раздел variables, как показано ниже. Подойдет как первичный, так и вторичный ключ.

```
Python Example

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
