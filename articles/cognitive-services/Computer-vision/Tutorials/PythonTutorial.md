---
title: Руководство по Выполнение операций с изображениями — Python
titlesuffix: Azure Cognitive Services
description: Узнайте о том, как использовать API компьютерного зрения с Python с помощью записных книжек Jupyter. Визуализируйте результаты с помощью популярных библиотек.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b5333557355aa816245b5086836eac980d90540a
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341485"
---
# <a name="tutorial-computer-vision-api-python"></a>Руководство по API компьютерного зрения с Python

В этом руководстве показано, как использовать API компьютерного зрения в Python и как визуализировать результаты с помощью популярных библиотек. Для работы с руководством используется Jupyter. Сведения о том, как приступить к работе с интерактивными записными книжками Jupyter Notebook, см. в [документации по Jupyter](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Предварительные требования

- [Python 2.7 или 3.5+](https://www.python.org/downloads/).
- Средство [PIP](https://pip.pypa.io/en/stable/installing/).
- Установленная записная книжка [Jupyter Notebook](https://jupyter.org/install).

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Открытие примера записной книжки в Jupyter 

1. Перейдите к репозиторию GitHub [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Нажмите зеленую кнопку, чтобы клонировать или загрузить репозиторий. 
3. Откройте командную строку и перейдите к папке **Cognitive-Vision-Python\Jupyter Notebook**.
1. Убедитесь, что вы имеете все необходимые библиотеки, выполнив команду `pip install requests opencv-python numpy matplotlib` из командной строки.
1. Запустите Jupyter, выполнив команду `jupyter notebook` из командной строки.
1. В окне Jupyter щелкните _Computer Vision API Example.ipynb_, чтобы открыть пример записной книжки.

## <a name="run-the-tutorial"></a>Запуск примера записной книжки

Для использования этой записной книжки потребуется ключ подписки на API компьютерного зрения. Перейдите на страницу [Подписка](https://azure.microsoft.com/try/cognitive-services/), чтобы зарегистрироваться. На странице **входа** войдите с помощью учетной записи Майкрософт. После этого вы сможете оформить подписку и получить бесплатные ключи. После регистрации вставьте ключ в `Variables` раздел записной книжки (воспроизведено ниже). Подойдет как первичный, так и вторичный ключ. Не забудьте заключить ключ в кавычки, чтобы сделать его строковым значением.

Кроме того, убедитесь, что поле `_region` совпадает с регионом, который соответствует вашей подписке.

```python
# Variables
_region = 'westcentralus'  # Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(
    _region)
_key = None  # Here you have to paste your primary key
_maxNumRetries = 10
```

При выполнении руководства вы сможете добавлять изображения для анализа как из URL-адреса, так и из локального хранилища. Скрипт выдаст изображения и информацию анализа в записной книжке.
