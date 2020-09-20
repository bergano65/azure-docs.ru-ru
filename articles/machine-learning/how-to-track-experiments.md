---
title: Запись экспериментов с машинным обучением и метрик в журнал
titleSuffix: Azure Machine Learning
description: Отслеживайте эксперименты с машинным обучением Azure и метрики выполнения, чтобы улучшить процесс создания модели. Добавьте ведение журнала в свой скрипт обучения с помощью run.log, Run.start_logging или ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 44fe71f575a32ccc1a687bc87793cb6a8b6508a9
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650623"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Включение ведения журнала для обучающих операций Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Пакет SDK Python для Машинного обучения Azure позволяет вам в реальном времени вести журнал с помощью пакета журналов Python по умолчанию и специализированных функций пакета SDK. Вы можете вести журналы в локальной среде и отправлять их в свою рабочую область на портале.

Журналы помогают вам диагностировать ошибки и предупреждения, а также отслеживать метрики производительности, например параметры и эффективность модели. Из этой статьи вы узнаете, как включить ведение журнала в следующих сценариях:

> [!div class="checklist"]
> * сеансы интерактивного обучения;
> * отправка заданий обучения с помощью ScriptRunConfig;
> * встроенные параметры `logging` Python;
> * ведение журнала из дополнительных источников.


> [!TIP]
> В этой статье демонстрируется, как обеспечить мониторинг для процесса обучения модели. Сведения о том, как обеспечить мониторинг использования ресурсов и событий из Машинного обучения Azure, например квот, завершенных обучающих операций или завершенных развертываний моделей, см. в статье [Мониторинг Машинного обучения Azure](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Типы данных

Вы можете вести журнал по нескольким типам данных, включая скалярные значения, списки, таблицы, изображения, каталоги и т. д. Дополнительные сведения и примеры кода на Python для разных типов данных см. на [странице справки по классу Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true).

## <a name="interactive-logging-session"></a>Интерактивный сеанс ведения журнала

Интерактивные сеансы ведения журнала обычно используются в средах записных книжек. Запустить интерактивный сеанс ведения журнала можно с помощью метода [Experiment.start_logging()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-). Все метрики, записанные во время сеанса, добавляются в запись о выполнении эксперимента. Метод [run.complete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) завершает сеансы и помечает операцию как завершенную.

## <a name="scriptrunconfig-logs"></a>Журналы ScriptRunConfig

Из этого раздела вы узнаете, как добавить код ведения журнала в операции ScriptConfig. Вы можете использовать класс [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true), чтобы инкапсулировать скрипты и среды для повторяемых операций. Вы также можете использовать этот параметр для отображения визуального мини-приложения Jupyter Notebook для мониторинга.

В этом примере выполняется очистка параметров для альфа-значений и регистрация результатов с помощью метода [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truelog-name--value--description----).

1. Создайте скрипт обучения, который включает логику ведения журнала `train.py`.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Отправьте скрипт ```train.py``` для выполнения в среде, управляемой пользователем. Для обучения отправляется вся папка скриптов.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    С помощью параметра `show_output` вы можете включить подробное ведение журнала, чтобы просматривать сведения о процессе обучения, а также об удаленных ресурсах или целевых объектах вычислений. Используйте следующий код, чтобы включить подробное ведение журнала при отправке эксперимента.

```python
run = exp.submit(src, show_output=True)
```

Этот же параметр вы можете указать в функции `wait_for_completion` для финального выполнения.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Собственное ведение журнала Python

Некоторые журналы, создаваемые с помощью пакета SDK, могут содержать сообщения об ошибках с предложением установить значение DEBUG для уровня ведения журнала. Чтобы изменить уровень ведения журнала, добавьте в скрипт приведенный ниже код.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Дополнительные источники ведения журнала

Машинное обучение Azure также может включать в журнал данные из других источников во время обучения, например при автоматизированных операциях машинного обучения, или данные из контейнеров Docker, выполняющих задания. Такие журналы не задокументированы, но если у вас возникли проблемы и вы обратились в службу поддержки Майкрософт, ее специалисты могут воспользоваться этими журналами для устранения неполадок.

Сведения о метриках ведения журнала в конструкторе Машинного обучения Azure (предварительная версия) см. в статье [Ведение журнала метрик в конструкторе (предварительная версия)](how-to-track-designer-experiments.md).

## <a name="example-notebooks"></a>Примеры записных книжек

Основные понятия, описанные в этой статье, демонстрируют следующие записные книжки:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

Подробные сведения об использовании Машинного обучения Azure см. в следующих статьях:

* [Ведения журнала метрик в конструкторе Машинного обучения Azure (предварительная версия)](how-to-track-designer-experiments.md).

* Пример регистрации оптимальных моделей и их развертывания см. в руководстве [Руководство № 1. Обучение модели классификации изображений с помощью Машинного обучения Azure](tutorial-train-models-with-aml.md).
