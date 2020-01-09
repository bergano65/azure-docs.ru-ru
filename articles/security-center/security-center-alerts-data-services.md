---
title: Обнаружение угроз для служб данных в центре безопасности Azure | Документация Майкрософт
description: В этой статье представлены оповещения служб данных, доступные в центре безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665740"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Обнаружение угроз для служб данных в центре безопасности Azure

 Центр безопасности Azure анализирует журналы служб хранилища данных и запускает оповещения при обнаружении угрозы для ресурсов данных. В этой статье перечислены оповещения, создаваемые центром безопасности для следующих служб:

* [База данных SQL Azure и хранилище данных SQL](#data-sql)
* [Хранилище Azure](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## База данных SQL и хранилище данных SQL<a name="data-sql"></a>

Расширенная защита от угроз для базы данных SQL Azure обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования.

Вы увидите оповещения при наличии подозрительных действий с базами данных, потенциальных уязвимостей или атак путем внедрения кода SQL, а также аномальных попыток доступа к базам данных и шаблонов запросов.

Расширенная защита от угроз для базы данных SQL Azure и SQL входит в состав унифицированного пакета [расширенной защиты данных (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) для расширенных возможностей обеспечения безопасности SQL, охватывающих базы данных SQL Azure, управляемые экземпляры базы данных SQL Azure, базы данных хранилища данных SQL Azure и серверы SQL Server на виртуальных машинах Azure.

Дополнительные сведения см. здесь:

* [Включение расширенной защиты от угроз для базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Включение расширенной защиты от угроз для серверов SQL Server на виртуальных машинах Azure](security-center-iaas-advanced-data.md)
* [Список оповещений системы защиты от угроз для базы данных SQL и хранилища данных SQL](alerts-reference.md#alerts-sql-db-and-warehouse)

## Служба хранилища Azure<a name="azure-storage"></a>

Расширенная защита от угроз для хранилища (сейчас доступно только для хранилища BLOB-объектов) определяет необычные и потенциально опасные попытки доступа или использования учетных записей хранения. Этот уровень защиты позволяет устранять угрозы без необходимости быть экспертом по безопасности и помогает управлять системами мониторинга безопасности.

>[!NOTE]
>В настоящее время Расширенная защита от угроз для хранилища недоступна в регионах Azure для государственных организаций и независимых.

Дополнительные сведения см. здесь:

* [Включение расширенной защиты от угроз для службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Список оповещений системы защиты от угроз для службы хранилища Azure](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB оповещения создаются необычными и потенциально опасными попытками доступа или использования Azure Cosmos DB учетных записей.

Дополнительные сведения см. здесь:

* [Расширенная защита от угроз для Azure Cosmos DB (Предварительная версия)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Список оповещений защиты от угроз для Azure Cosmos DB (Предварительная версия)](alerts-reference.md#alerts-azurecosmos)
