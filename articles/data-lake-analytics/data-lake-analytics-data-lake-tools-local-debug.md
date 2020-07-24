---
title: Локальная отладка кода Azure Data Lake Analytics
description: Узнайте, как выполнять отладку заданий U-SQL на локальной рабочей станции с помощью Средств Azure Data Lake для Visual Studio.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: how-to
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 7176e2f64faa8e5b43574084a9cbd947dbd576d3
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125642"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Локальная отладка кода Azure Data Lake Analytics

Вы можете использовать Средства Azure Data Lake для Visual Studio для выполнения и отладки кода Azure Data Lake Analytics не только в службе Azure Data Lake Analytics, но и на локальной рабочей станции.

Узнайте, как [выполнить скрипт U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Локальная отладка скриптов и сборок C#

Сборки C# можно отлаживать, не отправляя и не регистрируя их в службе Azure Data Lake Analytics. Точки останова можно устанавливать как в файле кода программной части, так и в указанном в ссылке проекте C#.

### <a name="debug-local-code-in-a-code-behind-file"></a>Отладка локального кода в файле кода программной части

1. Установите точки останова в файле кода программной части.
2. Нажмите клавишу **F5** для запуска локальной отладки скрипта.

> [!NOTE]
   > Описанные далее действия работают только в Visual Studio 2015. В более ранних версиях Visual Studio **PDB-файлы**, возможно, потребуется добавить вручную.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Отладка локального кода в указанном в ссылке проекте C#

1. Создайте проект сборки C# и скомпилируйте его, чтобы получить выходной **DLL-файл**.
2. Зарегистрируйте **DLL-файл** с помощью инструкции U-SQL:

   ```sql
   CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
   ```
   
3. Установите точки останова в коде C#.
4. Нажмите клавишу **F5**, чтобы запустить отладку скрипта, ссылаясь на локальный **DLL-файл** C#.


## <a name="next-steps"></a>Дальнейшие действия

- Пример более сложного запроса см. в статье [Анализ журналов веб-сайта с помощью Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Сведения о просмотре сведений о задании см. в статье [Использование обозревателя заданий и представления заданий для Azure Data Lake Analyticsных заданий](data-lake-analytics-data-lake-tools-view-jobs.md).
- Сведения об использовании представления выполнения вершин см. [в разделе Использование представления выполнения вершин в Data Lake инструментов для Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
