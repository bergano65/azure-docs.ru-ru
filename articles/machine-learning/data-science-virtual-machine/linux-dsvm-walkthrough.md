---
title: Изучение Linux
titleSuffix: Azure Data Science Virtual Machine
description: Узнайте, как выполнить несколько общих задач обработки и анализа данных с помощью виртуальной машины Linux для обработки и анализа данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: f9d4b933bc9c6e11dde8168d9797a1b6196e6f47
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170686"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Обработка и анализ данных с помощью виртуальной машины Linux для обработки и анализа данных в Azure

В этом пошаговом руководстве показано, как выполнить несколько общих задач обработки и анализа данных с помощью виртуальной машины Linux для обработки и анализа данных (DSVM). DSVM Linux — это образ виртуальной машины, доступный в Azure и предустановленный с набором средств, которые обычно используются для анализа данных и машинного обучения. Ключевые компоненты программного обеспечения подготавливаются [к работе при подготовке виртуальной машины Linux для обработки и анализа данных](linux-dsvm-intro.md). Образ DSVM упрощает процесс обработки и анализа данных за считаные минуты без необходимости установки и настройки каждого средства по отдельности. Вы можете легко масштабировать DSVM, если это необходимо, и вы можете приостанавливаться, когда она не используется. Ресурс DSVM является эластичным и экономически эффективным.

