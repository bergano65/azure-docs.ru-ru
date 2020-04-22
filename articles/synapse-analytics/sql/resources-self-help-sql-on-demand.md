---
title: Самостоятельное устранение проблем с SQL по запросу (предварительная версия)
description: В этой статье содержатся сведения, которые могут помочь в устранении проблем с SQL по запросу (предварительной версии).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421198"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Самостоятельное устранение проблем с SQL по запросу (предварительная версия)

Эта статья содержит сведения об устранении наиболее часто встречающихся проблем с SQL по запросу (предварительной версии) в Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>Компонент SQL по запросу неактивен в Synapse Studio

Если Synapse Studio не удается установить подключение к SQL по запросу, вы заметите, что SQL по запросу неактивен или отображается состояние "Не в сети". Как правило, эта проблема возникает в одном из случаев, указанных ниже.

1) Ваша сеть не позволяет связаться с серверной частью Azure Synapse. Чаще всего оказывается, что заблокирован порт 1443. Чтобы программа SQL по запросу работала, разблокируйте этот порт. Работе SQL по запросу также могут помешать другие проблемы. Чтобы получить дополнительные сведения, [ознакомьтесь с полным руководством по устранению проблем](../troubleshoot/troubleshoot-synapse-studio.md).
2) У вас нет разрешений на вход в SQL по запросу. Чтобы получить доступ, один из администраторов рабочей области Azure Synapse должен добавить вас в роль администратора рабочей области или администратора SQL. [Дополнительные сведения см. в полном руководстве по контролю доступа](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>Сбой запроса из-за невозможности открыть файл

Если происходит сбой запроса с сообщением File cannot be opened because it does not exist or it is used by another process (Не удается открыть файл, так как он не существует или используется другим процессом) и вы уверены, что оба файла существуют и не используются другим процессом, это означает, что SQL по запросу не может получить доступ к файлу. Эта проблема обычно возникает из-за того, что у удостоверения Azure Active Directory нет прав доступа к файлу. По умолчанию SQL по запросу пытается получить доступ к файлу, используя удостоверение Azure Active Directory. Чтобы устранить эту проблему, необходимо получить соответствующие права доступа к файлу. Проще всего предоставить себе роль "Участник для данных BLOB-объектов хранилища" в учетной записи хранения, к которой вы пытаетесь выполнить запрос. [Дополнительные сведения см. в полном руководстве по контролю доступа Azure Active Directory к хранилищу](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Сбой выполнения запроса из-за текущих ограничений ресурсов 

Если сбой запроса завершается с сообщением This query cannot be executed due to current resource constraints (Этот запрос нельзя выполнить из-за текущих ограничений ресурсов), это означает, что SQL по запросу не может выполнить его в этот момент из-за ограничений ресурсов: 

- Убедитесь, что используются типы данных допустимого размера. Кроме того, укажите схему для файлов Parquet для столбцов строк, так как по умолчанию у них будет тип VARCHAR(8000). 

- Если запрос предназначен для CSV-файлов, [создайте статистику](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Ознакомьтесь с [рекомендациями по повышению производительности для SQL по запросу](best-practices-sql-on-demand.md), чтобы оптимизировать создание запросов.  

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь со следующими статьями, чтобы узнать больше о том, как использовать SQL по запросу:

- [Запрашивание одного CSV-файла](query-single-csv-file.md)

- [Запрашивание папок и нескольких CSV-файлов](query-folders-multiple-csv-files.md)

- [Запрашивание конкретных файлов](query-specific-files.md)

- [Запрашивание файлов Parquet](query-parquet-files.md)

- [Запрашивание вложенных типов Parquet](query-parquet-nested-types.md)

- [Запрашивание JSON-файлов](query-json-files.md)

- [Создание и использование представлений](create-use-views.md)

- [Создание и использование внешних таблиц](create-use-external-tables.md)

- [Сохранение результатов запроса в хранилище](create-external-table-as-select.md)
