---
title: 'Выполнить R Сценарий: Модуль Ссылка'
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать модуль Execute R Script в Azure Machine Learning для запуска R-кода.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: f038293b48956ac89314e426df3f5dc491954df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064222"
---
# <a name="execute-r-script"></a>Выполнение сценария R

В этой статье описывается, как использовать модуль **Execute R Script** для запуска R-кода в конвейере (предварительном просмотре) конструктора машинного обучения Azure.

С помощью R можно выполнять задачи, которые в настоящее время не поддерживаются существующими модулями, такими как: 
- создание настраиваемых преобразований данных;
- Используйте свои собственные метрики для оценки прогнозов
- Создавайте модели с использованием алгоритмов, которые не реализованы в качестве автономных модулей в конструкторе

## <a name="r-version-support"></a>Поддержка R-версии

Дизайнер машинного обучения Azure использует распределение КРАН (Comprehensive R Archive Network) R. Используемая в настоящее время версия CRAN 3.5.1.

## <a name="supported-r-packages"></a>Поддерживаемые пакеты R

Среда R предварительно установлена с более чем 100 пакетами. Полный список можно опереться в разделе [Предустановленные пакеты R.](#pre-installed-r-packages)

Вы также можете добавить следующий код в любой модуль **Execute R Script** и увидеть установленные пакеты.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Установка пакетов R
Для установки дополнительных пакетов R используйте `install.packages()` метод. Обязательно укажите репозиторий CRAN. Пакеты устанавливаются для каждого модуля **Execute R Script** и не используются в других модулях Execute R **Script.**

Этот пример показывает, как установить зоопарк:
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
 > [!NOTE]
  > Пожалуйста, проверьте, если пакет уже существует перед установкой, чтобы избежать повторной установки. Как `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` в вышеобразном коде образца. Повторная установка может привести к тайм-ауту запроса веб-службы.     

## <a name="upload-files"></a>Upload files
**Сценарий выполнения R** поддерживает загрузку файлов с помощью Azure Machine Learning R SDK.

Ниже приводится следующий пример, как загрузить файл изображения в **сценарий Execute R:**
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

После завершения выполнения конвейера можно просмотреть изображение в правой панели модуля

> [!div class="mx-imgBorder"]
> ![Загружено-изображение](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Как настроить сценарий выполнения R

Модуль **Execute R Script** содержит пример кода, который можно использовать в качестве отправной точки. Чтобы настроить модуль **Execute R Script,** предоставьте набор входов и код для выполнения.

![R-модуль](media/module/execute-r-script.png)

Наборы данных, хранящиеся в конструкторе, автоматически преобразуются в рамку данных R при загрузке с помощью этого модуля.

1.  Добавьте модуль **Execute R Script** в конвейер.

  

1. Подключите все входные данные, необходимые скрипту. Входные данные не являются обязательными и могут включать данные и дополнительный R-код.

    * **Dataset1**: Ссылка на `dataframe1`первый вход как . Набор входных данных должен быть отформатирован как CSV, TSV, ARFF, или вы можете подключить набор данных машинного обучения Azure.

    * **Dataset2**: Ссылка на `dataframe2`второй вход как . Этот набор данных также должен быть отформатирован как файл CSV, TSV, ARFF или как набор данных машинного обучения Azure.

    * **Набор скриптов**: Третий вход принимает файлы зип. Застегнутый на молнию файл может содержать несколько файлов и несколько типов файлов.

1. В текстовом окне **R** введите или вставьте действительный R-скрипт.

    Чтобы помочь вам начать работу, текстовое окно **R Script** предварительно заселено примером кода, который вы можете отобразить или заменить.
    
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

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * Скрипт должен содержать `azureml_main`функцию, названную, которая является точкой входа для этого модуля.

 * Функция точки входа может содержать до двух `Param<dataframe1>` аргументов ввода: и`Param<dataframe2>`
 
   > [!NOTE]
    > Данные, передаваемые в модуль `dataframe1` `dataframe2`Execute **R Script,** называются как и, `dataset1`который `dataset2`отличается от дизайнера машинного обучения Azure (ссылка дизайнера как , ). Пожалуйста, проверьте, чтобы убедиться, что входные данные ссылаются правильно в вашем скрипте.  
 
    > [!NOTE]
    >  Существующий R-код может потребовать незначительных изменений для выполнения в конвейере конструктора. Например, данные ввода, которые вы предоставляете в формате CSV, должны быть явно преобразованы в набор данных, прежде чем вы сможете использовать его в коде. Типы данных и столбцов, используемые на языке R, также в некотором смысле отличаются от типов данных и столбцов, используемых в проектировщике.

1.  **Случайное семя**: Введите значение для использования внутри среды R в качестве случайного значения семян. Действие этого параметра эквивалентно вызову `set.seed(value)` в коде R.  

1. Отправить конвейер.  

## <a name="results"></a>Результаты

Модули **Execute R Script** могут возвращать несколько выходов, но они должны предоставляться в виде рамок данных R. Кадры данных автоматически преобразуются в наборы данных в конструкторе для совместимости с другими модулями.

Стандартные сообщения и ошибки из R возвращаются в журнал модуля.

Если вам нужно распечатать результаты в R-скрипте, вы можете найти печатные результаты в **70_driver_log** под **вкладкой Выходов в** правой панели модуля.

## <a name="sample-scripts"></a>Примеры сценариев

Существует множество способов расширения конвейера с помощью пользовательского R-скрипта.  В этом разделе приводится пример кода для общих задач.


### <a name="add-r-script-as-an-input"></a>Добавление R-скрипта в качестве ввода

Модуль **Execute R Script** поддерживает произвольные r-файлы сценариев в качестве входных данных. Для этого они должны быть загружены в рабочее пространство как часть файла ЗИП.

1. Чтобы загрузить файл, содержащий R-код, в рабочее пространство, перейдите на страницу **активов наборов данных,** нажмите **«Создать набор данных»,** а затем выберите **из локального файла** и опции типа набора данных **файла.**  

1. Убедитесь, что файл с молнией доступен в списке **my Datasets** в категории **Datasets** в левом дереве модуля.

1.  Подключите набор данных к вхотвому порту **Скрипта Bundle.**

1. Все файлы, содержащиеся в файле «ЗИП», доступны во время выполнения конвейера. 

    Если файл пакета скриптов содержал структуру каталога, структура сохраняется. Тем не менее, необходимо изменить свой код, чтобы подвести каталог **./Script Bundle** к пути.

### <a name="process-data"></a>Обработка данных

В следующем примере показано, как масштабировать и нормализовать входные данные:

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

### <a name="read-a-zip-file-as-input"></a>Прочитайте файл в качестве ввода в качестве ввода

В этом примере показано, как использовать набор данных в файле «ЗИП» в качестве ввода модуля **Execute R Script.**

1. Создайте файл данных в формате CSV и назовите его "mydatafile.csv".
1. Создайте файл и добавьте файл CSV в архив.
1. Загрузите файл на молнии в рабочее пространство Azure Machine Learning. 
1. Подключите полученный набор данных к вхотвому ввода **ScriptBundle** в модуле **Execute R Script.**
1. Использование следующего кода для чтения данных CSV из застежки-молнии файла.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Репликатные строки

Этот пример показывает, как реплицировать положительные записи в наборе данных, чтобы сбалансировать образец:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Передайте r-объекты между модулями Execute R Script

Вы можете передавать объекты R между экземплярами модуля **Выполнение скрипта R** с помощью внутреннего механизма сериализации. Этот пример предполагает, что вы хотите `A` переместить объект R, названный между двумя модулями **Execute R Script.**

1. Добавьте первый модуль **Execute R Script** в конвейер и введите следующий код в текстовом поле R **Script** для создания сериализованного объекта `A` в качестве столбца в таблице данных модуля:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Явное преобразование в тип рядов делается потому, `Raw` что функция сериализации выводит данные в формате R, который не поддерживается конструктором.

1. Добавьте второй экземпляр модуля **Execute R Script** и подключите его к выходу порта предыдущего модуля.

1. Введите следующий код в текстовом поле **R Script** для извлечения объекта `A` из таблицы данных ввода. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Предварительно установленные R пакеты

Текущий список предварительно установленных R пакетов, доступных для использования:

|              |            | 
|--------------|------------| 
| Пакет      | Версия    | 
| askpass      | 1,1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1–3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0–6      | 
| boot         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| клипер        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4–1      | 
| компилятор     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| универсальные шаблоны     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| графика     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| сетка         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23–15    | 
| knitr        | 1,23       | 
| labeling     | 0,3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Матрица       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3–12     | 
| numDeriv     | 2016.8-1.1 | 
| OpenSSL      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| ход выполнения     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4–15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1–2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0–7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4–1      | 
| пространственный      | 7.3–11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
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
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3–2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| зоопарк          | 1.8–6      | 

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [набором модулей, доступных](module-reference.md) для машинного обучения Azure. 
