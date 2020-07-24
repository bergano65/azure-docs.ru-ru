---
title: Поддерживаемые версии — база данных Azure для PostgreSQL — один сервер
description: Описание поддерживаемых основных и вспомогательных версий postgres в базе данных Azure для PostgreSQL-Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 1640395b3a73116c27894a2b3f2b95b8bd5bb2eb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084166"
---
# <a name="supported-postgresql-major-versions"></a>Поддерживаемые основные версии PostgreSQL
Корпорация Майкрософт нацелена на поддержку n-2 версий подсистемы PostgreSQL в базе данных Azure для PostgreSQL-Single Server. В Azure будут поддерживаться текущая основная версия (n) и две предыдущие основные версии (–2).

В настоящее время база данных Azure для PostgreSQL поддерживает следующие основные версии:

## <a name="postgresql-version-11"></a>PostgreSQL версии 11
Текущий дополнительный выпуск — 11,6. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) .

## <a name="postgresql-version-10"></a>PostgreSQL версии 10
Текущий дополнительный выпуск — 10,11. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) .

## <a name="postgresql-version-96"></a>PostgreSQL версии 9,6
Текущий дополнительный выпуск — 9.6.16. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) .

## <a name="postgresql-version-95"></a>PostgreSQL версии 9,5
Текущий дополнительный выпуск — 9.5.20. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) .

## <a name="managing-upgrades"></a>Управление обновлениями
Проект PostgreSQL регулярно выдает дополнительные выпуски для исправления ошибок, о которых сообщили. База данных Azure для PostgreSQL автоматически закрывает серверы с дополнительными выпусками во время ежемесячных развертываний службы. 

Автоматическое обновление на месте для основных версий не поддерживается. Чтобы выполнить обновление до следующей основной версии, можно 
   * Использование [pg_dump и pg_restore](./howto-migrate-using-dump-and-restore.md) для перемещения базы данных на сервер, созданный с помощью новой версии модуля
   * Кроме того, можно выполнить обновление с PostgreSQL 10 до 11 с помощью [Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) .

### <a name="version-syntax"></a>Синтаксис версии
До PostgreSQL версии 10 [Политика управления версиями PostgreSQL](https://www.postgresql.org/support/versioning/) считается обновлением _основного номера версии_ , что приводит к увеличению первого _или_ второго числа. Например, 9,5 на 9,6 считалось обновлением _основной_ версии. Начиная с версии 10, только изменение первого числа считается обновлением основной версии. Например, 10,0 на 10,1 — это _незначительное_ обновление выпуска. Версия 10 до 11 является _основным_ обновлением версии.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о поддерживаемых расширениях PostgreSQL см. [в документе о расширениях](concepts-extensions.md).
