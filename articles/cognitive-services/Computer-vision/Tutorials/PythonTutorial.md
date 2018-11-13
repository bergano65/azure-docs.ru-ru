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
ms.date: 11/6/2018
ms.author: kefre
ms.openlocfilehash: 16054d19c1ce6f211ebd3e2f0bbc4d152a255dda
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51276973"
---
# <a name="tutorial-computer-vision-api-python"></a>Руководство по API компьютерного зрения для Python

В этом руководстве показано, как использовать API компьютерного зрения в Python и как визуализировать результаты с помощью популярных библиотек. Для работы с руководством используется Jupyter. Чтобы узнать, как приступить к работе с интерактивными записными книжками Jupyter Notebook, обратитесь к [документации по Jupyter](http://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Предварительные требования

- [Python 2.7 или 3.5+](https://www.python.org/downloads/).
- Средство [PIP](https://pip.pypa.io/en/stable/installing/).
- Установленная записная книжка [Jupyter Notebook](http://jupyter.org/install).

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
_region = 'westcentralus' #Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(_region)
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

При выполнении руководства вы сможете добавлять изображения для анализа как из URL-адреса, так и из локального хранилища. Скрипт выдаст изображения и информацию анализа в записной книжке.