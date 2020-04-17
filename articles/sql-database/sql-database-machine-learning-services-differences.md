---
title: Основные различия для служб машинного обучения (предварительный просмотр)
description: Здесь описываются основные различия между Службами машинного обучения Базы данных SQL Azure (с использованием R) и Службами машинного обучения SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453155"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Основные различия между службами машинного обучения в базе данных Azure S'L (предварительный просмотр) и сервере S'L

Функциональность служб машинного обучения баз данных Azure S'L (с R) в (предварительном просмотре) аналогична [службам машинного обучения сервера S'L.](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) Ниже приведены некоторые ключевые различия.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Поддержка языков

SQL Server обеспечивает поддержку R и Python через [платформу расширения](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). База данных SQL не поддерживает оба языка. Ниже описаны основные различия.

- R — единственный поддерживаемый язык в Базе данных SQL. В настоящее время Python не поддерживается.
- Версия R — 3.4.4.
- Нет необходимости настраивать `external scripts enabled` через `sp_configure`.

## <a name="package-management"></a>Управление пакетами

Установка пакетов R и управление ими в Базе данных SQL и SQL Server отличаются, в частности:

- Пакеты R устанавливаются с использованием [sqlmlutils](https://github.com/Microsoft/sqlmlutils) или [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Пакеты не могут выполнять исходящие сетевые вызовы. Это ограничение аналогично [правилам брандмауэра по умолчанию для служб машинного обучения](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) в сервере S'L, но не может быть изменено в базе данных S'L.
- Пакеты, которые зависят от внешних сред выполнения (например, Java) или которым требуется доступ к API операционной системы для установки или использования, не поддерживаются.

## <a name="writing-to-a-temporary-table"></a>Запись на временную таблицу

Если вы используете RODBC в базе данных Azure S'L, то вы не можете написать `sp_execute_external_script` на временную таблицу, независимо от того, создана ли она внутри или за пределами сеанса. Решение заключается в использовании [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) и [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (с перезаписьми и приложениями) для записи `sp_execute_external_script` в глобальную временную таблицу, созданную до запроса.

## <a name="resource-governance"></a>Управление ресурсами

Ресурсы R невозможно ограничить с помощью [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) и внешних пулов ресурсов.

Во время публичного предварительного просмотра ресурсы R устанавливаются максимум на 20% ресурсов базы данных S'L и зависят от уровня обслуживания, который вы выберете. Дополнительные сведения см. в статье [Модели приобретения Базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Недостаточная ошибка памяти

Если для R не хватает памяти, вы получите сообщение об ошибке. Распространенными сообщениями об ошибках являются:

- Невозможно связаться с временем выполнения для скрипта 'R' для идентификатора запроса: Пожалуйста, проверьте требования времени выполнения 'R'
- Ошибка скрипта 'R' произошла во время выполнения "sp_execute_external_script" с HRESULT 0x80004004. ... произошла внешняя ошибка скрипта: ". не может выделить память (0 Мб) в функции C 'R_AllocStringBuffer'"
- Произошла внешняя ошибка скрипта: Ошибка: не удается выделить вектор размера.

Использование памяти зависит от того, сколько используется в ваших R-скриптов и количество параллельных запросов выполняется. Если вы получили вышеуказанные ошибки, можно масштабировать базу данных до более высокого уровня обслуживания, чтобы решить эту проблему.

## <a name="next-steps"></a>Следующие шаги

- Смотрите обзор, [Azure S'L Службы машинного обучения с R (предварительный просмотр)](sql-database-machine-learning-services-overview.md).
- Чтобы узнать, как использовать R для запроса служб машинного [Quickstart guide](sql-database-connect-query-r.md)обучения базы данных Azure S'L (предварительный просмотр), см.
- Чтобы начать работу с помощью некоторых простых R-скриптов, [см. Создать и запустить простые R-скрипты в службах машинного обучения базы данных Azure S'L (предварительный просмотр).](sql-database-quickstart-r-create-script.md)
