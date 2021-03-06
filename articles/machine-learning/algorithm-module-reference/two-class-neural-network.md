---
title: 'Нейронная сеть из двух классов: Справочник по модулям'
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать модуль нейронной сети с двумя классами в Машинное обучение Azure для создания модели нейронной сети, которая может использоваться для прогнозирования целевого объекта, имеющего только два значения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d8e27d13de6b1ac6940972022f66811eb75c8986
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485883"
---
# <a name="two-class-neural-network-module"></a>Модуль нейронной сети с двумя классами

В этой статье описывается модуль в Машинное обучение Azure Designer (Предварительная версия).

Этот модуль используется для создания модели нейронной сети, которая может использоваться для прогнозирования целевого объекта, имеющего только два значения.

Классификация, использующая нейронные сети, является защищенным методом обучения и поэтому требует наличия *набора данных с тегами*, который включает столбец меток. Например, эту модель нейронной сети можно использовать для прогнозирования двоичных результатов, таких как наличие определенного болезни или отсутствия пациента, а также вероятность сбоя компьютера в течение заданного периода времени.  

После определения модели обучить ее, предоставив набор данных с тегами и модель в качестве входных данных для [обучения модели](./train-model.md). Обученную модель затем можно использовать для прогнозирования значений новых входных данных.

### <a name="more-about-neural-networks"></a>Дополнительные сведения о нейронных сетях

Нейронная сеть — это набор взаимосвязанных слоев. Входные данные являются первым слоем и подключены к выходному слою с помощью ациклический графа, состоящего из взвешенных ребер и узлов.

Между входным и выходным слоями можно вставить несколько скрытых слоев. Большинство прогнозных задач можно легко реализовать только с одним или несколькими скрытыми слоями. Однако последние исследования показали, что глубокие нейронные сети (DNN) с множеством уровней могут быть эффективными в сложных задачах, таких как распознавание изображений или речи. Последовательные уровни используются для моделирования повышения глубины семантики.

Связь между входными и выходными данными изучена при обучении нейронной сети входных данных. Направление графа продолжается с входных данных через скрытый слой и на выходной слой. Все узлы слоя соединены по взвешенным краям к узлам следующего слоя.

Чтобы вычислить выходные данные сети для определенных входных данных, значение вычисляется на каждом узле в скрытых слоях и на выходном слое. Значение задается путем вычисления взвешенной суммы значений узлов из предыдущего слоя. Затем функция активации применяется к этой взвешенной сумме.
  
## <a name="how-to-configure"></a>Процесс настройки

1.  Добавьте модуль **нейронной сети из двух классов** в конвейер. Этот модуль можно найти в разделе **машинное обучение**, **Инициализация**в категории **классификация** .  
  
2.  Укажите, как должна быть обучена модель, установив параметр " **создать режим инструктора** ".  
  
    -   **Один параметр**: Выберите этот параметр, если вы уже знакомы с тем, как вы хотите настроить модель.  

3.  В поле **Спецификация скрытого слоя**выберите тип создаваемой сетевой архитектуры.  
  
    -   **Полностью подключенное обращение**. использует архитектуру нейронной сети по умолчанию, определенную для нейронных сетей двух классов следующим образом:
  
        -   Имеет один скрытый слой.
  
        -   Выходной слой полностью подключен к скрытому слою, а скрытый слой полностью подключен к входному слою.
  
        -   Количество узлов во входном слое равно числу характеристик в обучающих данных.
  
        -   Число узлов в скрытом слое задается пользователем. По умолчанию используется значение 100.
  
        -   Число узлов равно числу классов. Для нейронной сети с двумя классами это означает, что все входные данные должны сопоставляться с одним из двух узлов на выходном слое.

5.  Для **курса обучения**Определите размер шага, выполняемого на каждой итерации, перед исправлением. Большее значение темпа обучения может привести к тому, что модель будет выполняться быстрее, но она может отклонение локально прыжка.

6.  Для **количества итераций обучения**укажите максимальное число раз, которое алгоритм должен обработать обучающие варианты.

7.  В **поле начальный диаметр веса для обучения**укажите весовые коэффициенты узлов в начале процесса обучения.

8.  Для **этого**укажите весовой коэффициент, применяемый при обучении узлов из предыдущих итераций.  

10. Выберите вариант **случайный** выбор, чтобы перемешать варианты между итерациями. Если снять этот флажок, варианты будут обрабатываться в точно таком же порядке при каждом запуске конвейера.
  
11. В поле **Начальное число случайных чисел**введите значение, которое будет использоваться в качестве начального значения.
  
     Указание начального значения полезно, если необходимо обеспечить повторяемость во время выполнения одного конвейера.  В противном случае в качестве начального значения используется системное значение системных часов, что может привести к слегка разным результатам при каждом запуске конвейера.
  
13. Добавьте в конвейер набор данных с тегами и подключите один из [обучающих модулей](module-reference.md).  
  
    -   Если для параметра **создать режим инструктора** задано значение **Single**, используйте модуль [обучение модели](train-model.md) .  
  
14. Запустили конвейер.

## <a name="results"></a>Результаты

После завершения обучения:

+ Чтобы просмотреть сводку параметров модели, а также весовые коэффициенты функций, полученные в ходе обучения, а также другие параметры нейронной сети, щелкните правой кнопкой мыши результат [обучение модели](./train-model.md)и выберите **визуализировать**.  

+ Чтобы сохранить моментальный снимок обученной модели, щелкните правой кнопкой мыши выходные данные **обученной модели** и выберите команду **Сохранить как обученную модель**. Эта модель не обновляется при последующих выполнениях того же конвейера.


## <a name="next-steps"></a>Дальнейшие действия

См. [набор модулей, доступных](module-reference.md) для машинное обучение Azure. 
