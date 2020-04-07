---
title: Узнать больше о Linux
titleSuffix: Azure Data Science Virtual Machine
description: Узнайте, как выполнить несколько общих задач по науке данных с помощью виртуальной машины Linux Data Science.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 1298d5fe1d7407e836f454c2130a913dde6f8eec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755141"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Наука о данных с виртуальной машиной Linux Data Science в Azure

Это пошаговое решение показывает, как выполнить несколько общих задач науки о данных с помощью Linux Data Science Виртуальная машина (DSVM). Linux DSVM — это виртуальное машинное изображение, доступное в Azure, которое предустановлено с набором инструментов, обычно используемых для анализа данных и машинного обучения. Ключевые компоненты программного обеспечения перечислены в [предоставлении Linux Data Science Виртуальная машина](linux-dsvm-intro.md). Изображение DSVM позволяет легко начать делать данные науки в считанные минуты, без установки и настройки каждого из инструментов в отдельности. Вы можете легко масштабировать DSVM, если вам нужно, и вы можете остановить его, когда он не используется. Ресурс DSVM является одновременно эластичным и экономичным.

Задачи по науке о данных, продемонстрированы в этом пошаговом шаге, следуют шагам, изложенным в [процессе исследования данных команды?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Процесс team Data Science Process представляет собой систематический подход к науке о данных, который помогает группам ученых в области данных эффективно сотрудничать в течение жизненного цикла создания интеллектуальных приложений. Кроме того, этот процесс может служить итеративной платформой для обработки и анализа данных, которую можно использовать самостоятельно.

В этом пошаговом шаге мы анализируем набор данных [о спам-базе.](https://archive.ics.uci.edu/ml/datasets/spambase) Spambase представляет собой набор писем, которые помечены либо спам или ветчина (не спам). Spambase также содержит некоторые статистические данные о содержании писем. Мы говорим о статистике позже в пошаговом пошаговом пошаговом пошаге.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем использовать Linux DSVM, необходимо иметь следующие предпосылки:

* **Подписка Azure**. Чтобы получить подписку Azure, смотрите [Создать бесплатную учетную запись Azure сегодня.](https://azure.microsoft.com/free/)
* [**Linux Data Science Виртуальная машина**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Для получения информации о подготовке виртуальной машины, [см.](linux-dsvm-intro.md)
* [**X2Go**](https://wiki.x2go.org/doku.php) установлен на вашем компьютере с открытой сессией XFCE. Для получения дополнительной информации [см.](linux-dsvm-intro.md#x2go)
* Для более плавного прокрутки, в веб-браузере Firefox DSVM, переключать `gfx.xrender.enabled` флаг в `about:config`. [Подробнее](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Также рассмотреть `False`вопрос об установке `mousewheel.enable_pixel_scrolling` . [Подробнее](https://support.mozilla.org/questions/981140).
* **Учетная запись машинного обучения Azure**. Если у вас его еще нет, зарегистрируйтесь на новой учетной записи на [главной странице Azure Machine Learning.](https://azure.microsoft.com/free/services/machine-learning//)

## <a name="download-the-spambase-dataset"></a>Скачивание набора данных spambase

Набор данных [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) представляет собой относительно небольшой набор данных, содержащий 4601 пример. Набор данных является удобным размером для демонстрации некоторых ключевых особенностей DSVM, поскольку он сохраняет скромные потребности в ресурсах.

> [!NOTE]
> Это пошаговое походе было создано с помощью D2 v2-размера Linux DSVM (Ubuntu 18.04 Edition). Вы можете использовать DSVM такого размера для завершения процедур, которые продемонстрированы в этом пошаговом шаге.

Если вам нужно больше места для хранения, вы можете создать дополнительные диски и прикрепить их к DSVM. Диски используют постоянное хранилище Azure, поэтому их данные сохраняются, даже если сервер перезаготовляется из-за повторного размера или выключен. Чтобы добавить диск и прикрепить его к DSVM, завершить шаги в [Добавить диск на Linux VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). В шагах для добавления диска используется Azure CLI, который уже установлен на DSVM. Вы можете полностью выполнить шаги от самого DSVM. Другим вариантом увеличения хранилища является использование [файлов Azure.](../../storage/files/storage-how-to-use-files-linux.md)

Чтобы загрузить данные, откройте окно терминала, а затем запустите эту команду:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

Загруженный файл не имеет строки заголовка. Давайте создадим еще один файл, в который есть заголовок. Для этого выполните следующую команду:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Затем соперейте два файла вместе:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

Набор данных имеет несколько типов статистических данных для каждого письма:

* Столбцы, как **слово\_freq\_WORD** указать процент слов в электронной почте, которые соответствуют *WORD*. Например, если **\_слово\_freq сделать** это **1**, то 1% всех слов в электронной почте были *сделать*.
* Столбцы, такие как **char\_freq\_CHAR,** указывают процент всех символов в электронной почте, которые являются *CHAR.*
* **capital\_run\_length\_longest** — самая длинная последовательность прописных букв.
* **capital\_run\_length\_average** — средняя длина всех последовательностей прописных букв.
* **capital\_run\_length\_total** — общая длина всех последовательностей прописных букв.
* **spam** указывает, были ли сообщения признаны нежелательной почтой (1 — нежелательная почта, 0 —обычная почта).

## <a name="explore-the-dataset-by-using-r-open"></a>Изучите набор данных с помощью R Open

Давайте рассмотрим данные и сделаем некоторые основные машинное обучение с помощью R. DSVM поставляется с предустановленной [Microsoft R Open.](https://mran.revolutionanalytics.com/open/) Многопоточные математические библиотеки в предустановленной версии R обеспечивают лучшую производительность, чем однопоточные версии. R Open также обеспечивает воспроизводимость через моментальный снимок репозитория пакета CRAN.

Чтобы получить копии образцов кода, которые используются в этом пошаговом шаге, используйте git для клонирования репозитория Azure-Machine-Learning-Data-Science. Git предустановлен на DSVM. На командной строке git, запустите:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Откройте окно терминала и начните новую сессию R в интерактивной консоли R. Вы также можете использовать RStudio, который предустановлен на DSVM.

Для импорта данных и настройки среды:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Чтобы просмотреть сводные статистические данные по каждому столбцу, выполните следующую команду:

```R
summary(data)
```

Чтобы получить другое представление данных, выполните следующую команду:

```R
str(data)
```

В этом представлении отображается тип каждой переменной и первые несколько значений в наборе данных.

Столбец **spam** распознан как целое число, но это на самом деле категориальная переменная (или факторная). Чтобы задать тип данных в этом столбце, выполните следующую команду:

```R
data$spam <- as.factor(data$spam)
```

Чтобы сделать некоторые исследовательский анализ, используйте пакет [ggplot2,](https://ggplot2.tidyverse.org/) популярная библиотека графиков для R, которая предустановлена на DSVM. На основе сводных данных, отображаемых ранее, мы имеем сводные статистические данные о частоте восклицательного знака характера. Давайте посюжетим эти частоты здесь, запустив следующие команды:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Поскольку нулевая планка искажает сюжет, давайте устраним его:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Существует нетривиальная плотность выше 1, что выглядит интересно. Давайте посмотрим только на эти данные:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Затем разделите его спамом по сравнению с ветчиной:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Эти примеры должны помочь вам сделать похожие сюжеты и исследовать данные в других столбцах.

## <a name="train-and-test-a-machine-learning-model"></a>Поезд и тестирование модели машинного обучения

Давайте обучим несколько моделей машинного обучения, чтобы классифицировать электронные письма в наборе данных как содержащие спам или ветчину. В этом разделе мы обучаем модель дерева решений и случайную модель леса. Затем мы проверяем точность прогнозов.

> [!NOTE]
> Пакет *Rpart* (Recursive Partition and Regression Trees), используемый в следующем коде, уже установлен на DSVM.

Во-первых, давайте разделим набор данных на наборы обучения и тестовые наборы:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Затем создайте дерево решений для классификации писем:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Ниже приведен результат:

![Диаграмма созданного дерева принятия решений](./media/linux-dsvm-walkthrough/decision-tree.png)

Чтобы определить, насколько хорошо эта модель работает с обучающим набором данных, используйте следующий код:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Чтобы определить, насколько хорошо она работает с тестовым набором данных:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Теперь давайте испробуем модель случайного леса. Случайные леса тренируют множество деревьев решений и выводит класс, который является режимом классификации из всех отдельных деревьев решений. Они обеспечивают более мощный подход к машинного обучению, поскольку они корректируют тенденцию модели дерева решений переукладывать набор обучающих данных.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Учебники по глубокому обучению и пошаговые руководства

В дополнение к рамочным пробам предоставляется также набор всеобъемлющих пошагов. Эти пошаговые руководства помогут вам быстро приступить к разработке приложений для глубокого обучения в таких областях, как распознавание изображений, текста и языка.

- [Запуск нейронных сетей в различных средах:](https://github.com/ilkarman/DeepLearningFrameworks)всеобъемлющее пошаговое пошаговое походе, которое показывает, как перенести код из одной инфраструктуры в другую. В нем также показано, как сравнивать производительность модели и времени выполнения в рамках. 

- [Руководство по созданию сквозного решения для обнаружения продуктов в изображениях:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)обнаружение изображений — это метод, который может находить и классифицировать объекты в изображениях. Технология имеет потенциал, чтобы принести огромные награды во многих реальных бизнес-областях. Например, розничные продавцы могут использовать этот метод для определения того, какой продукт клиент взял с полки. Эти сведения, в свою очередь, помогают управлять запасами продукции. 

- [Глубокое обучение для аудио](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Этот учебник показывает, как обучать модели глубокого обучения для обнаружения аудио событий на [городских звуковых наборов данных.](https://urbansounddataset.weebly.com/) В учебнике содержится обзор того, как работать с аудиоданными.

- [Классификация текстовых документов](https://github.com/anargyri/lstm_han): Это пошаговое пошаговое сообщение демонстрирует, как строить и обучать две различные нейронные сетевые архитектуры: Иерархическая сеть внимания и долгосрочная краткосрочная память (LSTM). Эти нейронные сети выполняют классификацию текстовых документов с помощью API Keras для глубокого обучения. Keras — это интерфейс для трех самых популярных платформ машинного обучения: Microsoft Cognitive Toolkit, TensorFlow и Theano.

## <a name="other-tools"></a>Другие средства

Остальные разделы показывают, как использовать некоторые из инструментов, которые установлены на Linux DSVM. Мы обсуждаем следующие инструменты:

* XGBoost;
* Python
* JupyterHub
* Rattle;
* ПостгреСЗЛ и СКуиррел СЗЛ
* хранилище данных SQL Server.

### <a name="xgboost"></a>XGBoost;

[XGBoost](https://xgboost.readthedocs.org/en/latest/) обеспечивает быструю и точную реализацию увеличенного дерева.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

XGBoost также может звонить с Python или командной строки.

### <a name="python"></a>Python

Для разработки Python на DSVM установлены дистрибутивы Anaconda Python 3.5 и 2.7.

> [!NOTE]
> Распределение Anaconda включает [В Конду.](https://conda.pydata.org/docs/index.html) Conda можно использовать для создания пользовательских сред Python, в которых установлены различные версии или пакеты.

Давайте прочитаем в некоторых наборах данных spambase и классифицировать электронные письма с машинами поддержки векторных в Scikit-учиться:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Для создания прогнозов используем следующую команду:

```Python
clf.predict(X.ix[0:20, :])
```

Чтобы продемонстрировать, как опубликовать конечную точку машинного обучения Azure, давайте сделаем более базовую модель. Мы будем использовать три переменные, которые мы использовали, когда мы опубликовали модель R ранее:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Чтобы опубликовать модель для машинного обучения Azure:

```Python
# Publish the model.
workspace_id = "<workspace-id>"
workspace_token = "<workspace-token>"
from azureml import services
@services.publish(workspace_id, workspace_token)
@services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
@services.returns(int) # 0 or 1
def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
    inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
    return clf.predict(inputArray)

# Get some info about the resulting model.
predictSpam.service.url
predictSpam.service.api_key

# Call the model
predictSpam.service(1, 1, 1)
```

> [!NOTE]
> Эта опция доступна только для Python 2.7. Он еще не поддерживается на Python 3.5. Для запуска используйте **/anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

Распределение Anaconda в DSVM поставляется с ноутбуком Jupyter, кросс-платформенной средой для совместного использования кода Python, R или Julia. Ноутбук Jupyter доступен через JupyterHub. Вы врегистрируетесь, используя локальное имя\<пользователя Linux и пароль на\>https:// dSVM DNS имя или IP-адрес:8000/. Все файлы конфигурации для JupyterHub находятся в /etc/jupyterhub.

> [!NOTE]
> Чтобы использовать менеджер пакетов `pip` Python (через команду) из ноутбука Jupyter в текущем ядре, используйте эту команду в ячейке кода:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Чтобы использовать установщик Conda (через `conda` команду) из ноутбука Jupyter в текущем ядре, используйте эту команду в ячейке кода:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Несколько образцов ноутбуков уже установлены на DSVM:

* Пример ноутбуков Python:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Пример R ноутбук:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Язык Джулии также доступен из командной строки на Linux DSVM.

### <a name="rattle"></a>Rattle;

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*nalytical *T* *ool T*o *l*earn *E*asily) является графическим r-инструментом для интеллектуального анализа данных. Rattle имеет интуитивно понятный интерфейс, который упрощает загрузку, изучение и преобразование данных, а также создание и оценку моделей. [Rattle: Графический интерфейс data Mining для R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) обеспечивает пошаговое руководство, демонстрируя особенности Rattle.

Установите и начните Rattle, запустив следующие команды:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Вам не нужно устанавливать Rattle на DSVM. Тем не менее, вам может быть предложено установить дополнительные пакеты, когда Rattle открывается.

В Rattle используется интерфейс на основе вкладок. Большинство вкладок соответствуют шагам в [процессе team Data Science,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)таким как загрузка данных или изучение данных. Процесс обработки и анализа данных выполняется слева направо, перемещаясь по вкладкам. Последняя вкладка содержит журнал команд R, которые были запущены Rattle.

Чтобы загрузить и настроить набор данных:

1. Для загрузки файла выберите вкладку **Data.**
1. Выберите селектор рядом с **Filename,** а затем выберите **spambaseHeaders.data.**
1. Чтобы загрузить файл, Щелкните **Выполнить**. Вы должны увидеть резюме каждого столбца, включая его идентифицированный тип данных; будь то вход, цель или другой тип переменной; и количество уникальных ценностей.
1. Rattle правильно распознал тип данных столбца **spam** как целевой объект. Выберите столбец **спама,** а затем установите **тип целевых данных** на **Categoric.**

Чтобы изучить данные:

1. Выберите вкладку **Explore** (Изучение).
1. Чтобы просмотреть информацию о типах переменных и некоторые сводные статистические данные, выберите **Резюме** > **Выполнения**.
1. Чтобы просмотреть другие типы статистических данных о каждой переменной, выберите другие варианты, такие как **Описание** или **Основы.**

Вы также можете использовать вкладку **«Исследуйте»** для генерации проницательных сюжетов. Чтобы построить гистограмму данных:

1. Установите флажок **Distributions**(Дистрибутивы).
1. Для **word_freq_remove** и **word_freq_you**, выберите **Гистограмма**.
1. Выберите **Выполнить**. Вы должны увидеть оба участка плотности в одном окне графика, где ясно, что слово _вы_ появляетсяе гораздо чаще в письмах, чем _удалить_.

Интересен и сюжеты **Корреляции.** Для создания сюжета:

1. Для **типа**, выберите **Корреляция**.
1. Выберите **Выполнить**.
1. Rattle рекомендует использовать не более 40 переменных. Нажмите кнопку **Yes** (Да), чтобы просмотреть график.

Есть некоторые интересные корреляции, которые приходят: _технология_ сильно коррелирует с _HP_ и _лабораторий,_ например. Это также сильно коррелирует с _650,_ потому что код области доноров набора данных составляет 650.

Числовые значения корреляции между словами доступны в окне **Исследования.** Интересно отметить, например, что _технология_ негативно коррелирует с _вашими_ и _деньгами._

В Rattle также можно преобразовать набор данных для решения некоторых стандартных задач. Например, он может перемасштабировать объекты, вводить недостающие значения, обрабатывать выбросы и удалять переменные или наблюдения, в которых отсутствуют данные. Rattle также может определить правила ассоциации между наблюдениями и переменными. Эти вкладки не охвачены в этом вступительном пошаговом уходе.

Rattle также может запустить кластерный анализ. Давайте исключим некоторые признаки, чтобы облегчить чтение выходных данных. На вкладке **Data** выберите **Игнорировать** рядом с каждой из переменных, за исключением этих 10 элементов:

* word_freq_hp;
* word_freq_technology;
* word_freq_george;
* word_freq_remove;
* word_freq_your;
* word_freq_dollar;
* word_freq_money;
* capital_run_length_longest;
* word_freq_business;
* spam

Вернуться к вкладке **кластера.** Выберите **KMeans,** а затем установите **количество кластеров** до **4.** Выберите **Выполнить**. Результаты появятся в окне вывода. Один кластер имеет высокую частоту _Джордж_ и _л.с.,_ и, вероятно, законной деловой электронной почты.

Для создания базовой модели машинного обучения дерева решений:

1. Выберите вкладку **«Модель»,**
1. Для **типа**, выберите **Дерево**.
1. Нажмите кнопку **Execute** (Выполнить), чтобы отобразить дерево в виде текста в окне вывода.
1. Нажмите кнопку **Draw** (Рисовать), чтобы просмотреть графическое представление. Дерево принятия решений похоже на дерево, полученное ранее с помощью rpart.

Полезной особенностью Rattle является его способность запустить несколько методов машинного обучения и быстро оценить их. Вот шаги:

1. Для **типа**, выберите **все**.
1. Выберите **Выполнить**.
1. Когда Rattle завершает работу, вы можете выбрать любое значение **типа,** например **SVM,** и просмотреть результаты.
1. Вы также можете сравнить производительность моделей на наборе проверки с помощью вкладки **Оценка.** Например, в выборе **Матрицы ошибок** отображается матрица путаницы, общая ошибка и усреднение ошибки класса для каждой модели в наборе проверки. Вы также можете построить кривые ROC, провести анализ чувствительности и сделать другие типы оценок моделей.

Когда вы закончите построение моделей, выберите вкладку **журнала** для просмотра R-кода, который был запущен Rattle во время сеанса. Чтобы сохранить его, нажмите кнопку **Export** (Экспорт).

> [!NOTE]
> Текущий релиз Rattle содержит ошибку. Чтобы изменить скрипт или использовать его, чтобы повторить **#** ваши шаги позже, вы должны вставить символ перед *Экспорт этот журнал ...*

### <a name="postgresql-and-squirrel-sql"></a>ПостгреСЗЛ и СКуиррел СЗЛ

На ВМ для обработки и анализа данных установлен компонент PostgreSQL. PostgreSQL — продвинутая реляционная база данных с открытым исходным кодом. В этом разделе показан, как загрузить набор данных спамбазы в PostgreS'L, а затем задать его запрос.

Прежде чем загрузить данные необходимо разрешить проверку подлинности пароля от локального хоста. В окне командной строки выполните следующую команду.

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

В нижней части файла конфигурации расположены несколько строк, описывающие разрешенные подключения.

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Измените линию **локальных подключений IPv4,** чтобы использовать **md5** вместо **ident,** чтобы мы могли войти в систему с помощью имени пользователя и пароля:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Затем перезапустите сервис PostgreS'L:

```Bash
sudo systemctl restart postgresql
```

Чтобы запустить *psql* (интерактивный терминал для PostgreS'L) в качестве встроенного пользователя postgres, запустите эту команду:

```Bash
sudo -u postgres psql
```

Создайте новую учетную запись пользователя, используя имя пользователя учетной записи Linux, которую вы использовали для входа в систему. Создайте пароль:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Войти в psql:

```Bash
psql
```

Импортируйте данные в новую базу данных:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Теперь давайте рассмотрим данные и запустим некоторые запросы, используя S'куиррел, графический инструмент, который можно использовать для взаимодействия с базами данных через драйвер JDBC.

Для начала, в меню **приложений,** откройте S'Куиррел СЗЛ. Чтобы настроить драйвер, сделайте следующее:

1. Выберите**драйверы просмотра** **Windows** > .
1. Нажмите правой кнопкой **мыши PostgreS'L** и выберите **Драйвер модифицировать**.
1. Выберите **дополнительный путь** > класса**Добавить.**
1. Для **названия файлов**, введите **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Выберите **Открыть**.
1. Выберите **Драйверы списка**. Для **названия класса**, выберите **org.postgresql.Driver**, а затем выберите **OK**.

Чтобы установить подключение к локальному серверу:

1. Выберите прогон **Windows** > **View Aliases.**
1. Выберите **+** кнопку для создания нового псевдонима. Для нового имени псевдонима **введите базу данных Спам**. 
1. Для **драйвера**, выберите **PostgreS'L**.
1. В качестве URL-адреса укажите **jdbc:postgresql://localhost/spam**.
1. Введите имя пользователя и пароль.
1. Щелкните **ОК**.
1. Чтобы открыть окно **Подключение**, дважды щелкните псевдоним **База данных нежелательной почты**.
1. Выберите **Подключите**.

Чтобы выполнить запросы:

1. Выберите вкладку **SQL** .
1. В поле запроса в верхней части вкладки **S'L** введите базовый запрос, например. `SELECT * from data;`
1. Нажмите на Ctrl-Enter, чтобы запустить запрос. По умолчанию, S'Куиррел си-L возвращает первые 100 строк из вашего запроса.

Есть еще много запросов, которые можно запустить для изучения этих данных. Например, как отличается частота упоминания слова *make* в нежелательной и обычной почте?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Или, каковы характеристики электронной почты, которые часто содержат *3d?*

```SQL
SELECT * from data order by word_freq_3d desc;
```

Большинство писем, которые имеют *3d* высокий возникновение 3d-видимому, спам. Эта информация может быть полезна для создания прогностической модели для классификации электронных писем.

Если вы хотите сделать машинное обучение с помощью данных, хранящихся в базе данных PostgreS'L, рассмотрите возможность использования [MADlib.](https://madlib.incubator.apache.org/)

### <a name="sql-data-warehouse"></a>Хранилище данных SQL

Хранилище данных Azure S'L — это облачная база данных, которая может обрабатывать огромные объемы данных, как реляционных, так и несвязанных. Дополнительные сведения см. в статье [Что такое хранилище данных SQL Azure?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Чтобы подключиться к хранилищу данных и создать таблицу, выполните следующую команду из командной строки:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

В запросе sqlcmd запустите эту команду:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Копировать данные с помощью bcp:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> Загруженный файл содержит окончание строки в стиле Windows. Инструмент bcp ожидает окончания линии в стиле Unix. Используйте флаг -r, чтобы сказать bcp.

Затем, запрос с помощью sqlcmd:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Вы также можете задать запрос с помощью S'Куиррел СЗЛ. Выполняй те же шаги, подобные PostgreS'L, с помощью драйвера S'L Server JDBC. Драйвер JDBC находится в папке /usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Дальнейшие действия

Для обзора статей, которые проходят через задачи, которые включают в себя процесс науки о данных в Azure, [см.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)

Для описания сквозных пошаг-инструкций, демонстрирующие шаги в процессе исследования данных [Team Data Science Process walkthroughs](../team-data-science-process/walkthroughs.md)team Data для конкретных сценариев, см. В пошаговых руководствах также показано, как объединить облачные и локальные инструменты и службы в единый рабочий процесс или конвейер, чтобы создать интеллектуальное приложение.
