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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218036"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Машинное обучение Azure Studio (классическая модель): расширение эксперимента с помощью R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Функциональные возможности Машинное обучение Azure Studio (классическая модель) можно расширить с помощью языка R, используя модуль « [выполнение сценария r][execute-r-script] ».

Этот модуль принимает несколько входных наборов данных и выдает один выходной набор данных. Можно ввести скрипт R в параметр **r Script** модуля [EXECUTE r Script][execute-r-script] .

Для доступа к каждому входному порту модуля используется код, аналогичный приведенному ниже:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Вывод списка всех установленных пакетов
Список установленных пакетов может меняться. Список установленных в настоящее время пакетов можно найти в [пакетах R, поддерживаемых машинное обучение Azure Studio (классическая модель)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Вы также можете получить полный текущий список установленных пакетов, введя следующий код в модуль [выполнить сценарий R][execute-r-script] :

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

В результате список пакетов будет отправлен в порт вывода модуля [выполнить сценарий R][execute-r-script] .
Чтобы просмотреть список пакетов, подключите модуль преобразования, например [Convert to CSV][convert-to-csv] , к левому выходу модуля [выполнить сценарий R][execute-r-script] , запустите эксперимент, а затем щелкните выходные данные модуля преобразования и выберите **загрузить**. 

![Скачивание выходных данных модуля "Преобразовать в CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Импорт пакетов
Вы можете импортировать пакеты, которые еще не установлены, с помощью следующих команд в модуле [выполнить сценарий R][execute-r-script] :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

Ваш пакет содержится в файле `my_favorite_package.zip`.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
