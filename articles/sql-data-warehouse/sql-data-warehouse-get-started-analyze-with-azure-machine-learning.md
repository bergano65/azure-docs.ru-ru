---
title: Анализ данных с помощью машинного обучения Azure
description: Создание прогнозной модели машинного обучения с помощью машинного обучения Azure и данных из хранилища данных SQL Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f91960eaac92047e76275e63b1feaf471de3bac3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692799"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Анализ данных с помощью машинного обучения Azure
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

В этом руководстве показано, как построить прогнозную модель машинного обучения с помощью машинного обучения Azure и данных из хранилища данных SQL Azure. В частности, мы создадим кампанию целевого маркетинга для Adventure Works (магазин велосипедов). Для этого мы составим прогноз вероятности того, что клиент купит велосипед.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для выполнения этих действий необходимо иметь следующее:

* Хранилище данных SQL, в которое предварительно загружены демонстрационные данные AdventureWorksDW. Чтобы сделать это, ознакомьтесь со статьей [Создание хранилища данных SQL Azure][Create a SQL Data Warehouse] и выберите загрузку демонстрационных данных. Если хранилище данных уже существует, но не содержит демонстрационные данные, вы можете [загрузить их вручную][load sample data manually].

## <a name="1-get-the-data"></a>1. получение данных
Данные содержатся в представлении dbo.vTargetMail базы данных AdventureWorksDW. Чтобы считать эти данные, сделайте следующее.

1. Войдите в [Студию машинного обучения Azure][Azure Machine Learning studio] и щелкните «Мои эксперименты».
2. Щелкните **+ создать** в левом нижнем углу экрана и выберите **пустой эксперимент**.
3. Введите для своего эксперимента имя «Целевой маркетинг».
4. Перетащите модуль **Импорт данных** под элементом **Вход и вывод данных** из панели модули в холст.
5. Укажите сведения о базе данных хранилища данных SQL на панели свойств.
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
![Запуск эксперимента][1]

Когда эксперимент будет завершен, щелкните порт вывода в нижней части модуля чтения и выберите **Визуализировать**, чтобы просмотреть импортированные данные.
![Просмотр импортированных данных][3]

## <a name="2-clean-the-data"></a>2. Очистка данных
Чтобы очистить данные, мы удалим некоторые столбцы, которые не являются значимыми для этой модели. Для этого:

1. Перетащите модуль **Выбор столбцов в наборе** данных в разделе **Преобразование данных < манипуляции** с холстом. Подключите этот модуль к модулю **Импорт данных** .
2. В области "Свойства" щелкните **Launch column selector** (Запустить средство выбора столбцов), чтобы указать столбцы, которые вы хотите удалить.
   ![Столбцы проекта][4]
3. Исключите два столбца: CustomerAlternateKey и GeographyKey.
   ![Удаление ненужных столбцов][5]

## <a name="3-build-the-model"></a>3. Построение модели
Мы выполним разбивку данных в соотношении 80 к 20: 80 % для подготовки модели машинного обучения и 20 % для проверки модели. Для этой задачи бинарной классификации будут использоваться двухклассовые алгоритмы.

1. Перетащите модуль **разбивки** на холст.
2. На панели свойств введите 0,8 для целой части строк в первом выходном наборе данных.
   ![Разделение данных на обучающую и тестовую выборки][6]
3. Перетащите на холст модуль **Двухклассовое увеличивающееся дерево принятия решений** .
4. Перетащите модуль **обучение модели** на холст и укажите входные данные, подключив его к **высококлассному дереву принятия решений** (алгоритм машинного обучения) и **разделите** (данные для обучения алгоритма). 
     ![Подключение модуля "Обучение модели"][7]
5. В области «Свойства» щелкните **Запустить средство выбора столбцов** . Выберите столбец **BikeBuyer** в качестве прогнозируемого.
   ![Выбор столбца для прогнозирования][8]

## <a name="4-score-the-model"></a>4. Оценка модели
Теперь мы протестируем эффективность модели с использованием тестовых данных. Мы сравним наш алгоритм выбора с другими алгоритмами и посмотрим, какой работает лучше.

1. Перетащите модуль **Оценка модели** на холст и подключите его к модулю **обучение модели** и **разбиению данных** .
   ![оценить модель][9]
2. Перетащите на холст эксперимента модуль **Двухклассная точечная машина Байеса** . Мы сравним производительность этого алгоритма в сравнении с алгоритмом увеличивающегося дерева принятия решений.
3. Скопируйте и вставьте модули «Обучение модели» и «Оценка модели» на холсте.
4. Перетащите на холст модуль **Оценка модели** для сравнения двух алгоритмов.
5. **Запустите** эксперимент.
   ![Запуск эксперимента][10]
6. Щелкните правой кнопкой мыши порт вывода модуля «Анализ модели», а затем щелкните «Отобразить».
   ![Отображение результатов классификации][11]

Отображенные метрики представляют собой ROC-кривую, lift-кривую, а также диаграмму соотношения полноты и точности. При изучении этих метрик становится понятно, что первая модель является более эффективной, чем вторая. Чтобы узнать прогнозные значения первой модели, щелкните порт вывода модуля «Оценка модели» и щелкните «Отобразить».
![Отображение результатов вычисления][12]

Вы увидите два дополнительных столбца, добавленные в тестовый набор данных.

* Оценка вероятности: вероятность того, что клиент является покупателем велосипеда.
* Метка оценки: выполненная моделью классификация — покупатель велосипеда (1) или нет (0). Порог вероятности для маркировки равен 50 % и может быть изменен.

Сравнение столбца BikeBuyer (фактическое значение) и столбца «Метка оценки» (прогнозное значение) позволяет оценить эффективность выполнения модели. В дальнейшем эту модель можно использовать для прогнозирования количества новых клиентов. Вы можете затем опубликовать эту модель как веб-службу или записать результаты в хранилище данных SQL.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании прогнозирующих моделей машинного обучения см. в статье [Введение в машинное обучение в Azure][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
