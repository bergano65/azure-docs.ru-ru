---
title: Справочник по алгоритмам и модулям
description: Сведения о модулях, доступных в Машинное обучение Azure Designer (Предварительная версия)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: d3feb62c0c7fa24dd998add08d17ebd1d4e9ee6c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162588"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Ссылка на модуль & алгоритма для конструктора Машинное обучение Azure

Это справочное содержимое содержит технические сведения о каждом из алгоритмов и модулей машинного обучения, доступных в Машинное обучение Azure Designer (Предварительная версия).

Каждый модуль представляет собой набор кода, который может выполняться независимо и выполнять задачу машинного обучения с учетом требуемых входных данных. Модуль может содержать определенный алгоритм или выполнять задачу, важную для машинного обучения, например, замена отсутствующего значения или статистический анализ.

Справку по выбору алгоритмов см. в разделе 
* [Выбор алгоритмов](../how-to-select-algorithms.md)
* [Лист Памятка по алгоритма Машинное обучение Azure](../algorithm-cheat-sheet.md)

> [!TIP]
> В любом конвейере в конструкторе можно получить сведения о конкретном модуле. Выберите модуль, а затем щелкните ссылку **more help** (дополнительная помощь) в области **Quick Help** (Экспресс-справка).

## <a name="data-preparation-modules"></a>Модули подготовки данных


| Функциональность | Description | Модуль |
| --- |--- | --- |
| Входные и выходные данные | Перемещение данных из облачных источников в конвейер. Запишите результаты или промежуточные данные в службу хранилища Azure, базу данных SQL или Hive, во время выполнения конвейера или используйте облачное хранилище для обмена данными между конвейерами.  | [Ввод данных вручную](enter-data-manually.md) <br/> [Экспорт данных](export-data.md) <br/> [Импорт данных](import-data.md) |
| Преобразование данных. | Операции с данными, которые являются уникальными для машинного обучения, такие как нормализация или группирования данных, уменьшение размерности и преобразование данных в различные форматы файлов.| [Добавить столбцы](add-columns.md) <br/> [Добавить строки](add-rows.md) <br/> [Применить математическую операцию](apply-math-operation.md) <br/> [Применить преобразование SQL](apply-sql-transformation.md) <br/> [Очистить отсутствующие данные](clean-missing-data.md) <br/> [Отсеченные значения](clip-values.md) <br/> [Преобразовать в CSV-файл](convert-to-csv.md) <br/> [Преобразовать в набор данных](convert-to-dataset.md) <br/> [Преобразовать в значения индикатора](convert-to-indicator-values.md) <br/> [Изменить метаданные](edit-metadata.md) <br/> [Объединение данных](join-data.md) <br/> [Нормализация данных](normalize-data.md) <br/> [Секционирование и выборка](partition-and-sample.md)  <br/> [Удалить дублирующиеся строки](remove-duplicate-rows.md) <br/> [смоте](smote.md) <br/> [Преобразование "Выбор столбцов"](select-columns-transform.md) <br/> [Выбор столбцов в наборе данных](select-columns-in-dataset.md) <br/> [Split Data](split-data.md) (Разделение данных); |
| Выбор компонентов | Выберите подмножество важных, полезных функций, используемых при построении аналитической модели. | [Выбор компонентов на основе фильтра](filter-based-feature-selection.md) <br/> [Важность функции перестановки](permutation-feature-importance.md) |
| Статистические функции | Предоставляют широкий спектр статистических методов, связанных с обработкой и анализом данных. | [Суммировать данные](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Алгоритмы машинного обучения

| Функциональность | Description | Модуль |
| --- |--- | --- |
| Регрессия | Прогнозирование значения. | [Регрессия повышенного дерева принятия решений](boosted-decision-tree-regression.md) <br/> [Регрессия леса принятия решений](decision-forest-regression.md) <br/> [Линейная регрессия](linear-regression.md)  <br/> [Регрессия нейронной сети](neural-network-regression.md)  <br/> |
| Кластеризация | Группирование данных.| [Кластеризация K-средних](k-means-clustering.md)
| Классификация | Прогнозирование класса.  Выберите один из двоичных (двух классов) или многоклассовых алгоритмов.| [Многоклассическое дерево принятия решений](multiclass-boosted-decision-tree.md) <br/> [Лес решений в многоклассовых решениях](multiclass-decision-forest.md) <br/> [Логистическая регрессия в многоклассовой](multiclass-logistic-regression.md)  <br/> [Многоклассовая нейронная сеть](multiclass-neural-network.md) <br/> [Сравнение нескольких классов](one-vs-all-multiclass.md) <br/> [Среднее перцептрона, основанное на двух классах](two-class-averaged-perceptron.md) <br/>  [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md) (Двухклассовое увеличивающееся дерево принятия решений);  <br/> [Лес решений с двумя классами](two-class-decision-forest.md) <br/>  [Логистическая регрессия двух классов](two-class-logistic-regression.md) <br/> [Нейронная сеть с двумя классами](two-class-neural-network.md) <br/> [Векторный компьютер поддержки двух классов](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Модули для создания и оценки моделей

| Функциональность | Description | Модуль |
| --- |--- | --- |
| Обучение модели | Выполнение данных с помощью алгоритма. |  [Обучение модели кластеризации](train-clustering-model.md) <br/> [Train Model](train-model.md) (Обучение модели);  <br/> [Настройка параметров модели](tune-model-hyperparameters.md) |
| Оценка и вычисление модели | Измерьте точность обученной модели. | [Применить преобразование](apply-transformation.md) <br/> [Назначение данных кластерам](assign-data-to-clusters.md) <br/> [Перекрестная проверка модели](cross-validate-model.md) <br/> [Вычисление модели](evaluate-model.md) <br/> [Модель оценки](score-model.md) |
| Язык Python | Напишите код и внедрите его в модуль, чтобы интегрировать Python с вашим конвейером. | [Создание модели Python](create-python-model.md) <br/> [Выполнение скрипта Python](execute-python-script.md) |
| Язык R | Напишите код и внедрите его в модуль, чтобы интегрировать R с вашим конвейером. | [Выполнить сценарий R](execute-r-script.md) |
| Анализ текста | Предоставляют специализированные вычислительные средства для работы с структурированным и неструктурированным текстом. | [Извлечь из текста N функций для грамматики.](extract-n-gram-features-from-text.md) <br/> [Хэширование компонентов](feature-hashing.md) <br/> [Предварительная обработка текста](preprocess-text.md) |
| Рекомендация | Модели рекомендаций сборки. | [Ознакомление с рекомендацией](evaluate-recommender.md) <br/> [SVD рекомендации по оценке](score-svd-recommender.md) <br/> [Совет по обучению SVD](train-SVD-recommender.md) |

## <a name="error-messages"></a>Сообщения об ошибках

Сведения о [сообщениях об ошибках и кодах исключений](designer-error-codes.md) , которые могут возникнуть при использовании модулей в конструкторе машинное обучение Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Учебник. Создание модели в конструкторе для прогнозирования автоцен](../tutorial-designer-automobile-price-train-score.md)
