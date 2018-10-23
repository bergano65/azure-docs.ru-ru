---
title: Руководство по API компьютерного зрения для Python
titlesuffix: Azure Cognitive Services
description: Узнайте о том, как использовать API компьютерного зрения с Python с помощью записных книжек Jupyter. Визуализируйте результаты с помощью популярных библиотек.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 046250d3d2142badaac35490eff27bcac220fea9
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344903"
---
# <a name="tutorial-computer-vision-api-python"></a>Руководство по API компьютерного зрения для Python

В этом руководстве показано, как использовать API компьютерного зрения с помощью Python и как визуализировать результаты с помощью популярных библиотек. Для работы с этим руководством используется Jupyter. Чтобы узнать, как приступить к работе с интерактивными записными книжками Jupyter, обратитесь к [документации по Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Открытие примера записной книжки в Jupyter 

1. Перейдите к [примеру записной книжке на GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Нажмите зеленую кнопку, чтобы клонировать или скачать пример. 
3. Откройте командную строку и перейдите в папку _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Выполните команду **jupyter notebook** из командной строки. Запустится Jupyter.
5. В окне Jupyter щелкните _Computer Vision API Example.ipynb_, чтобы открыть пример записной книжки. 

## <a name="run-the-tutorial"></a>Запуск примера записной книжки

Для использования этой записной книжки потребуется ключ подписки на API компьютерного зрения. Перейдите на страницу [Подписка](https://azure.microsoft.com/try/cognitive-services/), чтобы зарегистрироваться. На странице "Вход" войдите с помощью учетной записи Майкрософт. После этого вы сможете оформить подписку и получить бесплатные ключи. Завершив регистрацию, вставьте ключ в раздел переменных записной книжки (см. ниже). Подойдет как первичный, так и вторичный ключ. Не забудьте заключить ключ в кавычки, чтобы сделать его строковым значением.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
