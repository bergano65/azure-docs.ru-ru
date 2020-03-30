---
title: Справочник по алгоритмам и модулям
description: Узнайте о модулях, доступных в дизайнере машинного обучения Azure (предварительный просмотр)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: c24c7ce1d47a4734d79d7e17e9fb4b3a2d4d70e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064144"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Ссылка на модуль «Алгоритм &» для дизайнера машинного обучения Azure (предварительный просмотр)

Это справочное содержание предоставляет технический справочный материал по каждому из алгоритмов машинного обучения и модулей, доступных в дизайнере машинного обучения Azure (предварительный просмотр).

Каждый модуль представляет собой набор кода, который может работать независимо и выполнять задачу машинного обучения, учитывая необходимые входные данные. Модуль может содержать определенный алгоритм или выполнять задачу, важную для машинного обучения, например, замена отсутствующего значения или статистический анализ.

Для помощи в выборе алгоритмов см. 
* [Выбор алгоритмов](../how-to-select-algorithms.md)
* [Лазурный алгоритм машинного обучения Чит лист](../../synapse-analytics/sql-data-warehouse/cheat-sheet.md)

> [!TIP]
> В любом конвейере в конструкторе можно получить информацию о конкретном модуле. Выберите модуль, а затем щелкните ссылку **more help** (дополнительная помощь) в области **Quick Help** (Экспресс-справка).

## <a name="data-preparation-modules"></a>Модули подготовки данных


| Функциональность | Описание | Module |
| --- |--- | --- |
| Входные и выходные данные | Перемещение данных из облачных источников в конвейер. Запишите свои результаты или промежуточные данные в Azure Storage, базу данных S'L или Hive во время запуска конвейера или используйте облачное хранилище для обмена данными между конвейерами.  | [Ввод данных вручную](enter-data-manually.md) <br/> [Экспорт данных](export-data.md) <br/> [Данные по импорту](import-data.md) |
| Преобразование данных. | Операции на данных, которые являются уникальными для машинного обучения, такие как нормализация или связывание данных, уменьшение размерности и преобразование данных в различные форматы файлов.| [Добавление столбцов](add-columns.md) <br/> [Добавление строк](add-rows.md) <br/> [Применение математической операции](apply-math-operation.md) <br/> [Применение преобразования SQL](apply-sql-transformation.md) <br/> [Чистые отсутствующие данные](clean-missing-data.md) <br/> [Обрезка значений](clip-values.md) <br/> [Преобразование в CSV-файл](convert-to-csv.md) <br/> [Преобразование в набор данных](convert-to-dataset.md) <br/> [Преобразование в значения индикатора](convert-to-indicator-values.md) <br/> [Правка метаданных](edit-metadata.md) <br/> [Объединение данных](join-data.md) <br/> [Нормализация данных](normalize-data.md) <br/> [Раздел и образец](partition-and-sample.md)  <br/> [Удаление дублирующихся строк](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Преобразование "Выбор столбцов"](select-columns-transform.md) <br/> [Выбор столбцов в наборе данных](select-columns-in-dataset.md) <br/> [Разделение данных](split-data.md) |
| Выбор компонентов | Выберите подмножество релевантных, полезных функций для использования в создании аналитической модели. | [Выбор признаков с помощью фильтра](filter-based-feature-selection.md) <br/> [Значение функции перестановки](permutation-feature-importance.md) |
| Статистические функции | Предоставить широкий спектр статистических методов, связанных с наукой о данных. | [Сведение данных](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Алгоритмы машинного обучения

| Функциональность | Описание | Module |
| --- |--- | --- |
| Регрессия | Предсказать значение. | [Регрессия с помощью увеличивающегося дерева принятия решений](boosted-decision-tree-regression.md) <br/> [Регрессия с использованием модели леса принятия решений](decision-forest-regression.md) <br/> [Линейная регрессия](linear-regression.md)  <br/> [Регрессия нейронной сети](neural-network-regression.md)  <br/> |
| Кластеризация | Групповые данные вместе.| [Кластеризация методом K-средних](k-means-clustering.md)
| Классификация | Предсказать класс.  Выберите из бинарных (двухклассных) или многоклассных алгоритмов.| [Мультиклассовое увеличивающееся дерево принятия решений](multiclass-boosted-decision-tree.md) <br/> [Лес решений с несколькими классами](multiclass-decision-forest.md) <br/> [Мультиклассовая регрессионная логистическая модель](multiclass-logistic-regression.md)  <br/> [Мультиклассовая нейронная сеть](multiclass-neural-network.md) <br/> [Один против всех мультикласс](one-vs-all-multiclass.md) <br/> [Двухклассовое усредненное восприятие](two-class-averaged-perceptron.md) <br/>  [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md) (Двухклассовое увеличивающееся дерево принятия решений);  <br/> [Двухклассовый лес принятия решений](two-class-decision-forest.md) <br/>  [Двухклассовая регрессионная логистическая модель](two-class-logistic-regression.md) <br/> [Двухклассовая нейронная сеть](two-class-neural-network.md) <br/> [Двухклассовый метод опорных векторов](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Модули для построения и оценки моделей

| Функциональность | Описание | Module |
| --- |--- | --- |
| Обучение модели | Запуск данных через алгоритм. |  [Обучение модели кластеризации](train-clustering-model.md) <br/> [Модель поезда](train-model.md)  <br/> [Настройка гиперпараметров модели](tune-model-hyperparameters.md) |
| Оценка и оценка модели | Измерьте точность обученной модели. | [Применение преобразования](apply-transformation.md) <br/> [Присваивая данные кластерам](assign-data-to-clusters.md) <br/> [Перекрестная проверка модели](cross-validate-model.md) <br/> [Анализ модели](evaluate-model.md) <br/> [Оценка модели](score-model.md) |
| Язык Python | Напишите код и ввязайте его в модуль для интеграции Python с конвейером. | [Создание модели Python](create-python-model.md) <br/> [Выполнение скриптов Python](execute-python-script.md) |
| Язык R | Напишите код и ввязайте его в модуль для интеграции R с конвейером. | [Выполнение сценария R](execute-r-script.md) |
| Анализ текста | Предоставьте специализированные вычислительные инструменты для работы как со структурированным, так и с неструктурированным текстом. | [Извлечение N-грамм из текста](extract-n-gram-features-from-text.md) <br/> [Хэширование признаков](feature-hashing.md) <br/> [Preprocess Text](preprocess-text.md) <br/> [Латентный Распределение Дирихлет](latent-dirichlet-allocation.md) |
| Рекомендация | Создавайте рекомендательные модели. | [Оценка рекомендателя](evaluate-recommender.md) <br/> [Оценка рекомендателя SVD](score-svd-recommender.md) <br/> [Обучение рекомендателя SVD](train-SVD-recommender.md) |
| Обнаружение аномалий | Создавайте модели обнаружения аномалий. | [Обнаружение аномалий на основе PCA](pca-based-anomaly-detection.md) <br/> [Обучение модели обнаружения аномалий](train-anomaly-detection-model.md) |


## <a name="error-messages"></a>Сообщения об ошибках

Узнайте о [сообщениях об ошибках и кодах исключений,](designer-error-codes.md) с которыми можно столкнуться с помощью модулей в конструкторе машинного обучения Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Учебник: Построить модель в дизайнер для прогнозирования цен на автомобили](../tutorial-designer-automobile-price-train-score.md)
