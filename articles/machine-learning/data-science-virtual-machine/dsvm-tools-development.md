---
title: Инструменты разработки
titleSuffix: Azure Data Science Virtual Machine
description: Узнайте об инструментах и интегрированных средах разработки, доступных на виртуальной машине Data Science.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282687"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Инструменты разработки на виртуальной машине Azure Data Science

Виртуальная машина Data Science (DSVM) объединяет несколько популярных инструментов в высокопроизводительной интегрированной среде разработки (IDE). Ниже приведены некоторые средства, предоставляемые на виртуальной машине для обработки и анализа данных.

## <a name="visual-studio-community-edition"></a>выпуск Visual Studio Community;

|    |           |
| ------------- | ------------- |
| Что это такое?   | Общий целевой IDE      |
| Поддерживаемые версии DSVM      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Распространенные способы применения      | Разработка программного обеспечения    |
| Как он настроен и устанавливается на DSVM?      | Рабочая нагрузка обработки и анализа данных (средства Python и R), рабочая нагрузка Azure (Hadoop, Data Lake), средства Node.js и SQL Server, [Машинное обучение Azure для Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Как использовать и запустить его      | Рабочий стол`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`ярлык (). Графически, открыть Visual Studio с помощью значка рабочего стола или меню **"Пуск".** Откройте окно поиска программ (кнопки Windows + S) и введите **Visual Studio**. Здесь можно создавать проекты на разных языках, например C#, Python, R и Node.js.   |
| Похожие инструменты на DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Может появиться сообщение о том, что период ознакомления завершился. Введите учетные данные для учетной записи Майкрософт. Вы можете создать новую бесплатную учетную запись, чтобы получить доступ к Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Что это такое?   | Общий целевой IDE      |
| Поддерживаемые версии DSVM      | Windows, Linux     |
| Распространенные способы применения      | Редактор кода и интеграция Git   |
| Как использовать и запустить его      | Кратчайший сотк рабочего стола (`C:\Program Files (x86)\Microsoft VS Code\Code.exe``code`) в Windows, настольный ярлык или терминал ( ) в Linux    |
| Похожие инструменты на DSVM      |     Визуальная студия, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Что это такое?   | Клиент IDE для языка R   |
| Поддерживаемые версии DSVM      | Windows, Linux      |
| Распространенные способы применения      |  Разработка на языке R     |
| Как использовать и запустить его      | Настольный`C:\Program Files\RStudio\bin\rstudio.exe`ярлык ( ) на`/usr/bin/rstudio`Windows, настольный ярлык ( ) на Linux      |
| Похожие инструменты на DSVM      |   Визуальная студия, Визуальный код студии, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Что это такое?   | Клиент IDE для языка R   |
| Что это такое?   | Интегрированная веб-среда разработки для R    |
| Поддерживаемые версии DSVM      | Linux      |
| Распространенные способы применения      |  Разработка на языке R     |
| Как использовать и запустить его      | Включите службу с _systemctl включить rstudio-сервер,_ а затем запустить службу с _systemctl начать rstudio-сервер_. Затем вопийте на RStudio\/Server на http: /your-vm-ip:8787.       |
| Похожие инструменты на DSVM      |   Визуальная студия, визуальный код студии, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Что это такое?   | Интегрированная среда разработки клиента для языка Julia   |
| Поддерживаемые версии DSVM      | Windows, Linux      |
| Распространенные способы применения      |  Разработка на языке Julia     |
| Как использовать и запустить его      | Настольный`C:\JuliaPro-0.5.1.1\Juno.bat`ярлык ( ) на`/opt/JuliaPro-VERSION/Juno`Windows, настольный ярлык ( ) на Linux      |
| Похожие инструменты на DSVM      |   Визуальная студия, Визуальный код студии, RStudio      |

## <a name="pycharm"></a>PyCharm

|    |           |
| ------------- | ------------- |
| Что это такое?   | Интегрированная среда разработки клиента для языка Python    |
| Поддерживаемые версии DSVM      | Windows 2019, Linux      |
| Распространенные способы применения      |  Разработка на Python     |
| Как использовать и запустить его      | Настольный`C:\Program Files\tk`ярлык ( ) на Windows. Рабочий стол`/usr/bin/pycharm`ярлык ( ) на Linux      |
| Похожие инструменты на DSVM      |   Визуальная студия, Визуальный код студии, RStudio      |
