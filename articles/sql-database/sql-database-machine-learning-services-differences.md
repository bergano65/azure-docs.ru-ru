---
title: Основные отличия для Службы машинного обучения (Предварительная версия)
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
ms.date: 03/01/2019
ms.openlocfilehash: 1397f5d81ddf63740d733111b965a0517a2b917f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827469"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Основные различия между Службы машинного обучения в базе данных SQL Azure (Предварительная версия) и SQL Server

Функции Службы машинного обучения базы данных SQL Azure (с R) в (Предварительная версия) похожи на [SQL Server службы машинного обучения](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Ниже приведены некоторые ключевые отличия.

> [!IMPORTANT]
> Сейчас службы машинного обучения в Базе данных SQL Azure находятся в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Поддержка языков

SQL Server обеспечивает поддержку R и Python через [платформу расширения](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). База данных SQL не поддерживает оба языка. Ниже описаны основные различия.

- R — единственный поддерживаемый язык в Базе данных SQL. В настоящее время Python не поддерживается.
- Версия R — 3.4.4.
- Нет необходимости настраивать `external scripts enabled` через `sp_configure`. Когда вы [зарегистрируетесь](sql-database-machine-learning-services-overview.md#signup), машинное обучения будет включено для вашей базы данных SQL.

## <a name="package-management"></a>Управление пакетами

Установка пакетов R и управление ими в Базе данных SQL и SQL Server отличаются, в частности:

- Пакеты R устанавливаются с использованием [sqlmlutils](https://github.com/Microsoft/sqlmlutils) или [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Пакеты не могут выполнять исходящие сетевые вызовы. Это ограничение аналогично [правилам брандмауэра по умолчанию для службы машинного обучения](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) в SQL Server, но не может быть изменено в базе данных SQL.
- Пакеты, которые зависят от внешних сред выполнения (например, Java) или которым требуется доступ к API операционной системы для установки или использования, не поддерживаются.

## <a name="writing-to-a-temporary-table"></a>Запись во временную таблицу

Если вы используете RODBC в базе данных SQL Azure, то не сможете выполнить запись во временную таблицу независимо от того, создана ли она внутри или вне сеанса `sp_execute_external_script`. Обходной путь заключается в использовании [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) и [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (with overwrite = false и Append = "Rows") для записи в глобальную временную таблицу, созданную перед запросом `sp_execute_external_script`.

## <a name="resource-governance"></a>управление ресурсами;

Ресурсы R невозможно ограничить с помощью [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) и внешних пулов ресурсов.

В общедоступной предварительной версии ресурсы R задаются максимум 20% ресурсов базы данных SQL и зависят от выбранного уровня служб. Дополнительные сведения см. в статье [Модели приобретения Базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Ошибка "недостаточно памяти"

Если объем доступной памяти для R недостаточен, вы получите сообщение об ошибке. Распространенные сообщения об ошибках:

- Не удается связаться со средой выполнения для скрипта "R" для идентификатора запроса: * * * * * * *. Проверьте требования среды выполнения "R".
- Произошла ошибка сценария R во время выполнения "sp_execute_external_script" с HRESULT 0x80004004. ... произошла ошибка внешнего скрипта: ".. не удалось выделить память (0 МБ) в функции C "R_AllocStringBuffer" "
- Произошла ошибка внешнего скрипта: ошибка: не удается выделить вектор размера.

Использование памяти зависит от того, сколько используется в скриптах R и сколько параллельных запросов выполняется. Если вы получаете приведенные выше ошибки, для решения этой проблемы можно масштабировать базу данных до более высокого уровня служб.

## <a name="next-steps"></a>Дальнейшие действия

- См. Обзор, [службы машинного обучения базы данных SQL Azure с R (Предварительная версия)](sql-database-machine-learning-services-overview.md).
- Сведения о том, как использовать R для запроса базы данных SQL Azure Службы машинного обучения (Предварительная версия), см. в [руководстве по быстрому](sql-database-connect-query-r.md)запуску.
- Чтобы приступить к работе с некоторыми простыми скриптами R, ознакомьтесь со статьей [Создание и выполнение простых скриптов r в базе данных SQL Azure службы машинного обучения (Предварительная версия)](sql-database-quickstart-r-create-script.md).
