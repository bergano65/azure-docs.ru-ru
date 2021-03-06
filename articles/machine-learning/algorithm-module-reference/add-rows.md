---
title: 'Добавить строки: ссылка на модуль'
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать модуль добавления строк в Машинное обучение Azure для сцепления двух наборов данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a16dc0bfc3fae8852c46435f6407c56149e1b14e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493958"
---
# <a name="add-rows-module"></a>Добавить модуль "строки"

В этой статье описывается модуль в Машинное обучение Azure Designer (Предварительная версия).

Используйте этот модуль для сцепления двух наборов данных. При объединении строки второго набора данных добавляются в конец первого набора данных.  
  
Объединение строк полезно в таких сценариях:  
  
+ Вы создали серию статистических данных оценки и хотите объединить их в одну таблицу для упрощения создания отчетов.  
  
+ Вы работали с различными наборами данных и хотите объединить наборы DataSet, чтобы создать окончательный набор.  

## <a name="how-to-use-add-rows"></a>Как использовать добавление строк  

Для сцепления строк из двух наборов данных строки должны иметь одну и ту же схему. Это означает, что такое же количество столбцов и один и тот же тип данных в столбцах.

1.  Перетащите модуль **Добавить строки** в конвейер, его можно найти в разделе " **Преобразование данных**" в категории " **операции** ".

2. Подключите наборы данных к двум входным портам. Набор данных, который необходимо добавить, должен быть подключен ко второму (правому) порту. 
  
3.  Запустили конвейер. Количество строк в выходном наборе данных должно равняться сумме строк обоих входных наборов данных.

    Если один и тот же набор данных добавляется к обоим входным данным модуля **добавления строк** , то набор данных дублируется. 

## <a name="next-steps"></a>Дальнейшие действия

См. [набор модулей, доступных](module-reference.md) для машинное обучение Azure. 