---
title: Запись экспериментов с машинным обучением и метрик в журнал
titleSuffix: Azure Machine Learning
description: Отслеживайте эксперименты с машинным обучением Azure и метрики выполнения, чтобы улучшить процесс создания модели. Добавьте ведение журнала в сценарий обучения с помощью Run. log, Run. start_logging или Скриптрунконфиг.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 18c7e5b3a1401540d7a94186fda647d413d562c0
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723849"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Включение ведения журнала в учебных запусках МАШИНного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Пакет SDK для Машинное обучение Azure Python позволяет регистрировать данные в режиме реального времени, используя как пакет ведения журнала Python по умолчанию, так и специальные функции пакета SDK. Вы можете вести журнал локально и отправить журналы в рабочую область на портале.

Журналы могут помочь в диагностике ошибок и предупреждений или отслеживании метрик производительности, таких как параметры и производительность модели. Из этой статьи вы узнаете, как включить ведение журнала в следующих сценариях:

> [!div class="checklist"]
> * Интерактивные сеансы обучения
> * Отправка заданий обучения с помощью Скриптрунконфиг
> * Собственные `logging` Параметры Python
> * Ведение журнала из дополнительных источников


> [!TIP]
> В этой статье показано, как отслеживать процесс обучения модели. Если вы заинтересованы в мониторинге использования ресурсов и событиях из машинного обучения Azure, таких как квоты, завершенные обучающие запуски или завершенные развертывания моделей, см. раздел [мониторинг машинное обучение Azure](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Типы данных

Можно вести журнал нескольких типов данных, включая скалярные значения, списки, таблицы, изображения, каталоги и многое другое. Дополнительные сведения и примеры кода Python для различных типов данных см. на [странице справки по запуску класса](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

## <a name="interactive-logging-session"></a>Интерактивный сеанс ведения журнала

Интерактивные сеансы ведения журнала обычно используются в средах записных книжек. Метод [эксперимент. start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) запускает интерактивный сеанс ведения журнала. Все метрики, регистрируемые во время сеанса, добавляются в запись Run в эксперименте. Метод [Run. Complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) завершает сеансы и помечает выполнение как завершенное.

## <a name="scriptrunconfig-logs"></a>Журналы Скриптрунконфиг

В этом разделе вы узнаете, как добавить код ведения журнала в Скриптконфиг запуски. Класс [**скриптрунконфиг**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) можно использовать для инкапсуляции скриптов и сред для повторяемых запусков. Этот параметр также можно использовать для отображения мини-приложения записных книжек визуального Jupyter для мониторинга.

В этом примере выполняется очистка параметров по альфа-значениям и результаты записываются с помощью метода [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) .

1. Создайте сценарий обучения, включающий логику ведения журнала `train.py` .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Отправьте ```train.py``` скрипт для запуска в управляемой пользователем среде. Вся папка Script отправлена для обучения.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output`Параметр включает подробное ведение журнала, позволяющее просматривать сведения из процесса обучения, а также сведения об удаленных ресурсах или целевых объектах вычислений. Используйте следующий код, чтобы включить подробное ведение журнала при отправке эксперимента.

```python
run = exp.submit(src, show_output=True)
```

Этот же параметр вы можете указать в функции `wait_for_completion` для финального выполнения.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Собственное ведение журнала Python

Некоторые журналы в пакете SDK могут содержать ошибку, которая дает указание установить уровень ведения журнала в значение DEBUG. Чтобы изменить уровень ведения журнала, добавьте в скрипт приведенный ниже код.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Дополнительные источники журналов

Машинное обучение Azure также может записывать в журнал данные из других источников во время обучения, такие как автоматические запуски машинного обучения или контейнеры DOCKER, выполняющие задания. Эти журналы не документированы, но если возникли проблемы и обратиться в службу поддержки Майкрософт, они смогут использовать эти журналы во время устранения неполадок.

Сведения о регистрации метрик в Машинное обучение Azureном конструкторе (Предварительная версия) см. в разделе [запись метрик в конструкторе (Предварительная версия)](how-to-track-designer-experiments.md) .

## <a name="example-notebooks"></a>Примеры записных книжек

Основные понятия, описанные в этой статье, демонстрируют следующие записные книжки:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании Машинное обучение Azure см. в следующих статьях:

* Сведения о [записи метрик в машинное обучение Azure конструктора (Предварительная версия)](how-to-track-designer-experiments.md).

* Пример регистрации оптимальных моделей и их развертывания см. в руководстве [Руководство № 1. Обучение модели классификации изображений с помощью Машинного обучения Azure](tutorial-train-models-with-aml.md).
