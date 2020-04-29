---
title: Расширение возможностей эксперимента с помощью R
titleSuffix: ML Studio (classic) - Azure
description: Как расширить функциональные возможности Машинное обучение Azure Studio (классическая модель) на языке R с помощью модуля выполнить сценарий R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218036"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Машинное обучение Azure Studio (классическая модель): расширение эксперимента с помощью R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Функциональные возможности Машинное обучение Azure Studio (классическая модель) можно расширить с помощью языка R, используя модуль « [выполнение сценария r][execute-r-script] ».

Этот модуль принимает несколько входных наборов данных и выдает один выходной набор данных. Вы можете ввести сценарий R в качестве значения параметра **R-скрипт** в модуле [Выполнить сценарий R][execute-r-script].

Для доступа к каждому входному порту модуля используется код, аналогичный приведенному ниже:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Вывод списка всех установленных пакетов
Список установленных пакетов может меняться. Список установленных в настоящее время пакетов можно найти в [пакетах R, поддерживаемых машинное обучение Azure Studio (классическая модель)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Чтобы получить полный актуальный список установленных пакетов, введите следующий код в модуле [Выполнение скрипта R][execute-r-script]:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

При этом список пакетов будет отправлен в порт вывода модуля [Выполнить сценарий R][execute-r-script].
Чтобы просмотреть список пакетов, подключите модуль преобразования, например [Преобразовать в CSV][convert-to-csv], к выходным данным модуля [Выполнить сценарий R][execute-r-script] слева, выполните эксперимент, а затем щелкните выходные данные модуля преобразования и выберите **Скачать**. 

![Скачивание выходных данных модуля "Преобразовать в CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Импорт пакетов
Чтобы импортировать пакеты, которые еще не установлены, выполните следующие команды в модуле [Выполнение скрипта R][execute-r-script]:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

Ваш пакет содержится в файле `my_favorite_package.zip`.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
