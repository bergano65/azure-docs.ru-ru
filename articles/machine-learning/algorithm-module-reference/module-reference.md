---
title: Справочник по алгоритмам и модулям
titleSuffix: Azure Machine Learning service
description: Сведения о модулях, доступных в визуальном интерфейсе Машинное обучение Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2d81c407c17c50c34afd15a99d2f8ac2f8c5361e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692838"
---
# <a name="algorithm--module-reference-overview"></a>Обзор справочника по модулям & алгоритма

Это справочное содержимое содержит технические сведения о каждом из алгоритмов и модулей машинного обучения, доступных в предварительной версии службы Машинное обучение Azure Service.

Каждый модуль представляет собой набор кода, который может выполняться независимо и выполнять задачу машинного обучения с учетом требуемых входных данных. Модуль может содержать определенный алгоритм или задачу, важную для машинного обучения, например, отсутствие замены значения или статистический анализ.

> [!TIP]
> В любом конвейере в визуальном интерфейсе можно получить сведения о конкретном модуле. Выберите модуль, а затем щелкните ссылку " **другие справки** " в области " **Быстрая справка** ".

## <a name="modules"></a>Модули

Модули организованы по функциональным возможностям:

| Функции | Описание | модуль |
| --- |--- | ---- |
| Преобразование формата данных | Преобразование данных из различных форматов файлов, используемых в машинном обучении, | [Преобразовать в CSV-файл](convert-to-csv.md) |
| Входные и выходные данные | Перемещение данных из облачных источников в эксперимент. Запись результатов или промежуточных данных в службу хранилища Azure, базу данных SQL или Hive во время эксперимента или использование облачного хранилища для обмена данными между экспериментами.  | [Импорт данных](import-data.md)<br/>[Экспорт данных](export-data.md)<br/>[Ввод данных вручную](enter-data-manually.md) |
| Преобразование данных | Операции с данными, которые являются уникальными для машинного обучения, такие как нормализация или группирования данных, выбор компонентов и уменьшение размерности.| [Выбор столбцов в наборе данных](select-columns-in-dataset.md) <br/> [Изменить метаданные](edit-metadata.md) <br/> [Очистить отсутствующие данные](clean-missing-data.md) <br/>  [Хэширование компонентов](feature-hashing.md) <br/>  [Извлечь из текста N функций для грамматики.](extract-n-gram-features-from-text.md) <br/> [Добавить столбцы](add-columns.md) <br/> [Добавить строки](add-rows.md) <br/> [Удалить дублирующиеся строки](remove-duplicate-rows.md) <br/> [Предварительная обработка текста](preprocess-text.md) <br/> [Объединение данных](join-data.md) <br/> [Разделение данных](split-data.md) <br/> [Нормализация данных](normalize-data.md) <br/> [Секционирование и выборка](partition-and-sample.md) <br/> [Отсеченные значения](clip-values.md) <br/> [Применить преобразование SQL](apply-sql-transformation.md)|
| Модули Python и R | Напишите код и внедрите его в модуль для интеграции Python и R с вашим экспериментом. | [Выполнение скрипта Python](execute-python-script.md)   <br/> [Создание модели Python](create-python-model.md) <br/> [Выполнить сценарий R](execute-r-script.md)
|  | **Алгоритмы машинного обучения**: | |
| классификация; | Прогнозирование класса.  Выберите один из двоичных (двух классов) или многоклассовых алгоритмов.| [Лес решений в многоклассовых решениях](multiclass-decision-forest.md) <br/> [Многоклассическое дерево принятия решений](multiclass-boosted-decision-tree.md) <br/> [Логистическая регрессия в многоклассовой](multiclass-logistic-regression.md)  <br/> [Многоклассовая нейронная сеть](multiclass-neural-network.md)  <br/>  [Логистическая регрессия двух классов](two-class-logistic-regression.md)  <br/>[Среднее перцептрона, основанное на двух классах](two-class-averaged-perceptron.md) <br/> [@No__t_2Decision &nbsp;Boosted с двумя классами &nbsp;Tree](two-class-boosted-decision-tree.md)  <br/> [Лес решений с двумя классами](two-class-decision-forest.md)  <br/> [Нейронная сеть с двумя классами](two-class-neural-network.md)  <br/> [Два&#8209;&nbsp;Support класса &nbsp;Vector &nbsp;Machine](two-class-support-vector-machine.md) 
| Кластеризация | Группирование данных.| [Кластеризация K-средних](k-means-clustering.md)
| Регрессия | Прогнозирование значения. | [Линейная регрессия](linear-regression.md)  <br/> [Регрессия нейронной сети](neural-network-regression.md)  <br/> [Регрессия леса принятия решений](decision-forest-regression.md)  <br/> [Повышенная &nbsp;Decision &nbsp;Tree &nbsp;Regression](boosted-decision-tree-regression.md)
|  | **Создание и вычисление моделей**: | |
| Train   | Выполнение данных с помощью алгоритма. | [Обучение модели](train-model.md)  <br/> [Обучение модели кластеризации](train-clustering-model.md)    |
| Анализ модели | Измерьте точность обученной модели. |  [Вычисление модели](evaluate-model.md)
| Оценка | Получите прогнозы на основе только что обученной модели. | [Применить преобразование](apply-transformation.md)<br/>[Назначение &nbsp;to &nbsp;Data &nbsp;Clusters](assign-data-to-clusters.md) <br/>[Модель оценки](score-model.md)
| Статистические функции | Предоставляют широкий спектр числовых методов, связанных с обработкой и анализом данных. | [Применить математическую операцию](apply-math-operation.md) <br/> [Суммировать данные](summarize-data.md)|

## <a name="error-messages"></a>Сообщения об ошибках

Сведения о [сообщениях об ошибках и кодах исключений](machine-learning-module-error-codes.md) , которые могут возникнуть при использовании модулей в визуальном интерфейсе машинное обучение Azure службы.
