---
title: Установка средств Azure Data Lake для Visual Studio
description: В этой статье описано, как установить Средства Azure Data Lake для Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d153ba1d00c0e43a5809503e047fb66713b7fb7c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104934"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Установка инструментов Data Lake для Visual Studio

Узнайте, как использовать Visual Studio для создания учетных записей Azure Data Lake Analytics. Задания можно определять в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправлять задания в службу Data Lake Analytics. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Предварительные требования

* **Visual Studio**: поддерживаются все выпуски, кроме Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK для .NET** (версии 2.7.1 или выше). Можно установить его с помощью [установщика веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).
* Учетная запись **Data Lake Analytics** . Чтобы создать учетную запись, ознакомьтесь со статьей [Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Установка Средства Azure Data Lake для Visual Studio 2017 или Visual Studio 2019

Средства Azure Data Lake для Visual Studio поддерживается в Visual Studio 2017 15,3 или более поздней версии. Это средство является частью рабочих нагрузок **хранения и обработки данных** и **разработки Azure** . Включите одну из этих рабочих нагрузок как часть установки Visual Studio.

Включите рабочую нагрузку **Хранение и обработка данных**, как показано ниже.

![Включение рабочей нагрузки для хранения и обработки данных](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Включите рабочую нагрузку **Разработка для Azure**, как показано ниже.

![Выбор рабочей нагрузки разработки Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Установка Средств Azure Data Lake для Visual Studio 2013 и 2015

Скачайте и установите [Microsoft Azure Data Lake и средства Stream Analytics для Visual Studio ](https://aka.ms/adltoolsvs). После установки Visual Studio будет иметь следующие изменения:

* Узел **Обозреватель сервера**  >  **Azure** содержит узел **Data Lake Analytics** .
* в меню **Средства** появился пункт **Data Lake**.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о том, как записывать диагностические данные в журнал, см. в статье [Доступ к журналам диагностики для Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Сведения об использовании представления выполнения вершин см. [в разделе Использование представления выполнения вершин в Data Lake инструментов для Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
