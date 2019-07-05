---
title: 'Выполнение R-сценария: Ссылка на модуль'
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать модуль выполнение сценария R в службе машинного обучения Azure для выполнения кода R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518057"
---
# <a name="execute-r-script"></a>Выполнение сценария R

В этой статье описывается использование **выполнение скрипта R** модуля для запуска кода R в вашем эксперименте визуального интерфейса.

С помощью R можно выполнять задачи, не поддерживаемые в настоящее время существующие модули такие как: 
- Создавать пользовательские преобразования данных
- Можно использовать собственные метрики для оценки прогнозов
- Создание моделей с помощью алгоритмов, которые не реализованы как автономные модули в визуальный интерфейс

## <a name="r-version-support"></a>Поддерживаемые версии R

Визуальный интерфейс службы машинного обучения Azure использует распределение (Comprehensive R Archive Network) CRAN R. Используемое в настоящее время версия — CRAN 3.5.1.

## <a name="supported-r-packages"></a>Поддерживаемые пакеты R

Среда R предварительно установлены с помощью более чем 100 пакетов. Полный список см. в разделе [пакетов R по](#pre-installed-r-packages).

Можно также добавить следующий код к любому **выполнение скрипта R** модуль просмотрите установленные пакеты.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Установка пакетов R
Чтобы установить дополнительные пакеты R, используйте `install.packages()` метод. Не забудьте указать репозиторий CRAN. Пакеты устанавливаются для каждого **выполнение скрипта R** модуля и не являются общими для другой **выполнение скрипта R** модулей.

В этом примере показано, как установить Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>Как настроить выполнение скрипта R

**Выполнение скрипта R** модуль содержит пример кода, который можно использовать в качестве отправной точки. Чтобы настроить **выполнение скрипта R** модуля, предоставить набор входных данных и код для выполнения.

![R-модуля](media/module/execute-r-script.png)

Наборы данных, хранящихся в визуальный интерфейс автоматически преобразуются в кадр данных R при загрузке с данным модулем.

1.  Добавить **выполнение скрипта R** эксперимент модуль.

    > [!NOTE]
    > Все данные, передаваемые **выполнение скрипта R** модуль преобразуется в R `data.frame` формат.

1. Подключите любой входных данных, необходимых в сценарии. Входные данные являются необязательными и могут быть данные и дополнительный код R.

    * **Dataset1**: Ссылаться на первую входную строку как `dataframe1`. Входной набор данных должен быть в формате CSV, TSV, ARFF или можно подключить набор данных машинного обучения Azure.

    * **Dataset2**: Ссылаться на вторую входную строку как `dataframe2`. Этот набор данных также должны быть в формате CSV, TSV, ARFF файл, или как набор данных машинного обучения Azure.

    * **Пакет скрипта**: Третий набор входных данных принимает ZIP-файлы. ZIP-файл может содержать несколько файлов и несколько типов файлов.

1. В **сценарий R** текстовом поле введите или вставьте допустимый сценарий R.

    Чтобы помочь вам приступить к работе, **сценарий R** текстовое поле заполняется заранее с помощью примера кода, который можно изменить или заменить.

    * Сценарий должен содержать функцию с именем `azureml_main`, который является точкой входа для этого модуля.

    * Функция точки входа может содержать до двух входных аргументов: `Param<dataframe1>` и `Param<dataframe2>`
 
    > [!NOTE]
    >  Имеющийся код R может потребоваться небольшие изменения для выполнения в эксперименте визуального интерфейса. Например входные данные, предоставляемые в формате CSV должен быть явно преобразованы в набор данных перед его использованием в коде. Типы данных и столбцов, используемые в языке R также отличаются в некотором смысле из типов данных и столбцов, используемых в визуальный интерфейс.

1.  **Случайное начальное значение**: Введите значение для использования в среде R в качестве случайного начального значения. Этот параметр эквивалентен вызову `set.seed(value)` в коде R.  

1. Запустите эксперимент.  

## <a name="results"></a>Результаты

**Выполнение скрипта R** модули могут возвращать несколько выходов, но их необходимо передать в качестве кадров данных R. Кадры данных автоматически преобразуются в визуальный интерфейс наборов данных для совместимости с другими модулями.

Стандартные сообщения и ошибки из R возвращаются в журнала модуля.

## <a name="sample-scripts"></a>Примеры сценариев

Существует множество способов расширения эксперимента с помощью пользовательского R-скрипта.  Этот раздел содержит пример кода для общих задач.


### <a name="add-r-script-as-an-input"></a>Добавить скрипт R в качестве входных данных

**Выполнение скрипта R** модуль поддерживает произвольные файлы скрипта R в качестве входных данных. Чтобы сделать это, она должна быть передана в рабочую область как часть ZIP-файл.

1. Чтобы передать ZIP-файл, содержащий код R в рабочую область, щелкните **New**, нажмите кнопку **набора данных**, а затем выберите **из локального файла** и **ZIP-файл**параметр.  

1. Убедитесь, что ZIP-файл доступен в **сохраненные наборы данных** списка.

1.  Подключите набор данных для **пакет сценария** входным портом.

1. Во время эксперимента, время выполнения доступны все файлы, которые содержатся в ZIP-файле. 

    Если файл пакета скрипта содержит структуру каталогов, сохраняется структура. Тем не менее, необходимо сначала изменить код, чтобы добавить каталог **. / объединить скрипт** к пути.

### <a name="process-data"></a>Обработка данных

Ниже приведен пример, как выполнять масштабирование и нормализации входных данных:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Чтение ZIP-файл в качестве входных данных

В этом примере показано, как использовать набор данных, в ZIP-файл в качестве входных данных **выполнение скрипта R** модуля.

1. Создайте файл данных в формате CSV и назовите его «mydatafile.csv».
1. Создайте ZIP-файл и добавьте в CSV-файле в архив.
1. Отправьте ZIP-файл в рабочую область машинного обучения Azure. 
1. Результирующий набор данных для подключения **ScriptBundle** ввода вашей **выполнение скрипта R** модуля.
1. Используя следующий код для чтения данных CSV-ФАЙЛ из ZIP-файл.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Репликация строк

В этом примере показано, как выполнить репликацию положительных записей в набор данных, чтобы сбалансировать выборку:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Передавать объекты R между модулями выполнения скриптов R

Вы можете передавать объекты R между экземплярами **выполнение скрипта R** модуля с помощью внутреннего механизма сериализации. В этом примере предполагается, что вы хотите переместить объект R с именем `A` между двумя **выполнение скрипта R** модулей.

1. Добавить область **выполнение скрипта R** модуля вашего эксперимента и введите следующий код в **сценарий R** текстового поля, чтобы создать сериализованный объект `A` как столбец в модуле таблицы выходных данных:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Выполняется явное преобразование в целочисленный тип, так как функция сериализации выводит данные в R `Raw` формате, который не поддерживается службой визуального интерфейса.

1. Добавьте второй экземпляр **выполнение скрипта R** модуля и подключить его к порту вывода предыдущего модуля.

1. Введите следующий код в **сценарий R** текстового поля, чтобы извлечь объект `A` из таблицы входных данных. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Предварительно установленные пакеты R

Текущий список предустановленных пакетов R, можно использовать:

|              |            | 
|--------------|------------| 
| Пакет      | Version    | 
| askpass      | 1,1        | 
| assertthat   | 0.2.1      | 
| бэкпортированным    | 1.1.4      | 
| Base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| Broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| Курсор        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| интерфейс командной строки          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| цветовое пространство   | 1.4-1      | 
| компилятор     | 3.5.1      | 
| Карандаш       | 1.3.4      | 
| curl         | 3.3        | 
| Data.Table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| КОМПАНИЯ DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0,14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| внешний      | 0.8-71     | 
| FS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Универсальные шаблоны     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| связующий         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| Графики     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| Сетка         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| не использовали        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| Итераторы    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| Добавление меток     | 0,3        | 
| решетки      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| ЗАПОМИНАЮЩИХ УСТРОЙСТВ         | 7.3-51.4   | 
| Матрица       | 1.2-17     | 
| Методы      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| OpenSSL      | 1.4        | 
| Параллельный     | 3.5.1      | 
| Принцип       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| ход выполнения     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| RandomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| RCPP         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| Рецепты      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| Масштабирует       | 1.0.0      | 
| Выберите нужный      | 0.4-1      | 
| Пространственные      | 7.3-11     | 
| сплайны      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| Практические советы     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| средства        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| Utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| Ограничитель выбросов      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml-файл         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| зоопарк          | 1.8-6      | 

## <a name="next-steps"></a>Дальнейшие действия

См. в разделе [набор модулей, доступных](module-reference.md) для службы машинного обучения Azure. 