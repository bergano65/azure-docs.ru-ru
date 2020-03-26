---
title: Руководство по Создание модели кластеризации с помощью R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Во второй части этой серии руководств из трех частей вы построите модель K-средних, чтобы выполнить кластеризацию с помощью языка R и Служб машинного обучения в Базе данных SQL Azure (предварительная версия).
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
ms.date: 07/29/2019
ms.openlocfilehash: 9f16ebc5acff7bbccc9de28e2fab0d223c6e244b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68640016"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Руководство по Построение модели кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия)

Во второй части этого цикла из трех частей вы создадите модель K-средних на языке R для выполнения кластеризации. В следующей части из этой серии описывается, как развернуть эту модель в базе данных SQL с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).

В этой статье вы узнаете, как выполнять следующие задачи.

> [!div class="checklist"]
> * Определение числа кластеров для алгоритма K-средних
> * Выполнение кластеризации
> * Анализ результатов

Из [первой части](sql-database-tutorial-clustering-model-prepare-data.md) вы узнали, как выполнять подготовку данных из базы данных SQL Azure для кластеризации.

В [третьей части](sql-database-tutorial-clustering-model-deploy.md) рассказывается, как создать в Базе данных SQL Azure хранимую процедуру, с помощью которой можно выполнять кластеризацию с помощью R на основе новых данных.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Во второй части этого учебника предполагается, что вы ознакомились с [**первой частью**](sql-database-tutorial-clustering-model-prepare-data.md) и ее компонентами.

## <a name="define-the-number-of-clusters"></a>Определение числа кластеров

Для кластеризации данных клиентов будет использоваться алгоритм **K-средних**, который является одним из простейших и популярных способов группирования данных.
Дополнительные сведения о нем см. в статье [Полное руководство по алгоритму кластеризации на основе K-средних](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Этот алгоритм принимает два входных значения: Сами данные и предварительно определенное число *k*, представляющее число создаваемых кластеров.
На выходе получается *k* кластеров, по которым разделяются входные данные.

Чтобы определить число кластеров для алгоритма, используется график значений сумм квадратов внутри групп по числу извлеченных кластеров. Необходимое число кластеров будет находиться на изгибе графика.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Изгиб графика](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

На этом графике оптимальным является значение *k = 4*. При таком значении *k* клиенты будут группироваться по четырем кластерам.

## <a name="perform-clustering"></a>Выполнение кластеризации

В следующем скрипте на языке R используется функция **rxKmeans**, которая является функцией K-средних из пакета RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Анализ результатов

После кластеризации методом K-средних, следующим шагом будет анализ результата и его оценка на наличие полезных сведений.

Объект **clust** содержит результат кластеризации методом K-средних.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Значения кластеров задаются с помощью переменных, определенных в [первой части](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = коэффициент возвратов заказов (отношение числа частично или полностью возвращенных заказов к общему числу заказов)
* *itemsRatio* = коэффициент возврата единицы товара (отношение возвращенных единиц товара к общему числу проданных единиц товара)
* *monetaryRatio* = коэффициент возврата в денежном выражении (отношение общего объема возвратов к общему объему покупок в денежном выражении)
* *frequency* = частота возвратов

Для интеллектуального анализа данных по методу K-средних часто требуется проведение дополнительного анализа результатов, а также выполнение других действий для лучшего понимания каждого кластера. Тем не менее, такой метод может дать полезные начальные результаты.
Интерпретировать результаты можно несколькими способами:

* Кластер 1 (самый большой кластер), вероятно, представлен пассивными клиентами (все значения равны нулю).
* Кластер 3 определяет группу с отличным от других поведением.

## <a name="clean-up-resources"></a>Очистка ресурсов

***Если вы не собираетесь продолжать работу с этим руководством***, удалите базу данных tpcxbb_1gb с сервера Базы данных SQL Azure.

На портале Azure сделайте следующее:

1. В меню слева на портале Azure выберите **Все ресурсы** или **Базы данных SQL**.
1. В поле **Фильтровать по имени…** введите **tpcxbb_1gb** и выберите свою подписку.
1. Выберите базу данных **tpcxbb_1gb**.
1. На странице **Обзор** выберите **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Во второй части этого учебника вы выполнили следующие действия.

* Определение числа кластеров для алгоритма K-средних
* Выполнение кластеризации
* Анализ результатов

Чтобы развернуть созданную модель машинного обучения, следуйте указаниям из третьей части этой серии учебников:

> [!div class="nextstepaction"]
> [Руководство. Развертывание модели кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия)](sql-database-tutorial-clustering-model-deploy.md)