---
title: 'Многоклассное дерево решений: Ссылка на модуль'
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать модуль Multiclass Boosted Decision Tree в Azure Machine Learning для создания классификатора с использованием помеченных данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920065"
---
# <a name="multiclass-boosted-decision-tree"></a>Мультиклассовое увеличивающееся дерево принятия решений

В этой статье описывается модуль в дизайнере машинного обучения Azure (предварительный просмотр).

Используйте этот модуль для создания модели машинного обучения, основанной на алгоритме ускоренных деревьев решений.

Увеличенное дерево решений – это метод обучения ансамблю, в котором второе дерево исправляет ошибки первого дерева, третье дерево исправляет ошибки первого и второго деревьев и так далее. Прогнозы основаны на ансамбле деревьев вместе.

## <a name="how-to-configure"></a>Порядок настройки 

Этот модуль создает неподготовленную модель классификации. Поскольку классификация является контролируемым методом обучения, требуется *помеченный набор данных,* который включает столбец метки со значением для всех строк.

Вы можете обучить этот тип модели с помощью [модели train.](././train-model.md) 

1.  Добавьте модуль **Multiclass Boosted Decision Tree** в конвейер.

1.  Укажите, как нужно обучить модель, установив опцию **«Создать тренер».**

    + **Единый параметр**: Если вы знаете, как настроить модель, вы можете предоставить определенный набор значений в качестве аргументов.
    
    + **Диапазон параметров**: Выберите этот параметр, если вы не уверены в лучших параметрах, и хотите запустить развертки параметров. Выберите диапазон значений для итерации, и [Hyperparameters Tune Model](tune-model-hyperparameters.md) итерирует все возможные комбинации параметров, которые вы предоставили, чтобы определить гиперпараметры, которые дают оптимальные результаты.  

1. **Максимальное количество листьев на дерево** ограничивает максимальное количество узлов терминала (листьев), которые могут быть созданы в любом дереве.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Минимальное количество образцов на узла листа** указывает на количество случаев, необходимых для создания любого терминального узла (листа) в дереве.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **Скорость обучения** определяет размер шага во время обучения. Введите число между 0 и 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Количество построенных деревьев** указывает общее количество деревьев решений для создания в ансамбле. Создавая больше деревьев принятия решений, вы можете потенциально получить большее покрытие, но время на обучение при этом увеличится.

1. **Случайное количество семян** дополнительно устанавливает неотрицательный целый ряд для использования в качестве случайного значения семян. Определение семени обеспечивает воспроизводимость на трассах с одинаковыми данными и параметрами.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> При установке **режима "Создание тренера"** на **единый параметр**подключите помеченный набор данных и модуль [модели train.](./train-model.md)

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [набором модулей, доступных](module-reference.md) для машинного обучения Azure. 
