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
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640016"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Руководство по Построение модели кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия)

Во второй части этого цикла из трех частей вы создадите модель K-средних на языке R для выполнения кластеризации. В следующей части из этой серии описывается, как развернуть эту модель в базе данных SQL с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).

Из этой статьи вы узнаете о следующем:

> [!div class="checklist"]
> * определять число кластеров для алгоритма методом K-средних;
> * выполнили кластеризацию;
> * провели анализ результатов.

Из [первой части](sql-database-tutorial-clustering-model-prepare-data.md) вы узнали, как выполнять подготовку данных из базы данных SQL Azure для кластеризации.

В [третьей части](sql-database-tutorial-clustering-model-deploy.md) рассказывается, как создать в Базе данных SQL Azure хранимую процедуру, с помощью которой можно выполнять кластеризацию с помощью R на основе новых данных.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Во второй части этого учебника предполагается, что вы ознакомились с [**первой частью**](sql-database-tutorial-clustering-model-prepare-data.md) и ее компонентами.

## <a name="define-the-number-of-clusters"></a>Определение количества кластеров

Для кластеризации пользовательских данных вы воспользуетесь алгоритмом кластеризации методом **K-средних**, одним из простейших и популярнейших способов группирования данных.
Дополнительные сведения о методе K-средних см. в [полном руководстве по алгоритму кластеризации методом K-средних](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Алгоритм принимает входные данные двух разновидностей: Сами данные, а также предопределенное число *k*, соответствующее количеству создаваемых кластеров.
Выходными данными будут кластеры в количестве *k* с входными данными, распределенными по ним.

Чтобы определить число кластеров для использования в алгоритме, используйте график суммарного квадратичного отклонения в группах по числу полученных кластеров. Соответствующее количество кластеров для использования находится на "изломе" графика.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![График с изломом](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Судя по графику, оптимальным количеством является *k = 4*. Значение *k* будет использоваться для группировки клиентов по четырем кластерам.

## <a name="perform-clustering"></a>Кластеризация

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

Средние четырех кластеров заданы с помощью переменных, определенных в [первой части](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = коэффициент возвращенных заказов (общее количество заказов, частично или полностью возвращенных, к общему количеству заказов);
* *itemsRatio* = коэффициент возвращенных товаров (общее количество возвращенных товаров к общему количеству приобретенных товаров);
* *monetaryRatio* = коэффициент возвращенных средств (общая стоимость возвращенных товаров к общей стоимости приобретенных товаров);
* *frequency* = частота возвратов.

Интеллектуальный анализ данных методом K-средних часто требует последующего анализа результатов и дальнейших шагов для лучшего понимания каждого кластера. Вместе с тем он может дать ряд хороших подсказок.
Вот несколько способов того, как можно интерпретировать полученные результаты:

* Кластер 1 (самый большой кластер), вероятно, представлен пассивными клиентами (все значения равны нулю).
* Кластер 3, похоже, представлен группой клиентов, которые часто возвращают товары.

## <a name="clean-up-resources"></a>Очистка ресурсов

***Если вы не собираетесь продолжать работу с этим руководством***, удалите базу данных tpcxbb_1gb с сервера Базы данных SQL Azure.

На портале Azure сделайте следующее:

1. В меню слева на портале Azure выберите **Все ресурсы** или **Базы данных SQL**.
1. В поле **Фильтровать по имени…** введите **tpcxbb_1gb** и выберите свою подписку.
1. Выберите базу данных **tpcxbb_1gb**.
1. На странице **Обзор** выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В рамках второй части этой серии учебников вы выполнили такие задачи:

* определять число кластеров для алгоритма методом K-средних;
* выполнили кластеризацию;
* провели анализ результатов.

Чтобы развернуть созданную модель машинного обучения, следуйте указаниям из третьей части этой серии учебников:

> [!div class="nextstepaction"]
> [Руководство. Развертывание модели кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия)](sql-database-tutorial-clustering-model-deploy.md)