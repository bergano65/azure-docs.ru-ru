---
title: Руководство. Обучение и сравнение прогнозных моделей на языке R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Во второй части этого учебника из трех частей вы создадите две прогнозные модели на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия), а затем выберите самую точную модель.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4a5936501f45694febe284d9747bdefdfeae6aeb
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84023991"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Руководство. Создание прогнозной модели на языке R с помощью Служб машинного обучения Azure в Базе данных SQL (предварительная версия)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Во второй части этого руководства, состоящего из трех частей, объясняется, как создать две прогнозные модели на языке R и выбрать самую точную. В следующей части из этой серии описывается, как развернуть эту модель в базе данных SQL с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

В этой статье вы узнаете, как выполнять следующие задачи.

> [!div class="checklist"]
>
> * Обучение двух моделей машинного обучения.
> * Создание прогнозов из обеих моделей.
> * Сравнение результатов для выбора самой точной модели.

Из [первой части](predictive-model-prepare-data-tutorial.md) вы узнали, как импортировать пример базы данных, а затем подготовить данные, которые будут использоваться для обучения прогнозной модели на языке R.

Из [третьей части](predictive-model-deploy-tutorial.md) вы узнаете, как сохранить модель в базе данных, а затем создать хранимые процедуры с помощью скриптов R, разработанных в первых двух частях. Хранимые процедуры будут выполняться в базе данных SQL для составления прогнозов на основе новых данных.

## <a name="prerequisites"></a>Предварительные требования

* Во второй части этого учебника предполагается, что вы ознакомились с [**первой частью**](predictive-model-prepare-data-tutorial.md) и ее компонентами.

## <a name="train-two-models"></a>Обучение двух моделей

Чтобы найти лучшую модель для данных о прокате лыж, создайте две разные модели (линейная регрессия и дерево принятия решений) и посмотрите, какая из них делает более точные прогнозы. Вы используете кадр данных `rentaldata`, созданный в первой части этой серии.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Создание прогнозов из обеих моделей.

Используйте функцию прогнозирования, чтобы спрогнозировать количество случаев аренды, используя каждую обученную модель.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Сравнение результатов

Теперь необходимо посмотреть, какая модель делает более точные прогнозы. Быстрый и простой способ сделать это — использовать базовую функцию построения, чтобы увидеть разницу между фактическими значениями в данных для обучения и прогнозируемыми значениями.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Сравнение двух моделей](./media/predictive-model-build-compare-tutorial/compare-models.png)

Похоже, что из двух моделей модель дерева принятия решений точнее.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь продолжать работу с этим руководством, удалите базу данных TutorialDB со своего сервера.

На портале Azure сделайте следующее:

1. В меню слева на портале Azure выберите **Все ресурсы** или **Базы данных SQL**.
1. В поле **Фильтровать по имени...** введите **TutorialDB** и выберите свою подписку.
1. Выберите базу данных TutorialDB.
1. На странице **Обзор** выберите **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Во второй части этого учебника вы выполнили следующие действия.

* Обучение двух моделей машинного обучения.
* Создание прогнозов из обеих моделей.
* Сравнение результатов для выбора самой точной модели.

Чтобы развернуть созданную модель машинного обучения, следуйте указаниям из третьей части этой серии учебников:

> [!div class="nextstepaction"]
> [Руководство. Развертывание прогнозной модели на языке R с помощью Служб машинного обучения Azure в Базе данных SQL (предварительная версия)](predictive-model-deploy-tutorial.md)
