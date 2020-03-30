---
title: 'ONNX: высокой perf, кросс-платформенный вывод'
titleSuffix: Azure Machine Learning
description: Узнайте, как использование Open Neural Network Exchange (ONNX) может помочь оптимизировать вывод модели машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270177"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX и Azure Machine Learning: Создание и ускорение моделей ML

Узнайте, как использование [Open Neural Network Exchange](https://onnx.ai) (ONNX) может помочь оптимизировать вывод модели машинного обучения. Вывод, или оценка модели, — это этап, на котором развернутая модель используется для прогнозирования, чаще всего на производственных данных. 

Оптимизация моделей машинного обучения для оценки выводов (или оценки моделей) затруднена, так как необходимо настроить модель и библиотеку выводов, чтобы максимально сделать аппаратные возможности. Проблема становится чрезвычайно трудной, если вы хотите получить оптимальную производительность на различных видах платформ (облако / край, процессор / GPU и т.д.), так как каждый из них имеет различные возможности и характеристики. Сложность возрастает, если у вас есть модели из различных инфраструктур, которые должны работать на различных платформах. Оптимизация всех различных комбинаций фреймворков и оборудования занимает много времени. Требуется решение для обучения один раз в предпочтительной структуре и запуска в любом месте в облаке или крае. Это где ONNX приходит дюйма

Корпорация Майкрософт и сообщество партнеров создали ONNX как открытый стандарт для представления моделей машинного обучения. Модели [из многих фреймворков,](https://onnx.ai/supported-tools) включая TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet и MATLAB, могут быть экспортированы или преобразованы в стандартный формат ONNX. После того, как модели находятся в формате ONNX, они могут быть запущены на различных платформах и устройствах.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) — высокопроизводительный движок выводов для развертывания моделей ONNX в производство. Он оптимизирован как для облачных, так и для краев и работает на Linux, Windows и Mac. Написанная в СЗ, она также имеет AA, C, Python и C. ONNX Runtime обеспечивает поддержку всех спецификаций ONNX-ML, а также интегрируется с ускорителями на различных аппаратных средствах, таких как TensorRT на графических процессорах NVidia.

OnNX Runtime используется в высокомасштабных службах Майкрософт, таких как Bing, Office и Cognitive Services. Повышение производительности зависит от ряда факторов, но эти службы Microsoft видели __в среднем 2x увеличение производительности на процессоре.__ ONNX Runtime также используется как часть Windows ML на сотнях миллионов устройств. Вы можете использовать время выполнения с помощью машинного обучения Azure. Используя ONNX Runtime, вы можете извлечь выгоду из обширной оптимизации производственного класса, тестирования и постоянных улучшений.

[![Диаграмма потока ONNX, показывающая обучение, преобразователи и развертывание](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Получение моделей ONNX

Модели ONNX можно получить различными способами:
+ Обучить новую модель ONNX в Azure Machine Learning (см. примеры в нижней части этой статьи)
+ Преобразование существующей модели из другого формата в ONNX (см. [учебники)](https://github.com/onnx/tutorials) 
+ Получить предварительно обученную модель ONNX из [зоопарка модели ONNX](https://github.com/onnx/models) (см. примеры в нижней части этой статьи)
+ Создание настраиваемых моделей ONNX в [пользовательской службе визуального распознавания Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/). 

Многие модели, включая классификацию изображений, обнаружение объектов и обработку текста, могут быть представлены в качестве моделей ONNX. Однако некоторые модели не могут быть успешно преобразованы. Если вы столкнулись с этой ситуацией, пожалуйста, подайте жалобу в GitHub соответствующего конвертера, который вы использовали. Можно продолжать использовать существующую модель формата до тех пор, пока проблема не будет решена.

## <a name="deploy-onnx-models-in-azure"></a>Развертывание моделей ONNX в Azure

С помощью машинного обучения Azure можно развертывать, управлять и контролировать модели ONNX. Используя стандартный [рабочий процесс развертывания](concept-model-management-and-deployment.md) и среду выполнения ONNX, можно создать конечную точку REST, размещенную в облаке. Смотрите пример Jupyter ноутбуки в конце этой статьи, чтобы попробовать его для себя. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Установка и использование ONNX Runtime с Python

Пакеты Python для ONNX Runtime доступны на [PyPi.org](https://pypi.org) [(процессор,](https://pypi.org/project/onnxruntime) [GPU).](https://pypi.org/project/onnxruntime-gpu) Пожалуйста, ознакомьтесь [с требованиями системы](https://github.com/Microsoft/onnxruntime#system-requirements) перед установкой. 

 Чтобы установить ONNX Runtime для Python, используйте одну из следующих команд: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Для вызова среды выполнения ONNX в сценарии Python используйте следующую команду.    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

Входные и выходные данные для использования модели см. в документации к соответствующей модели. Для просмотра модели можно также использовать такое средство визуализации, как [Netron](https://github.com/lutzroeder/Netron). Среда выполнения ONNX также позволяет запрашивать метаданные модели и ее входные и выходные данные:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Для вывода модели добавьте параметр `run` и передайте список выходных данных, которые хотите получить (не заполняйте, если вам нужны все), а также карту входных значений. Результатом будет список выходных данных.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Полный справочник по API Python см. в [документации по среде выполнения ONNX](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Примеры

[how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx): примеры записных книжек, создающих и развертывающих модели ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Дополнительные сведения

Узнайте больше об ONNX или поддержите проект:
+ [Веб-сайт проекта ONNX](https://onnx.ai)
+ [Код ONNX в GitHub](https://github.com/onnx/onnx)

Узнайте больше о среде выполнения ONNX или поддержите проект:
+ [Репозиторий GitHub для среды выполнения ONNX](https://github.com/Microsoft/onnxruntime)


