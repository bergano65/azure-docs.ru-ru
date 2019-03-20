---
title: Настройка Azure-SSIS Integration Runtime для отработки отказа базы данных SQL | Документация Майкрософт
description: В этой статье описывается настройка Azure-SSIS Integration Runtime с помощью георепликации базы данных SQL Azure и отработка отказа для базы данных SSISDB
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: dea0153b9ca6d8e751fd94cc558abd44b2591907
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453037"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Настройка Azure-SSIS Integration Runtime с помощью георепликации базы данных SQL Azure и отработка отказа

В этой статье описывается настройка Azure-SSIS Integration Runtime с помощью георепликации базы данных SQL Azure для базы данных SSISDB. В процессе отработки отказа работоспособность Azure-SSIS Integration Runtime можно отслеживать через базу данных-получатель.

Дополнительные сведения о георепликации и отработка отказа для базы данных SQL, см. в разделе [Обзор: Активные группы георепликации и автоматической отработки отказа](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Сценарий 1. Azure-SSIS IR указывает на конечную точку прослушивателя записи

### <a name="conditions"></a>Условия

Данный раздел применяется при выполнении следующих условий:

- Azure-SSIS IR указывает на конечную точку прослушивателя записи группы отработки отказа.

  И

- В сервере базы данных SQL *не* настроено правило конечной точки службы для виртуальной сети.

### <a name="solution"></a>Решение

Когда происходит отработка отказа, она является прозрачной для Azure-SSIS IR. Azure-SSIS IR автоматически подключается к новой первичной точке группы отработки отказа.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Сценарий 2. Azure-SSIS IR указывает на конечную точку основного сервера

### <a name="conditions"></a>Условия

Данный раздел применяется при выполнении одного из следующих условий:

- Azure-SSIS IR указывает на конечную точку основного сервера группы отработки отказа. Эта конечная точка изменяется при отработке отказа.

  Или

- В сервере базы данных SQL Azure настроено правило конечной точки службы для виртуальной сети.

  Или

- Сервер базы данных является управляемым экземпляром базы данных SQL, настроенным с помощью виртуальной сети.

### <a name="solution"></a>Решение

При отработке отказа необходимо выполнить следующие действия:

1. Остановите среду выполнения интеграции Azure SSIS.

2. Для основной конечной точки и виртуальной сети в новом регионе необходимо перенастроить среду выполнения интеграции.

3. Перезапустите среду выполнения интеграции.

В следующих разделах данные шаги описаны более подробно.

### <a name="prerequisites"></a>Технические условия

- Убедитесь, что вы активировали аварийное восстановление для сервера Базы данных SQL на случай сбоя в работе сервера в то же время. Дополнительные сведения см. в статье [Обзор обеспечения непрерывности бизнес-процессов с помощью Базы данных SQL Azure](../sql-database/sql-database-business-continuity.md).

- Если вы используете виртуальную сеть в текущем регионе, нужно использовать другую виртуальную сеть в новом регионе для подключения среды выполнения интеграции Azure-SSIS. Дополнительные сведения см. в статье [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md).

- Если вы используете выборочную установку, может потребоваться подготовить еще один универсальный код ресурса SAS для контейнера больших двоичных объектов, в котором хранится сценарий выборочной установки и связанные с ним файлы, чтобы он оставался доступным во время сбоя. Дополнительные сведения см. в статье [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Действия

Выполните следующие шаги, чтобы остановить среду выполнения интеграции Azure-SSIS, переключить ее в новый регион и запустить снова.

1. Остановите среду выполнения интеграции в исходном регионе.

2. Чтобы обновить настройки в среде выполнения интеграции, вызовите следующую команду в PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Дополнительные сведения об этой команде PowerShell см. в статье [Создание среды выполнения интеграции Azure SSIS в службе "Фабрика данных Azure"](create-azure-ssis-integration-runtime.md).

3. Запустите среду выполнения интеграции снова.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь со следующими параметрами конфигурации для среды выполнения интеграции Azure-SSIS:

- [Настройка среды выполнения интеграции Azure-SSIS для высокой производительности](configure-azure-ssis-integration-runtime-performance.md);

- [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md);

- [Подготовка выпуска Enterprise Edition для среды выполнения интеграции Azure Integration Services](how-to-configure-azure-ssis-ir-enterprise-edition.md).
