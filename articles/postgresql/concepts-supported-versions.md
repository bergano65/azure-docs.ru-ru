---
title: Поддерживаемые версии — база данных Azure для PostgreSQL — один сервер
description: Описание поддерживаемых основных и вспомогательных версий postgres в базе данных Azure для PostgreSQL-Single Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 72d774b4ced6471ff7b355b2cb43c3c9127b5975
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658525"
---
# <a name="supported-postgresql-major-versions"></a>Поддерживаемые основные версии PostgreSQL

См. Дополнительные сведения о политике [управления версиями базы данных Azure для PostgreSQL](concepts-version-policy.md) .

В настоящее время база данных Azure для PostgreSQL поддерживает следующие основные версии:

## <a name="postgresql-version-11"></a>PostgreSQL версии 11
Текущий дополнительный выпуск — 11,6. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) .

## <a name="postgresql-version-10"></a>PostgreSQL версии 10
Текущий дополнительный выпуск — 10,11. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) .

## <a name="postgresql-version-96"></a>PostgreSQL версии 9,6
Текущий дополнительный выпуск — 9.6.16. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) .

## <a name="postgresql-version-95"></a>PostgreSQL версии 9,5
Текущий дополнительный выпуск — 9.5.20. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) .

> [!NOTE]
> При согласовании с [политикой управления версиями](https://www.postgresql.org/support/versioning/)postgres Community в базе данных Azure для PostgreSQL будет снята версия 9,5 с 11 февраля 2021. Дополнительные сведения и ограничения см. в статье [Политика управления версиями базы данных Azure для PostgreSQL](concepts-version-policy.md) .

## <a name="managing-upgrades"></a>Управление обновлениями
Проект PostgreSQL регулярно выдает дополнительные выпуски для исправления ошибок, о которых сообщили. База данных Azure для PostgreSQL автоматически закрывает серверы с дополнительными выпусками во время ежемесячных развертываний службы. 

Автоматическое обновление на месте для основных версий не поддерживается. Чтобы выполнить обновление до следующей основной версии, можно 
   * Ознакомьтесь с различными методами [обновления основных версий с помощью дампа и восстановления](./how-to-upgrade-using-dump-and-restore.md) .
   * Использование [pg_dump и pg_restore](./howto-migrate-using-dump-and-restore.md) для перемещения базы данных на сервер, созданный с помощью новой версии модуля
   * Кроме того, можно выполнить обновление с PostgreSQL 10 до 11 с помощью [Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) .

### <a name="version-syntax"></a>Синтаксис версии
До PostgreSQL версии 10 [Политика управления версиями PostgreSQL](https://www.postgresql.org/support/versioning/) считается обновлением _основного номера версии_ , что приводит к увеличению первого _или_ второго числа. Например, 9,5 на 9,6 считалось обновлением _основной_ версии. Начиная с версии 10, только изменение первого числа считается обновлением основной версии. Например, 10,0 на 10,1 — это _незначительное_ обновление выпуска. Версия 10 до 11 является _основным_ обновлением версии.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о поддерживаемых расширениях PostgreSQL см. [в документе о расширениях](concepts-extensions.md).