Задачи обработки и анализа данных, продемонстрированные в этом пошаговом руководстве, выполняются в соответствии с инструкциями, описанными в разделе [что такое обработка и анализ данных группой?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Процесс обработки и анализа данных группы — это систематический подход к обработке и анализу данных, который помогает группам специалистов по обработке и анализу данных эффективно сотрудничать над жизненным циклом создания интеллектуальных приложений. Кроме того, этот процесс может служить итеративной платформой для обработки и анализа данных, которую можно использовать самостоятельно.

В этом пошаговом руководстве мы анализируем набор данных [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Spambase — это набор сообщений электронной почты, которые отмечены как спам или HAM (а не спам). Spambase также содержит некоторую статистику по содержимому сообщений электронной почты. Далее в этом пошаговом руководстве мы поговорим о статистике.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем использовать DSVM для Linux, необходимо выполнить следующие предварительные требования.

* **Подписка Azure**. Чтобы получить подписку Azure, см. статью [Создание бесплатной учетной записи Azure на сегодняшний день](https://azure.microsoft.com/free/).
* [**Виртуальная машина Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm)для обработки и анализа данных. Сведения о подготовке виртуальной машины см. [в статье Подготовка виртуальной машины Linux для обработки и анализа данных](linux-dsvm-intro.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) , установленный на компьютере с открытым сеансом Xfce. Дополнительные сведения см. [в статье Установка и Настройка клиента X2Go](linux-dsvm-intro.md#x2go).
* Для работы с более гладкими прокруткой в веб-браузере Firefox DSVM переключите `gfx.xrender.enabled` флаг в. `about:config` [Узнайте больше](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Также рассмотрите `mousewheel.enable_pixel_scrolling` возможность `False`установки значения. [Узнайте больше](https://support.mozilla.org/questions/981140).
* **Учетная запись машинное обучение Azure**. Если у вас ее еще нет, зарегистрируйте новую учетную запись на [домашней странице машинное обучение Azure](https://studio.azureml.net/). Вы можете испытать его бесплатно, чтобы помочь вам приступить к работе.

## <a name="download-the-spambase-dataset"></a>Скачивание набора данных spambase

Набор данных [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) — это относительно небольшой набор данных, содержащий примеры 4 601. Набор данных — это удобный размер для демонстрации некоторых ключевых функций DSVM, поскольку он позволяет хранить требования к ресурсам неограниченное количество.

> [!NOTE]
> Это пошаговое руководство было создано с помощью DSVM Linux версии 2 (CentOS Edition). Для выполнения процедур, продемонстрированных в этом пошаговом руководстве, можно использовать DSVM этот размер.

Если требуется больше места для хранения, можно создать дополнительные диски и подключить их к DSVM. Диски используют постоянное хранилище Azure, поэтому их данные сохраняются даже в том случае, если сервер повторно подготавливается из-за изменения размера или завершения работы. Чтобы добавить диск и подключить его к DSVM, выполните действия, описанные в статье [Добавление диска к виртуальной машине Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Шаги по добавлению диска применяют Azure CLI, который уже установлен на DSVM. Шаги можно выполнять только из самого DSVM. Еще один вариант увеличения объема хранилища — использование службы [файлов Azure](../../storage/files/storage-how-to-use-files-linux.md).

Чтобы загрузить данные, откройте окно терминала и выполните следующую команду:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Скачанный файл не содержит строку заголовка. Давайте создадим другой файл, у которого есть заголовок. Для этого выполните следующую команду:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Затем объедините два файла:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Набор данных имеет несколько типов статистики для каждого сообщения электронной почты:

* Такие столбцы **,\_как\_Word FREQ * Word***, указывают процент слов в сообщении электронной почты, соответствующих *слову*. Например, если **Word\_FREQ\_** имеет значение **1**, то были *внесены*1% всех слов в письме.
* Такие столбцы **,\_как\_char FREQ * char***, указывают процент всех символов в письме, которые являются *char*.
* **capital\_run\_length\_longest** — самая длинная последовательность прописных букв.
* **capital\_run\_length\_average** — средняя длина всех последовательностей прописных букв.
* **capital\_run\_length\_total** — общая длина всех последовательностей прописных букв.
* **spam** указывает, были ли сообщения признаны нежелательной почтой (1 — нежелательная почта, 0 —обычная почта).

## <a name="explore-the-dataset-by-using-r-open"></a>Просмотр набора данных с помощью Open R

Давайте рассмотрим данные и выберем некоторые основные машинные курсы с помощью языка R. DSVM поставляется с предварительно установленным [Microsoft R Open](https://mran.revolutionanalytics.com/open/) . Многопоточные математические библиотеки в предварительно установленной версии R обеспечивают лучшую производительность по сравнению с однопотоковыми версиями. R Open также предоставляет воспроизводимость через моментальный снимок репозитория пакетов CRAN.

Чтобы получить копии примеров кода, используемых в этом пошаговом руководстве, используйте Git для клонирования репозитория Azure-Machine-Learning-Data-наука. Git предварительно установлен на DSVM. В командной строке Git выполните:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Откройте окно терминала и запустите новый сеанс R в интерактивной консоли R. Вы также можете использовать RStudio, предварительно установленный на DSVM.

Чтобы импортировать данные и настроить среду, выполните следующие действия.

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Чтобы просмотреть сводные статистические данные по каждому столбцу, выполните следующую команду:

    summary(data)

Чтобы получить другое представление данных, выполните следующую команду:

    str(data)

В этом представлении отображается тип каждой переменной и несколько первых значений в наборе данных.

Столбец **spam** распознан как целое число, но это на самом деле категориальная переменная (или факторная). Чтобы задать тип данных в этом столбце, выполните следующую команду:

    data$spam <- as.factor(data$spam)

Для проведения какого-либо исследовательского анализа используйте пакет [ggplot2](https://ggplot2.tidyverse.org/) — популярную библиотеку диаграмм для R, которая предустановлена на DSVM. Исходя из сводных данных, показанных выше, сводная статистика по частоте символа восклицательного знака. Давайте выберем эти частоты, выполнив следующие команды:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Так как отметка от нуля отклоняется, давайте исключим ее:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Существует нетривиальный плотности выше 1, который выглядит интересным. Рассмотрим только эти данные:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Затем разделите его по спаму и HAM:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Эти примеры помогут сделать похожие диаграммы и исследовать данные в других столбцах.

## <a name="train-and-test-a-machine-learning-model"></a>Обучение и тестирование модели машинного обучения

Давайте обучить несколько моделей машинного обучения, чтобы классифицировать сообщения электронной почты в наборе данных, которые содержат нежелательную почту или HAM. В этом разделе мы обучить модель дерева принятия решений и модель случайного леса. Затем мы протестируем точность прогнозов.

> [!NOTE]
> Пакет *rpart* (рекурсивное секционирование и деревья регрессии), используемый в следующем коде, уже установлен на DSVM.

Сначала давайте разделите набор данных на обучающие наборы и наборы тестов:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Затем создайте дерево принятия решений для классификации сообщений электронной почты:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Ниже приведен результат.

![Схема созданного дерева принятия решений](./media/linux-dsvm-walkthrough/decision-tree.png)

Чтобы определить, насколько хорошо эта модель работает с обучающим набором данных, используйте следующий код:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Чтобы определить, насколько хорошо она работает с тестовым набором данных:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Теперь давайте испробуем модель случайного леса. Случайные леса обучить множество деревьев принятия решений и выводят класс, который является режимом классификаций из всех отдельных деревьев принятия решений. Они предоставляют более мощный подход к машинному обучению, поскольку они устраняют тенденции модели дерева принятия решений для переобучению набора данных для обучения.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Развертывание модели в Машинное обучение Azure Studio

[Студия машинного обучения Azure](https://studio.azureml.net/) — это облачная служба, упрощающая сборку и развертывание моделей прогнозной аналитики. Удобная функция Машинное обучение Azure Studio — это возможность публикации любой функции R в качестве веб-службы. Пакет Машинное обучение Azure Studio R делает развертывание простым, прямо из сеанса R на DSVM.

Чтобы развернуть код дерева принятия решений из предыдущего раздела, войдите в Машинное обучение Azure Studio. Для этого необходимо указать идентификатор рабочей области и маркер авторизации. Чтобы найти эти значения и инициализировать переменные Машинное обучение Azure с ними, выполните следующие действия:

1. В меню слева выберите **Параметры**. Запишите значение для **идентификатора рабочей области**.

   ![ИДЕНТИФИКАТОР рабочей области Машинное обучение Azure Studio](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Перейдите на вкладку **токены авторизации** . Запишите значение **основного маркера авторизации**.

   ![Основной маркер авторизации Машинное обучение Azure Studio](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Загрузите пакет **AzureML** , а затем задайте значения переменных с помощью токена и идентификатора рабочей области в сеансе R в DSVM:

        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Давайте упростим модель, чтобы облегчить реализацию этой демонстрации. Выберите три переменные в дереве решений, расположенном ближе к корню, и создайте новое дерево, используя только следующие три переменные:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. Нам нужна функция прогнозирования, которая принимает в качестве входных данных признаки и возвращает прогнозируемые значения.

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }


1. Опубликуйте функцию **предиктспам** в AzureML с помощью функции **публишвебсервице** :

        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Эта функция принимает функцию **предиктспам** , создает веб-службу с именем **спамвебсервице** , которая имеет определенные входные и выходные данные, а затем возвращает сведения о новой конечной точке.

    Используйте эту команду для просмотра сведений о последней опубликованной веб-службе, включая ее конечную точку API и ключи доступа:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Чтобы испытать эту функцию на первых 10 строках тестового набора данных, выполните следующую команду:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Учебники и пошаговые руководства по глубокому обучению

В дополнение к примерам на основе платформы также предоставляется набор подробных пошаговых руководств. Эти пошаговые руководства помогут вам быстро приступить к разработке приложений для глубокого обучения в таких областях, как распознавание изображений, текста и языка.

- [Запуск нейронных сетей на разных платформах](https://github.com/ilkarman/DeepLearningFrameworks). Исчерпывающее пошаговое руководство, в котором показано, как перенести код из одной платформы в другую. В нем также показано, как сравнивать производительность модели и среды выполнения между различными платформами. 

- [Руководство по созданию комплексного решения для обнаружения продуктов в изображениях](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Обнаружение изображений — это метод, который позволяет находить и классифицировать объекты на изображениях. Эта технология может привести к огромным преимуществам во многих реальных бизнес-доменах. Например, розничные продавцы могут использовать этот метод для определения того, какой продукт клиент взял с полки. Эти сведения, в свою очередь, помогают управлять запасами продукции. 

- [Глубокое обучение для аудио](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): В этом учебнике показано, как обучить модель глубокого обучения для обнаружения событий аудио в [наборе данных городской звук](https://urbansounddataset.weebly.com/). В этом учебнике приводятся общие сведения о работе с звуковыми данными.

- [Классификация текстовых документов](https://github.com/anargyri/lstm_han). В этом пошаговом руководстве показано, как создать и обучить две разные архитектуры нейронных сетей: Иерархическая сетевое внимание и длительная Краткосрочная память (LSTM). Эти нейронные сети выполняют классификацию текстовых документов с помощью API Keras для глубокого обучения. Keras — это интерфейс для трех самых популярных платформ машинного обучения: Microsoft Cognitive Toolkit, TensorFlow и Theano.

## <a name="other-tools"></a>Другие средства

В остальных разделах показано, как использовать некоторые из средств, установленных в DSVM Linux. Мы обсудим следующие средства:

* XGBoost;
* Python
* JupyterHub
* Rattle;
* PostgreSQL и SQuirreL SQL
* хранилище данных SQL Server.

### <a name="xgboost"></a>XGBoost;

[XGBoost](https://xgboost.readthedocs.org/en/latest/) обеспечивает быструю и точную реализацию повышенного дерева.

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

XGBoost также может вызывать из Python или из командной строки.

### <a name="python"></a>Python

Для разработки на Python на DSVM установлены дистрибутивы Anaconda Python 3,5 и 2,7.

> [!NOTE]
> Распространение Anaconda включает [Conda](https://conda.pydata.org/docs/index.html). Conda можно использовать для создания пользовательских сред Python с разными версиями или пакетами, установленными в них.

Давайте проверим часть набора данных spambase и классификация сообщений электронной почты с помощью виртуальных машин поддержки в Scikit-учиться:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Для создания прогнозов используем следующую команду:

    clf.predict(X.ix[0:20, :])

Чтобы продемонстрировать, как опубликовать конечную точку Машинное обучение Azure, давайте создадим более простую модель. Мы будем использовать три переменные, которые использовались при публикации модели R ранее:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Чтобы опубликовать модель в Машинное обучение Azure, выполните следующие действия.

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


> [!NOTE]
> Этот параметр доступен только для Python 2,7. Он еще не поддерживается в Python 3,5. Для запуска используйте **/Anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

Распространение Anaconda в DSVM поставляется с Jupyter Notebook, многоплатформенной средой для совместного использования кода Python, R или Julia и анализа. Доступ к Jupyter Notebook осуществляется через JupyterHub. Войдите в систему, используя имя пользователя и пароль локального Linux по адресу\<HTTPS://DSVM DNS-имя или\>IP-адрес: 8000/. Все файлы конфигурации для JupyterHub находятся в/ЕТК/жупитерхуб.

> [!NOTE]
> Чтобы использовать диспетчер пакетов Python (с помощью `pip` команды) из Jupyter Notebook в текущем ядре, используйте эту команду в ячейке кода:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Чтобы использовать установщик Conda (с помощью `conda` команды) из Jupyter Notebook в текущем ядре, используйте эту команду в ячейке кода:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

В DSVM уже установлены несколько примеров записных книжек:

* Примеры записных книжек Python:
  * [Интротожупитерписон. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [ирисклассифиерпимлвебсервице](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Пример записной книжки R:
  * [интротуториалинр](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Язык Julia также доступен из командной строки в DSVM Linux.

### <a name="rattle"></a>Rattle;

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*налитикал *t*ул *t*o *L*— получение *E*асили) — это графическое средство R для интеллектуального анализа данных. Rattle имеет интуитивно понятный интерфейс, который позволяет легко загружать, изучать и преобразовывать данные, а также создавать и оценивать модели. [Rattle Графический интерфейс интеллектуального анализа данных](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) для R предоставляет пошаговое руководство, демонстрирующее функции Rattle.

Установите и запустите Rattle, выполнив следующие команды:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Вам не нужно устанавливать Rattle на DSVM. Однако при открытии Rattle может быть предложено установить дополнительные пакеты.

В Rattle используется интерфейс на основе вкладок. Большинство вкладок соответствуют шагам в [процессе обработки и анализа данных группы](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), таких как загрузка данных или просмотр данных. Процесс обработки и анализа данных выполняется слева направо, перемещаясь по вкладкам. Последняя вкладка содержит журнал команд R, которые были запущены с помощью Rattle.

Чтобы загрузить и настроить набор данных:

1. Чтобы загрузить файл, перейдите на вкладку **данные** .
1. Выберите селектор рядом с **именем файла**, а затем выберите **спамбасехеадерс. Data**.
1. Чтобы загрузить файл, Выберите **выполнить**. Вы должны увидеть сводку по каждому столбцу, включая его идентифицированный тип данных. является ли он входным, целевым или другим типом переменной; и количество уникальных значений.
1. Rattle правильно распознал тип данных столбца **spam** как целевой объект. Выберите столбец **Нежелательная почта** и задайте для **целевого типа данных** значение **категорик**.

Чтобы изучить данные:

1. Выберите вкладку **Explore** (Изучение).
1. Чтобы просмотреть сведения о типах переменных и некоторые сводные статистические данные, выберите **сводный** > **Запуск**.
1. Чтобы просмотреть другие типы статистики по каждой переменной, выберите другие параметры, например **Описание** или **Основные сведения**.

Можно также использовать вкладку **Обзор** для создания аналитических диаграмм. Чтобы построить гистограмму данных:

1. Установите флажок **Distributions**(Дистрибутивы).
1. Для **word_freq_remove** и **word_freq_you**выберите **гистограмма**.
1. Нажмите кнопку **Выполнить**. В одном окне графа отобразятся обе диаграммы плотности, где ясно, что в сообщениях электронной почты часто встречаются такие _же слова,_ как и для _удаления_.

Также интересны графики **корреляции** . Чтобы создать график, выполните следующие действия.

1. В качестве **типа**выберите **корреляция**.
1. Нажмите кнопку **Выполнить**.
1. Rattle рекомендует использовать не более 40 переменных. Нажмите кнопку **Yes** (Да), чтобы просмотреть график.

Есть несколько интересных корреляций: _технология_ строго связана с _HP_ и _лабораториями_, например. Он также строго связан с _650_ , так как код города для спонсоров набора данных — 650.

Числовые значения для корреляции между словами доступны в окне **Обзор** . Интересно отметить, например, что _технология_ отрицательно соотносится с _вашими_ и _деньгами_.

В Rattle также можно преобразовать набор данных для решения некоторых стандартных задач. Например, он может масштабировать функции, аппроксимация отсутствующие значения, выполнять выбросы и удалять переменные или наблюдения с отсутствующими данными. Rattle также может выявляет правила взаимосвязей между наблюдениями и переменными. Эти вкладки не рассматриваются в этом вводном руководстве.

Rattle также может выполнять анализ кластера. Давайте исключим некоторые признаки, чтобы облегчить чтение выходных данных. На вкладке **данные** выберите **пропустить** рядом с каждой из переменных, за исключением 10 элементов:

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

Вернитесь на вкладку **Cluster (кластер** ). Выберите **кмеанс**, а затем задайте для **параметра число кластеров** значение **4**. Нажмите кнопку **Выполнить**. Результаты появятся в окне вывода. Один кластер имеет высокую частоту от _Георгия_ до _HP_и, вероятно, является законным бизнес-сообщением.

Чтобы создать модель машинного обучения для базового дерева принятия решений, выполните следующие действия.

1. Перейдите на вкладку **Model** (Модель).
1. Для **типа**выберите **дерево**.
1. Нажмите кнопку **Execute** (Выполнить), чтобы отобразить дерево в виде текста в окне вывода.
1. Нажмите кнопку **Draw** (Рисовать), чтобы просмотреть графическое представление. Дерево принятия решений похоже на дерево, полученное ранее с помощью rpart.

Полезным компонентом Rattle является возможность выполнять несколько методов машинного обучения и быстро оценивать их. Ниже приведены действия.

1. В качестве **типа**выберите **все**.
1. Нажмите кнопку **Выполнить**.
1. Когда Rattle закончит работу, можно выбрать любое значение **типа** , например **SVM**, и просмотреть результаты.
1. Можно также сравнить производительность моделей в наборе проверки с помощью вкладки « **Оценка** ». Например, если выбрать **матрицу ошибок** , отобразится матрица неточностей, общий показатель ошибок и ошибка усредненного класса для каждой модели в наборе для проверки. Можно также построить график ROC кривых, запустить анализ чувствительности и выполнить другие типы оценки модели.

Завершив создание моделей, перейдите на вкладку **log (журнал** ), чтобы просмотреть код R, который был запущен с помощью Rattle во время сеанса. Чтобы сохранить его, нажмите кнопку **Export** (Экспорт).

> [!NOTE]
> В текущем выпуске Rattle содержится ошибка. Чтобы изменить сценарий или использовать его для повторения шагов позже, необходимо вставить **#** символ перед *экспортом этого журнала...* в тексте журнала.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL и SQuirreL SQL

На ВМ для обработки и анализа данных установлен компонент PostgreSQL. PostgreSQL — продвинутая реляционная база данных с открытым исходным кодом. В этом разделе показано, как загрузить набор данных spambase в PostgreSQL, а затем запросить его.

Прежде чем можно будет загрузить данные, необходимо разрешить проверку подлинности с помощью пароля с локального узла. В окне командной строки выполните следующую команду.

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

В нижней части файла конфигурации расположены несколько строк, описывающие разрешенные подключения.

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Измените строку **локальных подключений IPv4** , чтобы использовать **MD5** вместо **Ident**, поэтому мы можем войти с помощью имени пользователя и пароля:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Затем перезапустите службу PostgreSQL:

    sudo systemctl restart postgresql

Чтобы запустить *psql* (интерактивный терминал для PostgreSQL) в качестве встроенного пользователя postgres, выполните следующую команду:

    sudo -u postgres psql

Создайте новую учетную запись пользователя, используя имя пользователя учетной записи Linux, которую вы использовали для входа. Создайте пароль:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Войдите в PSQL:

    psql

Импорт данных в новую базу данных:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Теперь рассмотрим данные и выполните некоторые запросы с помощью SQuirreL SQL — графического средства, которое можно использовать для взаимодействия с базами данных через драйвер JDBC.

Чтобы начать работу, в меню **приложения** откройте SQuirreL SQL. Чтобы настроить драйвер, сделайте следующее:

1. Выберите **Windows** > **Просмотреть драйверы**.
1. Щелкните правой кнопкой мыши **PostgreSQL** и выберите пункт **изменить драйвер**.
1.  > Выберите **дополнительный путь к классу** **Добавить**.
1. В качестве **имени файла**введите **/usr/share/Java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar**.
1. Щелкните **Open**(Открыть).
1. Выберите **список драйверов**. В качестве **имени класса**выберите **org. PostgreSQL. Driver**и нажмите кнопку **ОК**.

Чтобы установить подключение к локальному серверу:

1. Выберите > **псевдонимы представлений Windows.**
1. **+** Нажмите кнопку, чтобы создать новый псевдоним. В поле имя нового псевдонима введите **база данных нежелательной почты**. 
1. В качестве **драйвера**выберите **PostgreSQL**.
1. В качестве URL-адреса укажите **jdbc:postgresql://localhost/spam**.
1. Введите имя пользователя и пароль.
1. Нажмите кнопку **ОК**.
1. Чтобы открыть окно **Подключение**, дважды щелкните псевдоним **База данных нежелательной почты**.
1. Нажмите кнопку **Подключиться**.

Чтобы выполнить запросы:

1. Выберите вкладку **SQL** .
1. В поле запроса в верхней части вкладки **SQL** введите базовый запрос, например `SELECT * from data;`.
1. Нажмите клавиши CTRL + ВВОД, чтобы выполнить запрос. По умолчанию SQuirreL SQL возвращает первые 100 строк из запроса.

Для изучения этих данных можно выполнить гораздо больше запросов. Например, как отличается частота упоминания слова *make* в нежелательной и обычной почте?

    SELECT avg(word_freq_make), spam from data group by spam;

Или каковы характеристики сообщения электронной почты, которое часто содержит *3D*?

    SELECT * from data order by word_freq_3d desc;

Большинство сообщений электронной почты с большим объемом *трехмерных* объектов — Нежелательная почта. Эти сведения могут оказаться полезными при создании прогнозной модели для классификации сообщений электронной почты.

Если вы хотите выполнять машинное обучение, используя данные, хранящиеся в базе данных PostgreSQL, рассмотрите возможность использования [мадлиб](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>Хранилище данных SQL

Хранилище данных SQL Azure — это облачная база данных с горизонтальным масштабированием, которая может обрабатывать большие объемы данных, как реляционные, так и нереляционные. Дополнительные сведения см. в статье [Что такое хранилище данных SQL Azure?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Чтобы подключиться к хранилищу данных и создать таблицу, выполните следующую команду из командной строки:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

В командной строке sqlcmd выполните следующую команду:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Скопируйте данные с помощью программы bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Скачанный файл содержит символы конца строки в стиле Windows. Средство bcp ждет завершения строк в стиле UNIX. Используйте флаг-r, чтобы сообщить bcp.

Затем выполните запрос с помощью программы sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Можно также выполнить запрос с помощью SQuirreL SQL. Выполните действия, аналогичные PostgreSQL, с помощью драйвера JDBC SQL Server. Драйвер JDBC находится в папке/usr/share/Java/jdbcdrivers/sqljdbc42.jar

## <a name="next-steps"></a>Следующие шаги

Обзор статей, посвященных задачам, составляющим процесс обработки и анализа данных в Azure, см. в разделе [процесс обработки и анализа данных группы](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Описание комплексных пошаговых руководств, демонстрирующих шаги процесса обработки и анализа данных группы в конкретных сценариях, см. в разделе [Пошаговые руководства по процессу обработки и анализа данных](../team-data-science-process/walkthroughs.md). В пошаговых руководствах также показано, как объединить облачные и локальные инструменты и службы в единый рабочий процесс или конвейер, чтобы создать интеллектуальное приложение.
