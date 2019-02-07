---
title: Расширение возможностей эксперимента с помощью R
titleSuffix: Azure Machine Learning Studio
description: Рассматривается, как расширить функциональные возможности Студии машинного обучения Microsoft Azure с помощью языка R, используя модуль «Выполнение сценария R».
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 98f5922cc329aedc8deeffbcb042bddf90405883
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497166"
---
# <a name="azure-machine-learning-studio-extend-your-experiment-with-r"></a>Студия машинного обучения Azure: Расширение возможностей эксперимента с помощью R 
Вы можете расширить функциональные возможности Студии машинного обучения Azure с помощью языка R, используя модуль [Выполнение скрипта R][execute-r-script].

Этот модуль принимает несколько входных наборов данных и выдает один выходной набор данных. Вы можете ввести сценарий R в качестве значения параметра **R-скрипт** в модуле [Выполнить сценарий R][execute-r-script].

Для доступа к каждому входному порту модуля используется код, аналогичный приведенному ниже:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Вывод списка всех установленных пакетов
Список установленных пакетов может меняться. Список установленных пакетов см. в статье [R Packages Supported by Azure Machine Learning Studio](https://msdn.microsoft.com/library/azure/mt741980.aspx) (Пакеты R, поддерживаемые в Студии машинного обучения Azure).

Чтобы получить полный актуальный список установленных пакетов, введите следующий код в модуле [Выполнение скрипта R][execute-r-script]:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

При этом список пакетов будет отправлен в порт вывода модуля [Выполнить сценарий R][execute-r-script].
Чтобы просмотреть список пакетов, подключите модуль преобразования, например [Преобразовать в CSV][convert-to-csv], к выходным данным модуля [Выполнить сценарий R][execute-r-script] слева, выполните эксперимент, а затем щелкните выходные данные модуля преобразования и выберите **Скачать**. 

![Скачивание выходных данных модуля "Преобразовать в CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Импорт пакетов
Чтобы импортировать пакеты, которые еще не установлены, выполните следующие команды в модуле [Выполнение скрипта R][execute-r-script]:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

Ваш пакет содержится в файле `my_favorite_package.zip`.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
