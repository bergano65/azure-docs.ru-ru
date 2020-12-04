---
title: Подключение фабрики данных к Azure зрения
description: Узнайте, как подключить фабрику данных к Azure зрения
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 94b2ed8a25ca5cc837f6677dea1c0bbb54225fcd
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603351"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Подключение фабрики данных к Azure зрения (Предварительная версия)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье объясняется, как подключить фабрику данных к Azure зрения и как сообщить о преобразовании данных в ADF, скопировать данные, поток данных и выполнить пакет служб SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Подключение фабрики данных к Azure зрения
Azure зрения — это новая облачная служба, которая используется пользователями данных для централизованного управления данными, охватывающими облачные и локальные среды. Вы можете подключить фабрику данных к Azure зрения, и подключение позволяет использовать зрения Azure для сбора данных журнала обращений и преобразований для копирования, потока данных и выполнения пакета служб SSIS. Сведения о регистрации фабрики данных в Azure зрения см. в статье [Подключение фабрики данных Azure и Azure зрения](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Отчет о данных журнала преобразований в Azure зрения
Когда клиенты выполняют операции копирования, потока данных или выполнения пакетов служб SSIS в фабрике данных Azure, клиенты могут получить отношение зависимости и иметь общий обзор всего процесса рабочего процесса между источниками данных и назначением.
Сведения о том, как получить журнал обращений и преобразований из фабрики данных Azure, см. в разделе [Журнал преобразований](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities)

## <a name="next-steps"></a>Дальнейшие действия
[Пользовательская структура журнала преобразований каталога](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Руководство. Отправка данных журнала преобразований в фабрике данных в Azure зрения](turorial-push-lineage-to-purview.md)