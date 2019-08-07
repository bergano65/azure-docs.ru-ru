---
title: Поддерживаемые версии в базе данных Azure для PostgreSQL — один сервер
description: Описание поддерживаемых версий в базе данных Azure для PostgreSQL-Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b77fd082be43b8cbdedf7cbe5875a8931eb0474a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837891"
---
# <a name="supported-postgresql-database-versions"></a>Поддерживаемые версии базы данных PostgreSQL
Корпорация Майкрософт нацелена на поддержку n-2 версий подсистемы PostgreSQL в базе данных Azure для PostgreSQL-Single Server. В Azure будут поддерживаться текущая основная версия (n) и две предыдущие основные версии (–2).

В настоящее время база данных Azure для PostgreSQL поддерживает следующие основные версии:

## <a name="postgresql-version-11"></a>PostgreSQL версии 11
Текущая дополнительная версия — 11,4. Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/11/static/release-11-4.html).

## <a name="postgresql-version-10"></a>PostgreSQL версии 10
Текущая дополнительная версия — 10,9. Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/10/static/release-10-9.html).

## <a name="postgresql-version-96"></a>PostgreSQL версии 9,6
Текущая дополнительная версия — 9.6.14. Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.6/static/release-9-6-14.html).

## <a name="postgresql-version-95"></a>PostgreSQL версии 9,5
Текущая дополнительная версия — 9.5.18. Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.5/static/release-9-5-18.html).

## <a name="managing-upgrades"></a>Управление обновлениями
База данных Azure для PostgreSQL автоматически управляет дополнительными обновлениями версий. 

Автоматическое обновление основных версий не поддерживается. Например, автоматическое обновление с PostgreSQL 9,5 до PostgreSQL 9,6 не предусмотрено. Чтобы выполнить обновление до следующей основной версии, создайте [дамп базы данных и](./howto-migrate-using-dump-and-restore.md) восстановите его на сервере, который был создан с помощью новой версии модуля.

### <a name="version-syntax"></a>Синтаксис версии
До PostgreSQL версии 10 [Политика управления версиями PostgreSQL](https://www.postgresql.org/support/versioning/) считается обновлением _основного номера версии_ , что приводит к увеличению первого _или_ второго числа. Например, 9,5 на 9,6 считалось обновлением _основной_ версии. Начиная с версии 10, только изменение первого числа считается обновлением основной версии. Например, 10,0 на 10,1 — это _дополнительное_ обновление версии. Версия 10 до 11 является _основным_ обновлением версии.

## <a name="next-steps"></a>Следующие шаги
Сведения о поддерживаемых расширениях PostgreSQL см. [в документе](concepts-extensions.md)о расширениях.
