---
title: Руководство. Создание и запуск записной книжки Jupyter в Azure
description: Как создать и запустить записную книжку Jupyter в службе "Записные книжки Azure" для демонстрации процесса линейной регрессии при обработке и анализе данных.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: dcee7df0b5da53bd7014a26f1f09695fe874833d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849667"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Руководство. Создание и запуск записной книжки Jupyter с помощью Python

В этом руководстве приводятся пошаговые инструкции по созданию полной записной книжки Jupyter с помощью службы "Записные книжки Azure" для демонстрации простой линейной регрессии. Во время работы с этим руководством вы ознакомитесь с пользовательским интерфейсом записных книжек Jupyter, который включает в себя создание различных ячеек, выполнение ячеек и представление записной книжки в виде слайд-шоу.

Готовую записную книжку можно найти на странице с [примерами записных книжек Azure на сайте GitHub](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Однако это руководство начинается с нового проекта и пустой записной книжки, позволяя создать ее на практике шаг за шагом.

## <a name="create-the-project"></a>Создание проекта

1. Перейдите в службу [Записные книжки Azure](https://notebooks.azure.com) и выполните вход. (Дополнительные сведения см. в кратком руководстве по [входу в Записные книжки Azure](quickstart-sign-in-azure-notebooks.md).)

1. Вверху на общедоступной странице профиля щелкните **Мои проекты**:

    ![Ссылка "Мои проекты" в верхней части окна браузера](media/quickstarts/my-projects-link.png)

1. На странице **Мои проекты** выберите **+ Новый проект** (сочетание клавиш: n). Если окно браузера сужено, кнопка может отображаться как **+**:

    ![Команда "Новый проект" на странице "Мои проекты"](media/quickstarts/new-project-command.png)

1. В появившемся всплывающем окне **Создание проекта** введите или укажите приведенные ниже сведения, а затем нажмите кнопку **Создать**:

    - **Project name** (Имя проекта): Linear Regression Example - Cricket Chirps (Пример линейной регрессии — стрекотание сверчка).
    - **Project ID** (Идентификатор проекта): linear-regression-example.
    - **Public project** (Общедоступный проект): (флажок снят).
    - **Create a README.md** (Создать файл README.md): (флажок снят).

1. Через несколько секунд в службе "Записные книжки Azure" откроется новый проект.

## <a name="create-the-data-file"></a>Создание файла данных

Модель линейной регрессии, которую вы создадите в записной книжке, извлекает данные из содержащегося в проекте файла с именем *cricket_chirps.csv*. Этот файл можно создать либо путем копирования со страницы [примеров записных книжек Azure на сайте GitHub] (https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps), либо путем непосредственного ввода данных. В следующих разделах описаны оба подхода.

### <a name="upload-the-data-file"></a>Отправка файла данных

1. На панели мониторинга проекта в службе "Записные книжки Azure" выберите **Upload** > **From URL** (Отправить > С URL-адреса).
1. Во всплывающем окне введите указанный ниже URL-адрес в поле **File URL** (URL-адрес файла) и *cricket_chirps.csv* в поле **File Name** (Имя файла), а затем щелкните **Done** (Готово).

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Теперь в списке файлов проекта должен появиться файл *cricket_chirps.csv*:

    ![Только что созданный CSV-файл, отображаемый в списке файлов проекта](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Создание файла с нуля

1. На панели мониторинга проекта в службе "Записные книжки Azure" выберите **+ New** > **Blank File** (+ Создать > Пустой файл).
1. В списке файлов проекта появится поле. Введите *cricket_chirps.csv* и нажмите клавишу ВВОД.
1. Щелкните правой кнопкой мыши файл *cricket_chirps.csv* и выберите пункт **Edit File** (Изменить файл).
1. В открывшемся редакторе введите следующие данные:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Выберите **Save File** (Сохранить файл), чтобы сохранить файл и вернуться на панель мониторинга проекта.

## <a name="install-project-level-packages"></a>Установка пакетов на уровне проекта

В записной книжке всегда можно использовать команды, например `!pip install`, в ячейке кода для установки необходимых пакетов. Однако такие команды выполняются каждый раз при выполнении ячеек кода записной книжки, что может занимать много времени. Вместо этого можно установить пакеты на уровне проекта с помощью файла `requirements.txt`.

1. С помощью процесса, описанного в разделе [Создание файла с нуля](#create-a-file-from-scratch), создайте файл с именем `requirements.txt` и следующим содержимым:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Также при желании можно отправить файл `requirements.txt` с локального компьютера, как описано в разделе [Отправка файла данных](#upload-the-data-file).

1. На панели мониторинга проекта выберите **Project Settings** (Параметры проекта).
1. В появившемся всплывающем окне выберите вкладку **Environment** (Среда), а затем выберите **+Add** (+ Добавить).
1. В первом раскрывающемся списке (операция) в разделе **Environment Setup Steps** (Шаги по настройке среды) выберите файл **Requirements.txt**.
1. Во втором раскрывающемся списке (имя файла) выберите *requirements.txt* (файл, который вы создали).
1. В третьем раскрывающемся списке (версия Python) выберите **Python Version 3.6** (Python версии 3.6).
1. Щелкните **Сохранить**.

![Вкладка Environment (Среда) раздела Project Settings (Параметры проекта) с выбранным файлом requirements.txt](media/tutorial/tutorial-requirements-txt.png)

После выполнения этого этапа настройки любая записная книжка, запускаемая в проекте, будет работать в среде, в которой установлены указанные выше пакеты.

## <a name="create-and-run-a-notebook"></a>Создание и запуск записной книжки

Теперь, когда готов файл данных и настроена среда проекта, можно создать и открыть записную книжку.

1. На панели мониторинга проекта выберите **+ New** > **Записная книжка** (+ Создать > Записная книжка).
1. Во всплывающем окне в поле **Item Name** (Имя элемента) введите *Linear Regression Example - Cricket Chirps.ipynb*, выберите язык **Python 3.6**, а затем нажмите кнопку **New** (Создать).
1. Когда новая записная книжка появится в списке файлов, выберите ее, чтобы запустить записную книжку. Автоматически откроется новая вкладка браузера.
1. Так как файл *requirements.txt* указан в параметрах среды, отобразится сообщение "Waiting for your container to finish being prepared" (Ожидание завершения подготовки контейнера). Можно нажать кнопку **ОК**, чтобы закрыть сообщение и продолжить работу в записной книжке, но пока среда полностью не настроена, ячейки кода нельзя выполнить.
1. Записная книжка откроется в интерфейсе Jupyter с одной пустой ячейкой кода по умолчанию.

    [![Первоначальный вид новой записной книжки в службе "Записные книжки Azure"](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Обзор интерфейса записной книжки

Запустив записную книжку, можно добавить код и разметить ячейки, выполнить эти ячейки и управлять работой записной книжки. Однако сначала стоит уделить несколько минут знакомству с интерфейсом. Для просмотра полной документации выберите команду меню **Help** > **Notebook Help (Справка > Справка по записной книжке)**.

В верхней части окна вы увидите следующие элементы:

(A) Имя записной книжки, которое можно изменить, щелкнув его.
(B) Кнопки для перехода к содержащему записную книжку проекту и панели мониторинга проектов, после нажатия которых откроются новые вкладки в браузере.
(C) Меню с командами для работы с записной книжкой.
(D) Панель инструментов с сочетаниями клавиш для выполнения распространенных операций.
(E) Холст для редактирования, содержащий ячейки.
(F) Индикатор, показывающий, является ли записная книжка доверенной (значение по умолчанию — **Not Trusted** (Не является доверенной)).
(G) Ядро, используемое для запуска записной книжки, с индикатором активности.

[![Основные области пользовательского интерфейса Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

В Jupyter предоставляется встроенный обзор основных элементов пользовательского интерфейса. Чтобы начать обзор, выберите команду **Help** > **User Interface Tour** (Справка > Обзор пользовательского интерфейса) и поочередно выберите всплывающие окна.

Команды в меню разделяются на следующие группы:

| Меню | ОПИСАНИЕ |
| --- | --- |
| Файл | Команды для управления файлом записной книжки, включая команды создания и копирования записных книжек, предварительного просмотра для печати и скачивания записной книжки в различных форматах. |
| Редактирование | Типовые команды вырезания, копирования и вставки ячеек, поиска и замены значений, управления вложениями в ячейках и вставки изображений.  |
| Просмотр | Команды для управления видимостью различных частей пользовательского интерфейса Jupyter. |
| Вставить | Команды для вставки новой ячейки выше или ниже текущей. Эти команды часто используются при создании записной книжки. |
| Cell (Ячейка) | Различные команды **Run** (Выполнить) выполняют одну или несколько ячеек в различных сочетаниях. Команды **Cell Type** (Тип ячейки) изменяют тип ячейки на **Code** (Код), **Markdown** или **Raw NBConvert** (простой текст). Команды **Current Outputs** (Текущие выходные данные) и **All Outputs** (Все выходные данные) позволяют выбрать способ вывода данных после выполнения кода, а также содержат команду очистки всех выходных данных. |
| Kernel (Ядро) | Команды для управления способом выполнения кода в ядре, а также команда **Change kernel** (Изменить ядро) для изменения языка или версии Python, используемой для запуска записной книжки. |
| Данные | Команды для отправки и скачивания файлов из проекта или сеанса. См. статью о [работе с файлами данных проекта](work-with-project-data-files.md). |
| Мини-приложения | Команды для управления [мини-приложениями Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), которые предоставляют дополнительные возможности для визуализации, построения карт и графиков.|
| Справка | Команды, предоставляющие справочные сведения и документацию по интерфейсу Jupyter. |

Большинству команд на панели инструментов соответствуют эквивалентные команды меню. Единственным исключением является команда **Enter/Edit RISE Slideshow** (Открыть/изменить слайд-шоу RISE), которая описывается в статье об [общем доступе к записным книжкам и их представлении](present-jupyter-notebooks-slideshow.md).

Ряд этих команд используется по мере заполнения записной книжки в последующих разделах.

## <a name="create-a-markdown-cell"></a>Создание ячейки Markdown

1. Щелкните первую пустую ячейку, отображающуюся на холсте записной книжки. По умолчанию ячейка имеет тип **Code** (Код), который означает, что она предназначена для хранения исполняемого кода для выбранного ядра (Python, R или F#). Текущий тип отображается в раскрывающемся списке типов на панели инструментов:

    ![Раскрывающийся список типов ячеек на панели инструментов](media/tutorial/tutorial-cell-type-drop-down.png)

1. Измените тип ячейки на **Markdown** с помощью раскрывающегося списка на панели инструментов. Также это можно сделать, выбрав в меню **Cell** > **Cell Type** > **Markdown** (Ячейка > Тип ячейки > Markdown):

    ![Команда Cell Type (Тип ячейки) в меню](media/tutorial/tutorial-cell-type-menu.png)

1. Щелкните в ячейке, чтобы начать редактирование, а затем введите следующий код Markdown:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](http://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Чтобы преобразовать Markdown в код HTML для браузера, выберите команду **Run** (Выполнить) на панели инструментов или воспользуйтесь командой **Cell** > **Run Cells (Ячейка > Выполнить ячейки)**. Код Markdown для форматирования и ссылки теперь правильно отображаются в браузере.

1. При выполнении последней ячейки в записной книжке Jupyter автоматически создает новую ячейку под выполненной. Поместите дополнительный код Markdown в эту ячейку, повторив шаги из этого раздела со следующим кодом Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Чтобы изменить код Markdown еще раз, дважды щелкните в преобразованной ячейке. Чтобы вывести код HTML еще раз после внесения изменений, выполните ячейку.

## <a name="create-a-code-cell-with-commands"></a>Создание ячейки кода с командами

Как показано на примере предыдущей ячейки с кодом Markdown, команды можно включить непосредственно в записную книжку. С помощью команд можно установить пакеты, запустить curl или wget для получения данных или выполнить другие действия. Записные книжки Jupyter фактически запускаются на виртуальной машине Linux, поэтому для работы доступен полный набор команд Linux.

1. Введите приведенные ниже команды в ячейку кода, которая появилась после выполнения команды **Run** (Выполнить) в предыдущей ячейке Markdown. Если новая ячейка не отображается, создайте ее с помощью команды **Insert** > **Insert Cell Below** (Вставка > Вставить ячейку ниже) или кнопки **+** на панели инструментов.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Перед выполнением ячейки создайте новую ячейку с помощью кнопки **+** на панели инструментов, задайте для нее значение Markdown и введите следующее пояснение:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Выберите команду **Cell** > **Run All** (Ячейка > Выполнить все), чтобы выполнить все ячейки в записной книжке. Обратите внимание, что ячейки Markdown преобразуются в формат HTML, а команда выполняется в ядре, и наблюдайте за индикатором ядра, как описано в самом коде Markdown:

    ![Индикатор занятости ядра записной книжки](media/tutorial/tutorial-kernel-busy.png)

1. Выполнение всех команд `pip install` также занимает время, и поскольку вы уже установили эти пакеты в среде проекта (и они также включаются в записные книжки Azure по умолчанию), вы увидите много сообщений с текстом "Requirement already satisfied" (Требование уже удовлетворено). Все эти сообщения могут отвлекать внимание, поэтому выделите соответствующую ячейку (одиночным щелчком), а затем выберите **Cell** > **Cell Outputs** > **Toggle**  (Ячейка > Выходные данные ячейки > Переключить), чтобы скрыть выходные данные. Также можно воспользоваться командой **Clear** (Очистить) из этого же вложенного меню, чтобы полностью удалить выходные данные.

    Команда **Toggle** (Переключить) скрывает только последние выходные данные из ячейки; если выполнить ячейку еще раз, выходные данные появятся снова.

1. Так как пакеты устанавливаются в среде проекта, закомментируйте команды `! pip install` символом `#` — таким образом они могут оставаться в записной книжке как учебный материал, но не будут занимать время на выполнение и создавать ненужные выходные данные. В этом случае сохранение закомментированных команд в записной книжке также указывает зависимости записной книжки.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Создание остальных ячеек

Чтобы заполнить оставшуюся часть записной книжки, создайте далее ряд ячеек типа Markdown и ячеек с кодом. Для каждой из перечисленных ниже ячеек сначала создайте новую ячейку, затем задайте тип и наконец вставьте содержимое.

Несмотря на то что можно дождаться запуска записной книжки после создания каждой ячейки, интерес представляет выполнение каждой из ячеек по мере их создания. Не во всех ячейках отображаются выходные данные. Если ошибок нет, предполагается, что ячейка выполнена нормально.

Код каждой ячейки зависит от кода, выполненного в предыдущих ячейках, и если пренебречь выполнением одной из ячеек, при выполнении последующих ячеек могут возникнуть ошибки. Если вы обнаружили, что забыли выполнить ту или иную ячейку, попробуйте воспользоваться командой **Cell** > **Run All Above** (Ячейка > Выполнить все предыдущие) перед выполнением текущей ячейки.

Если отображаются непредвиденные результаты (что вполне вероятно), убедитесь, что для каждой ячейки задан тип Code (Код) или Markdown. Например, ошибка "Invalid syntax" (Недопустимый синтаксис) обычно появляется при вводе кода Markdown в ячейку типа Code (Код).

1. Ячейка Markdown:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Ячейка Code. При выполнении отображает содержимое таблицы в качестве выходных данных. Выходные данные можно скрыть, закомментировав инструкцию `print`.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Этот код может выводить предупреждения "numpy.dtype size changed" (размер numpy.dtype изменен). Их можно проигнорировать.

1. Ячейка Markdown:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Ячейка Code. При выполнении выходные данные не отображаются.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Ячейка Markdown:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept`).
    ```

1. Ячейка Code. При выполнении отображаются выходные данные: `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Ячейка Markdown:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_` and `coef_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Ячейка Code. При выполнении отображаются примерно такие результаты: `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Ячейка Markdown:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Ячейка Markdown:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Ячейка Code. При выполнении эта ячейка создает график. Если в первый раз график не отображается (а вместо него отображается надпись "Figure size 640x480 with 1 Axes" (Размер рисунка 640x480 с 1 осью)), выполните ячейку повторно.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Вывод графика из кода matplotlib](media/tutorial/tutorial-plot-output.png)

1. Ячейка Markdown:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Очистка выходных данных и повторное выполнение всех ячеек

После выполнения действий, описанных в предыдущем разделе, для заполнения всей записной книжки вы создали фрагмент выполняемого кода в контексте полного руководства по линейной регрессии. Это прямое сочетание кода и текста является одним из важнейших преимуществ записных книжек.

Попробуйте теперь повторно выполнить всю записную книжку:

1. Очистите все данные сеанса ядра и все выходные данные ячеек, выбрав **Kernel** > **Restart & Clear Output** (Ядро > Перезапустить и очистить выходные данные). Эту команду всегда рекомендуется выполнять после завершения создания записной книжки, чтобы убедиться, что вы не создали странных зависимостей между ячейками кода.

1. Повторно запустите записную книжку, выбрав командой **Cell** > **Run All** (Ячейка > Выполнить все). Обратите внимание, что во время выполнения кода индикатор ядра заполняется.

    Если какой-либо код выполняется слишком долго или "зависает" по иной причине, можно остановить ядро, выбрав **Kernel** > **Interrupt** (Ядро > Прервать).

1. Прокрутите записную книжку, чтобы проанализировать результаты. (Если график снова не отображается, повторно выполните соответствующую ячейку.)

## <a name="save-halt-and-close-the-notebook"></a>Сохранение, остановка и закрытие записной книжки

Во время редактирования записной книжки можно сохранить ее текущее состояние с помощью команды **File** > **Save and Checkpoint** (Файл > Сохранить и создать контрольную точку) или кнопки сохранения на панели инструментов. "Контрольная точка" создает моментальный снимок, к которому можно вернуться к любой момент во время сеанса. Контрольные точки позволяют сделать ряд экспериментальных изменений, и если эти изменения не работают, можно просто вернуться к контрольной точке с помощью команды **File** > **Revert to Checkpoint** (Файл > Вернуться к контрольной точке). Альтернативный подход — создать дополнительные ячейки и закомментировать код, который не следует выполнять. Подойдет любой из этих способов.

Также можно с помощью команды **File** > **Make a Copy** (Файл > Создать копию) в любой момент создать копию текущего состояния записной книжки в новом файле в проекте. Эта копия автоматически откроется в новой вкладке браузера.

Завершив работу с записной книжкой, воспользуйтесь командой **File** > **Close and halt** (Файл > Закрыть и остановить), которая закрывает записную книжку и завершает работу выполняющего ее ядра. Затем служба "Записные книжки Azure" автоматически закроет вкладку браузера.

## <a name="debug-notebooks-using-visual-studio-code"></a>Отладка записных книжек с помощью Visual Studio Code

Если ячейки кода в записной книжке не работают ожидаемым образом, возможно, в коде есть ошибки или другие проблемы. Однако типичная среда Jupyter не предлагает других средств отладки для просмотра значения переменных, кроме `print`.

К счастью, вы можете загрузить файл *.ipynb* записной книжки, затем открыть его в Visual Studio Code, используя расширения Python. Расширение непосредственно импортирует записную книжку как один файл кода, сохраняя ячейки Markdown в комментариях. Как только записная книжка импортирована, используйте отладчик Visual Studio Code для пошагового выполнения кода, установления точек перерыва, проверки состояния и т. д. После внесения исправлений в код экспортируйте файл *.ipynb* из Visual Studio Code и оправьте его обратно в Записные книжки Azure.

Дополнительные сведения см. в документации Visual Studio Code [Working with Jupyter Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) (Работа с Jupyter Notebooks в Visual Studio Code)

Чтобы узнать больше о дополнительных функциях Visual Studio Code для записных книжек Jupyter, см. [эту статью](https://code.visualstudio.com/docs/python/jupyter-support).

## <a name="next-steps"></a>Дополнительная информация

- [Обзор примеров записных книжек](azure-notebooks-samples.md)

Статьи с инструкциями, посвященные таким темам:

- [Создание и клонирование проектов](create-clone-jupyter-notebooks.md)
- [Администрирование и настройка проектов](configure-manage-azure-notebooks-projects.md)
- [Установка пакетов из записной книжки](install-packages-jupyter-notebook.md)
- [Представление слайд-шоу](present-jupyter-notebooks-slideshow.md)
- [Работа с файлами данных](work-with-project-data-files.md)
- [Доступ к ресурсам данных](access-data-resources-jupyter-notebooks.md)
- [Работа со Службами машинного обучения Azure](use-machine-learning-services-jupyter-notebooks.md)
