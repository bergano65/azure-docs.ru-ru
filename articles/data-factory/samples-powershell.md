---
title: Примеры Azure PowerShell для фабрики данных Azure
description: Примеры Azure PowerShell — это скрипты для создания фабрик данных и управления ими.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 9abf4b46896a3c067cfb189da7921dfe318aedab
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229386"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Примеры Azure PowerShell для фабрики данных Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В следующей таблице содержатся ссылки на примеры скриптов Azure PowerShell для фабрики данных Azure.

| Сценарий | Описание  |
|---|---|
|**Копирование данных**||
|[Копирование больших двоичных объектов из одной папки в другую в хранилище BLOB-объектов Azure](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Скрипт PowerShell копирует большие двоичные объекты из папки в хранилище BLOB-объектов Azure в другую папку в том же хранилище. |
|[Копирование данных из SQL Server в хранилище BLOB-объектов Azure](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот сценарий PowerShell копирует данные из базы данных SQL Server в хранилище BLOB-объектов Azure. |
|[Массовое копирование](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот пример сценария PowerShell копирует данные из нескольких таблиц в базе данных SQL Azure в хранилище данных SQL Azure. |
|[Добавочное копирование](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот пример сценария PowerShell загружает в приемник только новые или обновленные записи из исходного хранилища данных после первоначального полного копирования данных из источника в приемник. |
|**Преобразование данных**||
|[Преобразование данных с помощью кластера Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот скрипт PowerShell преобразовывает данные путем запуска программы на кластере Spark. |
|**Перенос пакетов SSIS в Azure**||
|[Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Этот сценарий PowerShell подготавливает к работе среду выполнения интеграции Azure и SSIS, которая запускает пакеты служб SQL Server Integration Services (SSIS) в Azure. |



