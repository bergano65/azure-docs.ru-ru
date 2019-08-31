---
title: Средства разработки
titleSuffix: Azure Data Science Virtual Machine
description: Сведения о средствах и интегрированных средах разработки, доступных на виртуальной машине для обработки и анализа данных.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 6ff4d92cb3730716c532332bf426132fcbb8e122
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191947"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Средства разработки на виртуальной машине Azure для обработки и анализа данных

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

