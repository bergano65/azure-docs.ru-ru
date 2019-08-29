---
title: Средства разработки виртуальных машин для обработки и анализа данных в Azure | Документация Майкрософт
description: Сведения о средствах и интегрированных средах разработки, предустановленных на виртуальной машине для обработки и анализа данных.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 8f9dad0fb007945b69b75daadfdb12f61dc4defb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074304"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Средства разработки виртуальных машин для обработки и анализа данных

Виртуальная машина для обработки и анализа данных (DSVM) объединяет несколько популярных средств в высокопроизводительную интегрированную среду разработки (IDE). Ниже приведены некоторые средства, предоставляемые на виртуальной машине для обработки и анализа данных.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Что это такое?   | Среда IDE общего назначения      |
| Поддерживаемые версии DSVM      | Windows      |
| Распространенные способы применения      | Разработка программного обеспечения    |
| Как она настраивается и устанавливается на DSVM?      | Рабочая нагрузка обработки и анализа данных (средства Python и R), рабочая нагрузка Azure (Hadoop, Data Lake), средства Node.js и SQL Server, [Машинное обучение Azure для Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Использование и запуск      | Ярлык рабочего стола`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`()    |
| Связанные средства на DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Что это такое?   | Среда IDE общего назначения      |
| Поддерживаемые версии DSVM      | Windows, Linux     |
| Распространенные способы применения      | Редактор кода и интеграция Git   |
| Использование и запуск      | Ярлык рабочего стола`C:\Program Files (x86)\Microsoft VS Code\Code.exe`() в Windows, ярлык рабочего стола или`code`терминал () в Linux    |
| Связанные средства на DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Что это такое?   | Интегрированная среда разработки клиента для языка R   |
| Поддерживаемые версии DSVM      | Windows, Linux      |
| Распространенные способы применения      |  Разработка на языке R     |
| Использование и запуск      | Ярлык рабочего стола`C:\Program Files\RStudio\bin\rstudio.exe`() в Windows, ярлык на`/usr/bin/rstudio`рабочем столе () в Linux      |
| Связанные средства на DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| Что это такое?   | Интегрированная среда разработки клиента для языка R   |
| Что это такое?   | Интегрированная веб-среда разработки для R    |
| Поддерживаемые версии DSVM      | Linux      |
| Распространенные способы применения      |  Разработка на языке R     |
| Использование и запуск      | Включите службу с помощью _systemctl Enable Rstudio-Server_, а затем запустите службу с помощью _systemctl Start Rstudio-Server_. Затем войдите на сервер RStudio по адресу HTTP\/:/йоур-ВМ-ИП: 8787.       |
| Связанные средства на DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Что это такое?   | Интегрированная среда разработки клиента для языка Julia   |
| Поддерживаемые версии DSVM      | Windows, Linux      |
| Распространенные способы применения      |  Разработка на языке Julia     |
| Использование и запуск      | Ярлык рабочего стола`C:\JuliaPro-0.5.1.1\Juno.bat`() в Windows, ярлык на`/opt/JuliaPro-VERSION/Juno`рабочем столе () в Linux      |
| Связанные средства на DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>PyCharm

|    |           |
| ------------- | ------------- |
| Что это такое?   | Интегрированная среда разработки клиента для языка Python    |
| Поддерживаемые версии DSVM      | Linux      |
| Распространенные способы применения      |  Разработка на Python     |
| Использование и запуск      | Ярлык на рабочем столе (`/usr/bin/pycharm`) в Linux      |
| Связанные средства на DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Что это такое?   | Интерактивная визуализация данных и средство бизнес-аналитики    |
| Поддерживаемые версии DSVM      | Windows  |
| Распространенные способы применения      |  Визуализация данных и создание панелей мониторинга   |
| Использование и запуск      | Ярлык рабочего стола`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| Связанные средства на DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

