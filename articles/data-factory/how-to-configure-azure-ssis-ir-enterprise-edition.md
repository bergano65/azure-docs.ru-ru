---
title: 'Подготовка выпуска Enterprise Edition для среды выполнения интеграции Azure Integration Services '
description: В этой статье описаны возможности выпуска Enterprise Edition для среды выполнения интеграции Azure Integration Services, а также процедура его подготовки.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8c9db18d416d508dd5a98cbacc14b7db6c075a55
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673957"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Подготовка выпуска Enterprise Edition для среды выполнения интеграции Azure Integration Services

Выпуск Enterprise Edition среды выполнения интеграции Azure SSIS позволяет использовать следующие расширенные возможности и возможности уровня "Премиум":
-   компоненты отслеживания измененных данных;
-   соединители Oracle, Teradata и SAP BW;
-   соединители и преобразования SQL Server Analysis Services и Azure Analysis Services;
-   преобразования нечетких группирований и нечетких уточняющих запросов;
-   Преобразования извлечения терминов и поиска по терминам

Некоторым из этих возможностей требуется установка дополнительных компонентов для настройки среды выполнения интеграции Azure SSIS. Дополнительные сведения об установке дополнительных компонентов см. в статье [Пользовательская установка для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Корпоративные функции

| **Корпоративные функции** | **Описания** |
|---|---|
| Компоненты CDC | Источник CDC, задача управления и преобразование "Разделитель" предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. Чтобы подключиться к Oracle, необходимо установить CDC Designer и службу на другом компьютере. |
| Соединители Oracle | Диспетчер подключений, источник и назначение Oracle предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. Также необходимо установить драйвер интерфейса OCI (Oracle Call Interface) и при необходимости настроить Oracle Transport Network Substrate в среде выполнения интеграции Azure SSIS. Дополнительные сведения см. в статье о [настройке среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Соединители Teradata | Необходимо установить диспетчер подключений, источник и назначение Teradata, а также API параллельного транспортера Teradata и драйвер ODBC Teradata в среде выполнения интеграции Azure SSIS Enterprise Edition. Дополнительные сведения см. в статье о [настройке среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Соединители SAP BW | Диспетчер подключений, источник и назначение SAP BW предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. Необходимо также установить драйвер SAP BW в среде выполнения интеграции Azure SSIS. Эти соединители поддерживают SAP BW версии 7.0 или более ранних. Чтобы подключиться к более поздним версиям SAP BW или другим продуктам SAP, можно приобрести и установить соединители SAP от сторонних независимых поставщиков программного обеспечения в среде выполнения интеграции Azure SSIS. Дополнительные сведения об установке дополнительных компонентов см. в статье [Пользовательская установка для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Компоненты Analysis Services               | Назначение обучения модели интеллектуального анализа данных, назначение обработки измерений, назначение обработки секций, а также преобразование запросов интеллектуального анализа данных предварительно установлены в среде выполнения интеграции Azure SSIS. Все эти компоненты поддерживают SQL Server Analysis Services, однако Azure Analysis Services поддерживается только назначением обработки секций. Чтобы подключиться к SQL Server Analysis Services, необходимо [настроить учетные данные проверки подлинности Windows в SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Помимо этих компонентов, задача "DDL выполнения Analysis Services", задача "Обработка средствами Analysis Services" и задача "Запрос интеллектуального анализа данных" также предварительное установлены в среде выполнения интеграции Azure SSIS выпуска Standard и Enterprise Edition. |
| преобразования нечетких группирований и нечетких уточняющих запросов;  | Преобразования нечетких группирований и нечетких уточняющих запросов предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. При хранении эталонных данных эти компоненты поддерживают и сервер SQL Server, и Базу данных SQL Azure. |
| Преобразования извлечения терминов и поиска по терминам | Преобразования извлечения терминов и поиска по терминам предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. При хранении эталонных данных эти компоненты поддерживают и сервер SQL Server, и Базу данных SQL Azure. |

## <a name="instructions"></a>Указания

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Скачайте и установите [Azure PowerShell](/powershell/azure/install-az-ps).

2.  При подготовке или перенастройке среды выполнения интеграции Azure SSIS с помощью PowerShell, прежде чем ее запускать, выполните команду `Set-AzDataFactoryV2IntegrationRuntime` с **Enterprise** в качестве значения для параметра **Edition**. Ниже приведен пример скрипта:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Дальнейшие действия

-   [Пользовательская установка для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Разработка платных или лицензионных пользовательских компонентов для среды выполнения интеграции Azure SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
