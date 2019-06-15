---
title: Основные отличия для службы обучения машины базы данных SQL Azure (Предварительная версия)
description: Здесь описываются основные различия между Службами машинного обучения Базы данных SQL Azure (с использованием R) и Службами машинного обучения SQL Server.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 92785015a1ce122b8301b56fa62d122c8d95180c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725051"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Основные различия между SQL Server и служб машинного обучения, в базе данных SQL Azure (Предварительная версия)

Функциональные возможности SQL базы данных служб машинного обучения Azure (с помощью R) (Предварительная версия) аналогичен [службы машинного обучения SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Ниже приведены некоторые ключевые различия.

> [!IMPORTANT]
> Сейчас службы машинного обучения в базе данных SQL Azure находятся в общедоступной предварительной версии.
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
- Пакеты не могут выполнять исходящие сетевые вызовы. Это ограничение аналогично [правила брандмауэра по умолчанию для служб машинного обучения](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) в SQL Server, но не может быть изменено в базе данных SQL.
- Пакеты, которые зависят от внешних сред выполнения (например, Java) или которым требуется доступ к API операционной системы для установки или использования, не поддерживаются.

## <a name="resource-governance"></a>управление ресурсами;

Ресурсы R невозможно ограничить с помощью [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) и внешних пулов ресурсов.

В общедоступной предварительной версии R-ресурсов, присваивается более 20% ресурсов базы данных SQL и зависит от выберите иной уровень обслуживания. Дополнительные сведения см. в статье [Модели приобретения Базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Ошибка нехватки памяти

Если недостаточно памяти для R, вы получите сообщение об ошибке. Ниже приведены распространенные сообщения об ошибках.

- Не удается установить связь со средой выполнения для скрипта «R» для ИД запроса: ***. Проверьте требования среды выполнения «R»
- При выполнении «sp_execute_external_script» с HRESULT 0x80004004 произошла ошибка скрипта «R». ... Произошла ошибка во внешнем скрипте: «.. не удалось выделить память (0 МБ) в функции C «R_AllocStringBuffer»»
- Произошла ошибка во внешнем скрипте: Ошибка: не удается выделить размер вектора.

Использование зависит от объема памяти используется в сценарии R и номер выполнения параллельных запросов. При появлении ошибки выше, вы можете масштабировать базу данных до более высокого уровня службы, чтобы устранить эту проблему.

## <a name="next-steps"></a>Дальнейшие действия

- См. в разделе «Обзор», [SQL базы данных служб машинного обучения Azure с помощью R (Предварительная версия)](sql-database-machine-learning-services-overview.md).
- Чтобы узнать, как использовать R для запроса SQL базы данных служб машинного обучения Azure (Предварительная версия), см. в разделе [краткого руководства по](sql-database-connect-query-r.md).
- Чтобы начать работу с некоторые простые скрипты R, см. в разделе [Создание и выполнение простых сценариев R в SQL базы данных служб машинного обучения Azure (Предварительная версия)](sql-database-quickstart-r-create-script.md).
