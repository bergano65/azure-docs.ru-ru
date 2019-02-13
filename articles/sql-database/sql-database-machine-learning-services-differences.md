---
title: Обзор основных отличий Служб машинного обучения (с использованием R) в Базе данных SQL Azure (предварительная версия)
description: Здесь описываются основные различия между Службами машинного обучения Базы данных SQL Azure (с использованием R) и Службами машинного обучения SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: adc303e9b375aaa6f37e9e79ea5434675c75523d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55825031"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Основные различия между Службами машинного обучения в Базе данных SQL Azure и SQL Server

Функциональные возможности Служб машинного обучения (с использованием R) в Базе данных SQL Azure похожи на возможности [Служб машинного обучения SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Ниже приведены некоторые основные различия между ними.

## <a name="language-support"></a>Поддержка языков

SQL Server обеспечивает поддержку R и Python через [платформу расширения](https://docs.microsoft.com/en-us/sql/advanced-analytics/concepts/extensibility-framework). База данных SQL не поддерживает оба языка. Ниже описаны основные различия.

- R — единственный поддерживаемый язык в Базе данных SQL. В настоящее время Python не поддерживается.
- Версия R — 3.4.4.
- Нет необходимости настраивать `external scripts enabled` через `sp_configure`. Когда вы [зарегистрируетесь](sql-database-machine-learning-services-overview.md#signup), машинное обучения будет включено для вашей базы данных SQL.

## <a name="package-management"></a>Управление пакетами

Установка пакетов R и управление ими в Базе данных SQL и SQL Server отличаются, в частности:

- Пакеты R устанавливаются с использованием [sqlmlutils](https://github.com/Microsoft/sqlmlutils) или [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Пакеты не могут выполнять исходящие сетевые вызовы. Это ограничение аналогично [правилам брандмауэра по умолчанию для Служб машинного обучения](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) в SQL Server, но не может быть изменено в Базе данных SQL.
- Пакеты, которые зависят от внешних сред выполнения (например, Java) или которым требуется доступ к API операционной системы для установки или использования, не поддерживаются.

## <a name="resource-governance"></a>управление ресурсами;

Ресурсы R невозможно ограничить с помощью [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) и внешних пулов ресурсов. Ресурсы R представляют собой процент ресурсов Базы данных SQL и зависят от выбранного уровня служб. Дополнительные сведения см. в статье [Модели приобретения Базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

## <a name="security-isolation"></a>Защита путем изоляции

В Базе данных SQL Azure уровень абстракции платформы SQL (SQLPAL) обеспечивает изоляцию для внешних процессов. Такая изоляция обеспечивает дополнительный уровень безопасности для выполнения сценариев R.

## <a name="next-steps"></a>Дополнительная информация

- [Документация по Службам машинного обучения SQL Server](https://docs.microsoft.com/sql/advanced-analytics)
- [Краткое руководство. Использование Служб машинного обучения (с использованием R) в Базе данных SQL Azure (предварительная версия)](sql-database-connect-query-r.md)
- [Руководства по языку R в SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)