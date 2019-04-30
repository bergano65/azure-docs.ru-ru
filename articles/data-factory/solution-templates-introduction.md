---
title: Общие сведения о шаблонах для Фабрики данных Azure | Документация Майкрософт
description: Узнайте, как использовать предварительно определенный шаблон, чтобы быстро начать работу с Фабрикой данных Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4bd38991b2452bdda65a7647f844dcc17fdfb125
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60786976"
---
# <a name="templates"></a>Шаблоны

Шаблоны — это предварительно определенные конвейеры Фабрики данных Azure, которые позволяют быстро начать работу с этой службой. Шаблоны нужны в тех случаях, когда вы еще не знакомы с Фабрикой данных и хотите быстро начать работу с ней. Эти шаблоны позволяют сократить время разработки для создания проектов интеграции данных, тем самым улучшая производительность разработчика.

## <a name="create-data-factory-pipelines-from-templates"></a>Создание конвейеров Фабрики данных на основе шаблонов

Вы можете приступить к созданию конвейера Фабрики данных на основе шаблона двумя приведенными ниже способами:

1.  Выберите **Create pipeline from template** (Создать конвейер с помощью шаблона) на странице Overview (Обзор), чтобы открыть коллекцию шаблонов.

    ![Открытие коллекции шаблонов на странице Overview (Обзор)](media/solution-templates-introduction/templates-intro-image1.png)

1.  На вкладке Author (Автор) в обозревателе ресурсов выберите **+**, затем **Pipeline from template** (Конвейер из шаблона), чтобы открыть коллекцию шаблонов.

    ![Открытие коллекции шаблонов на вкладке Author (Автор)](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Коллекция шаблонов

![Коллекция шаблонов](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Готовые шаблоны Фабрики данных

Фабрика данных использует шаблоны Azure Resource Manager для сохранения шаблонов конвейера фабрики данных. Вы увидите все шаблоны Resource Manager, а также файл манифеста, используемые для шаблонов фабрики данных поле, в [официальный репозиторий GitHub фабрики данных Azure](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Предварительно определенные шаблоны, предоставляемые корпорацией Майкрософт, включают, в частности, следующие элементы:

-   Шаблоны копирования:

    -   [Массовое копирование из базы данных](solution-template-bulk-copy-with-control-table.md)
    
    -   [Копировать новые файлы, Дата последнего изменения](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Копирование нескольких контейнеров файлов между файловыми хранилищами](solution-template-copy-files-multiple-containers.md)

    -   [Разностное копирование из базы данных](solution-template-delta-copy-with-control-table.md)

    -   Копирование из \<источника\> в \<назначение\>

        -   Из Amazon S3 в Azure Data Lake Store 2-го поколения

        -   Из Google Big Query в Azure Data Lake Store 2-го поколения

        -   Из HDF в Azure Data Lake Store 2-го поколения

        -   Из Netezza в Azure Data Lake Store 1-го поколения

        -   Из локального экземпляра SQL Server в Базу данных SQL Azure

        -   Из локального экземпляра SQL Server в Хранилище данных SQL Azure

        -   Из локального экземпляра Oracle в Хранилище данных SQL Azure

-   Шаблоны SSIS

    -   Планирование Azure-SSIS Integration Runtime для выполнения пакетов SSIS

-   Шаблоны преобразования

    -   [Извлечение, преобразование и загрузка с Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Мои шаблоны

Конвейер можно также сохранить как шаблон, выбрав **Save as template** (Сохранить как шаблон) на вкладке Pipeline (Конвейер).

![Сохранение конвейера как шаблон](media/solution-templates-introduction/templates-intro-image4.png)

В разделе **My Templates** (Мои шаблоны) в коллекции шаблонов можно просмотреть конвейеры, сохраненные в виде шаблонов. Также их можно увидеть в разделе **Templates** (Шаблоны) обозревателя ресурсов.

![Мои шаблоны](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Чтобы использовать функцию My Templates (Мои шаблоны), необходимо включить интеграцию с GIT. Поддерживаются GIT Azure DevOps и GitHub.
