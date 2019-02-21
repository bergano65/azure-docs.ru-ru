---
title: Краткое руководство. Обнаружение лиц на изображении с помощью REST API и Python
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как определять лица на изображении с помощью REST API распознавания лиц Azure и Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: db7c3da7d9fff0aa604a73e541be19afc0033a57
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309036"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>Краткое руководство. Обнаружение лиц на изображении с помощью REST API распознавания лиц и Python

В этом кратком руководстве описано, как определять лица на изображении с помощью REST API распознавания лиц Azure и Python. Это скрипт нарисует рамки вокруг лиц и наложит на изображение сведения о поле и возрасте.

![Изображение женщины и мужчины с прямоугольниками, нарисованными вокруг лиц, и сведениями о возрасте и поле](../images/labelled-faces-python.png)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 


## <a name="prerequisites"></a>Предварительные требования

- Ключ подписки на API распознавания лиц. Вы можете получить ключ бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Или следуйте инструкциям в руководстве по [созданию учетной записи Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), чтобы получить подписку на API распознавания лиц и свой ключ.

## <a name="run-the-jupyter-notebook"></a>Запуск записной книжки Jupyter

Код из этого краткого руководства можно запустить как Jupyter Notebook в [MyBinder](https://mybinder.org). Чтобы запустить Binder, нажмите следующую кнопку: Затем следуйте указаниям в блокноте.

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>Дополнительная информация

Затем ознакомьтесь со справочной документацией по API распознавания лиц, чтобы узнать больше о поддерживаемых сценариях.

> [!div class="nextstepaction"]
> [API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
