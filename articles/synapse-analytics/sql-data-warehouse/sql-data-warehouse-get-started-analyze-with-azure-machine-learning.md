---
title: Анализ данных с помощью машинного обучения Azure
description: Используйте Azure Machine Learning для создания модели прогностического машинного обучения на основе данных, хранящихся в Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 74a6d1aecfc83ea68b9e30453056d231f4bf3e65
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416176"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Анализ данных с помощью машинного обучения Azure
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

В этом учебнике используется Azure Machine Learning для создания модели прогностического машинного обучения на основе данных, хранящихся в Azure Synapse. В частности, мы создадим кампанию целевого маркетинга для Adventure Works (магазин велосипедов). Для этого мы составим прогноз вероятности того, что клиент купит велосипед.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для пошагового изучения этого учебника потребуется следующее:

* Пул с предварительной загрузкой с помощью выборочных данных AdventureWorksDW. Для этого [см. Создать пул S'L](create-data-warehouse-portal.md) и выбрать загрузку выборочных данных. Если хранилище данных уже существует, но в нем нет демонстрационных данных, вы можете [загрузить их вручную](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Получить данные
Данные содержатся в представлении dbo.vTargetMail базы данных AdventureWorksDW. Чтобы считать эти данные, сделайте следующее.

1. Войдите в [Студию машинного обучения Azure](https://studio.azureml.net/) и щелкните My experiments (Мои эксперименты).
2. Нажмите **кнопку «NEW»** в левом нижнем углу экрана и выберите **Пустой эксперимент.**
3. Введите для своего эксперимента имя «Целевой маркетинг».
4. Перетащите модуль **данных Импорта** под **ввод данных и выход** из панели модулей в холст.
5. Укажите детали вашего пула S'L в панели Properties.
6. Создайте **запрос** к базе данных на чтение нужных данных.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Выполнить эксперимент, нажав **Run** под холст эксперимента.

![Выполнение эксперимента](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Когда эксперимент будет завершен, щелкните порт вывода в нижней части модуля чтения и выберите **Визуализировать**, чтобы просмотреть импортированные данные.

![Просмотр импортированных данных](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Очистите данные
Чтобы очистить данные, мы удалим некоторые столбцы, которые не являются значимыми для этой модели. Для этого:

1. Перетащите **выбранные столбцы в** модуле набора данных под **трансформацией данных < манипуляции** в холст. Подключите этот модуль к модулю **данных импорта.**
2. В области "Свойства" щелкните **Launch column selector** (Запустить средство выбора столбцов), чтобы указать столбцы, которые вы хотите удалить.

   ![Прогнозирование столбцов](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Исключите два столбца: CustomerAlternateKey и GeographyKey.

   ![Удаление ненужных столбцов](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Построить модель
Мы выполним разбивку данных в соотношении 80 к 20: 80 % для подготовки модели машинного обучения и 20 % для проверки модели. Мы будем использовать алгоритмы "Двухклассных" для этой проблемы двоичной классификации.

1. Перетащите модуль **разбивки** на холст.
2. В панели свойств введите 0,8 для фракции строк в первом наборе выходных данных.

   ![Разделение данных на обучающую и тестовую выборки](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Перетащите на холст модуль **Двухклассовое увеличивающееся дерево принятия решений** .
4. Перетащите модуль **модели поезда** в холст и укажите входные данные, подключив его к **двухклассным увеличенным дереву решений** (алгоритм ML) и **Split** (данные для обучения алгоритма) модулей. 

     ![Подключение модуля «Обучение модели»](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. В области «Свойства» щелкните **Запустить средство выбора столбцов** . Выберите столбец **BikeBuyer** в качестве прогнозируемого.

   ![Выбор столбца для прогнозирования](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Оценка модели
Теперь мы протестируем эффективность модели с использованием тестовых данных. Мы сравним наш алгоритм выбора с другими алгоритмами и посмотрим, какой работает лучше.

1. Перетащите модуль **модели оценки** в холст и подключите его к модулям **Train Model** и **Split Data.**

   ![Оценка модели](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Перетащите на холст эксперимента модуль **Двухклассная точечная машина Байеса** . Мы сравним производительность этого алгоритма в сравнении с алгоритмом увеличивающегося дерева принятия решений.
3. Скопируйте и вставьте модули «Обучение модели» и «Оценка модели» на холсте.
4. Перетащите на холст модуль **Оценка модели** для сравнения двух алгоритмов.
5. **Запустите** эксперимент.

   ![Выполнение эксперимента](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Щелкните правой кнопкой мыши порт вывода модуля «Анализ модели», а затем щелкните «Отобразить».

   ![Отображение результатов классификации](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

Приведенные метрики представляют кривую ROC, диаграмму точного отзыва и кривую подъема. При изучении этих метрик становится понятно, что первая модель является более эффективной, чем вторая. Чтобы посмотреть на то, что предсказала первая модель, нажмите на выходной порт модели Оценка и нажмите Визуализировать.

![Отображение результатов вычисления](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Вы увидите два дополнительных столбца, добавленные в тестовый набор данных.

* Оценка вероятности: вероятность того, что клиент является покупателем велосипеда.
* Метка оценки: выполненная моделью классификация — покупатель велосипеда (1) или нет (0). Порог вероятности для маркировки равен 50 % и может быть изменен.

Сравнение столбца BikeBuyer (фактическое значение) и столбца «Метка оценки» (прогнозное значение) позволяет оценить эффективность выполнения модели. Далее можно использовать эту модель для прогнозирования новых клиентов и публиковать эту модель в виде веб-сервиса или записывать результаты в Azure Synapse.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании прогнозных моделей машинного обучения см. в статье [Введение в машинное обучение в Azure](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/).