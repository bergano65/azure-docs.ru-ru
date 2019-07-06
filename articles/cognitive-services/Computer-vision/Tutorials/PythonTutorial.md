---
title: Выполнение операций с изображениями — Python
titlesuffix: Azure Cognitive Services
description: Узнайте о том, как использовать API компьютерного зрения с Python с помощью записных книжек Jupyter. Визуализируйте результаты с помощью популярных библиотек.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b18f41db772c1c214bdf48c9bb765b1ce7e6d2c2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604157"
---
# <a name="computer-vision-api-jupyter-notebook"></a>Записная книжка API Jupyter компьютерного зрения компьютера

В этом руководстве показано, как с помощью API компьютерного зрения в Python и визуализировать результаты с помощью популярных библиотек. Для работы с руководством используется Jupyter. Сведения о том, как приступить к работе с интерактивными записными книжками Jupyter Notebook, см. в [документации по Jupyter](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Технические условия

- [Python 2.7 или 3.5+](https://www.python.org/downloads/).
- Средство [PIP](https://pip.pypa.io/en/stable/installing/).
- Установленная записная книжка [Jupyter Notebook](https://jupyter.org/install).

## <a name="open-the-notebook-in-jupyter"></a>Открыть записную книжку в Jupyter 

1. Перейдите к репозиторию GitHub [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Нажмите зеленую кнопку, чтобы клонировать или загрузить репозиторий. 
3. Откройте командную строку и перейдите к папке **Cognitive-Vision-Python\Jupyter Notebook**.
1. Убедитесь, что вы имеете все необходимые библиотеки, выполнив команду `pip install requests opencv-python numpy matplotlib` из командной строки.
1. Запустите Jupyter, выполнив команду `jupyter notebook` из командной строки.
1. В окне Jupyter щелкните _Computer Vision API Example.ipynb_, чтобы открыть пример записной книжки.

## <a name="run-the-notebook"></a>Запустите записную книжку

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
