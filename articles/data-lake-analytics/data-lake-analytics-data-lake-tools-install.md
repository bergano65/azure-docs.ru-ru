---
title: Установка средств Azure Data Lake для Visual Studio
description: В этой статье описано, как установить Средства Azure Data Lake для Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914101"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Установка инструментов Data Lake для Visual Studio

Узнайте, как использовать Visual Studio для создания учетных записей Аналитики озер данных Azure. Вы можете определить вакансии в [U-S'L](data-lake-analytics-u-sql-get-started.md) и представить вакансии в службу аналитики Data Lake. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Предварительные требования

* **Visual Studio**: поддерживаются все выпуски, кроме Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK для .NET** (версии 2.7.1 или выше). Можно установить его с помощью [установщика веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).
* Учетная запись **Data Lake Analytics.** Чтобы создать учетную запись, ознакомьтесь со статьей [Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Установка инструментов Azure Data Lake для Visual Studio 2017 или Visual Studio 2019

Инструменты Azure Data Lake для визуальной студии поддерживаются в Visual Studio 2017 15.3 или позже. Этот инструмент является частью рабочих нагрузок для **хранения и обработки данных** и **разработки Azure.** Включите одну из этих рабочих нагрузок как часть установки Visual Studio.

Включите рабочую нагрузку **Хранение и обработка данных**, как показано ниже.

![Включить рабочую нагрузку на хранение и обработку данных](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Включите рабочую нагрузку **Разработка для Azure**, как показано ниже.

![Выберите рабочую нагрузку разработки Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Установка Средств Azure Data Lake для Visual Studio 2013 и 2015

Скачать и установить [Microsoft Azure Data Lake и Stream Analytics Tools для визуальной студии. ](https://aka.ms/adltoolsvs) После установки, Visual Studio имеет следующие изменения:

* Узел **Server Explorer** > **Azure** содержит узел **анализа озера данных.**
* в меню **Средства** появился пункт **Data Lake**.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о том, как записывать диагностические данные в журнал, см. в статье [Доступ к журналам диагностики для Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Чтобы использовать представление о выполнении вершины, [см.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
