---
title: Анализ данных с помощью машинного обучения Azure
description: Создание прогнозной модели машинного обучения с помощью Машинного обучения Azure и данных из Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a0e4660967dafec8e314fd681d05e694e562b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368198"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Анализ данных с помощью машинного обучения Azure
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

В этом руководстве показано, как построить прогнозную модель машинного обучения с помощью Машинного обучения Azure и данных из Azure Synapse. В частности, мы создадим кампанию целевого маркетинга для Adventure Works (магазин велосипедов). Для этого мы составим прогноз вероятности того, что клиент купит велосипед.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для пошагового изучения этого учебника потребуется следующее:

* Пул SQL, в который предварительно загружены демонстрационные данные AdventureWorksDW. Чтобы подготовить пул, выберите загрузку демонстрационных данных и следуйте указаниям, приведенным в статье [Создание пула SQL](create-data-warehouse-portal.md). Если хранилище данных уже существует, но не содержит демонстрационные данные, вы можете [загрузить их вручную](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Получение данных
Данные содержатся в представлении dbo.vTargetMail базы данных AdventureWorksDW. Чтобы считать эти данные, сделайте следующее.

1. Войдите в [Студию машинного обучения Azure](https://studio.azureml.net/) и щелкните «Мои эксперименты».
2. В левом нижнем углу экрана щелкните **Создать** и выберите **Blank Experiment** (Пустой эксперимент).
3. Введите имя эксперимента. Целевой маркетинг.
4. В разделе **Data Input and output** (Входные и выходные данные) перетащите модуль **Импорт данных** с панели модулей на холст.
5. Укажите сведения о пуле SQL в области "Свойства".
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

Запустите эксперимент, щелкнув элемент **Пуск** под холстом эксперимента.

![Выполнение эксперимента](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Когда эксперимент будет завершен, щелкните порт вывода в нижней части модуля чтения и выберите **Визуализировать**, чтобы просмотреть импортированные данные.

![Просмотр импортированных данных](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Очистка данных
Чтобы очистить данные, мы удалим некоторые столбцы, которые не являются значимыми для этой модели. Для этого:

1. В разделе **Data Transformation < Manipulation** (Преобразование данных < Манипуляции) перетащите модуль **Select Columns in Dataset** (Выбор столбцов в наборе данных) на холст. Подключите этот модуль к модулю **Импорт данных**.
2. В области "Свойства" щелкните **Launch column selector** (Запустить средство выбора столбцов), чтобы указать столбцы, которые вы хотите удалить.

   ![Столбцы проекта](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Исключите два столбца. CustomerAlternateKey и GeographyKey.

   ![Удаление ненужных столбцов](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Построение модели
Мы выполним разбивку данных в соотношении 80 к 20: 80 % для подготовки модели машинного обучения и 20 % для проверки модели. Для этой задачи бинарной классификации будут использоваться двухклассовые алгоритмы.

1. Перетащите модуль **разбивки** на холст.
2. На панели свойств в поле "Доля строк в первом наборе выходных данных" введите значение 0,8.

   ![Разделение данных на обучающую и тестовую выборки](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Перетащите на холст модуль **Двухклассовое увеличивающееся дерево принятия решений** .
4. Перетащите модуль **Обучающая модель** на холст и укажите входные данные, подключив его к модулям **Two-Class Boosted Decision Tree** (двухклассовое усиленное дерево решений) и **Split** (разделение данных для обучения алгоритма). 

     ![Подключение модуля «Обучение модели»](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. В области «Свойства» щелкните **Запустить средство выбора столбцов** . Выберите столбец **BikeBuyer** в качестве прогнозируемого.

   ![Выбор столбца для прогнозирования](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Оценка модели
Теперь мы протестируем эффективность модели с использованием тестовых данных. Мы сравним наш алгоритм выбора с другими алгоритмами и посмотрим, какой работает лучше.

1. Перетащите модуль **Score Model** (Оценка модели) на холст и подключите его к модулям **Обучение модели** и **Split Data** (Разделение данных).

   ![Оценка модели](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Перетащите на холст эксперимента модуль **Двухклассная точечная машина Байеса** . Мы сравним производительность этого алгоритма в сравнении с алгоритмом увеличивающегося дерева принятия решений.
3. Скопируйте и вставьте модули «Обучение модели» и «Оценка модели» на холсте.
4. Перетащите на холст модуль **Оценка модели** для сравнения двух алгоритмов.
5. **Запустите** эксперимент.

   ![Выполнение эксперимента](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Щелкните правой кнопкой мыши порт вывода модуля «Анализ модели», а затем щелкните «Отобразить».

   ![Отображение результатов классификации](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

Отображенные метрики представляют собой ROC-кривую, lift-кривую, а также диаграмму соотношения полноты и точности. При изучении этих метрик становится понятно, что первая модель является более эффективной, чем вторая. Чтобы узнать прогнозные значения первой модели, выберите порт вывода модуля "Оценка модели" и щелкните "Отобразить".

![Отображение результатов вычисления](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Вы увидите два дополнительных столбца, добавленные в тестовый набор данных.

* Оценка вероятности: вероятность того, что клиент является покупателем велосипеда.
* Метка оценки: выполненная моделью классификация — покупатель велосипеда (1) или нет (0). Порог вероятности для маркировки равен 50 % и может быть изменен.

Сравнение столбца BikeBuyer (фактическое значение) и столбца «Метка оценки» (прогнозное значение) позволяет оценить эффективность выполнения модели. Далее эту модель можно использовать для прогнозирования количества новых клиентов. Вы можете затем опубликовать эту модель как веб-службу или записать результаты в Azure Synapse.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании прогнозных моделей машинного обучения см. в статье [Введение в машинное обучение в Azure](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).