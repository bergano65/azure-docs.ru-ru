---
title: Поддерживаемые версии в базе данных Azure для PostgreSQL — один сервер
description: Описание поддерживаемых версий в базе данных Azure для PostgreSQL-Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551356"
---
# <a name="supported-postgresql-database-versions"></a>Поддерживаемые версии базы данных PostgreSQL
Корпорация Майкрософт нацелена на поддержку n-2 версий подсистемы PostgreSQL в базе данных Azure для PostgreSQL-Single Server. В Azure будут поддерживаться текущая основная версия (n) и две предыдущие основные версии (–2).

В настоящее время база данных Azure для PostgreSQL поддерживает следующие версии:

## <a name="postgresql-version-112"></a>PostgreSQL версии 11,2
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/11/static/release-11-2.html).

## <a name="postgresql-version-107"></a>PostgreSQL версии 10,7
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/10/static/release-10-7.html).

## <a name="postgresql-version-9612"></a>PostgreSQL версия 9.6.12
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html).

## <a name="postgresql-version-9516"></a>PostgreSQL версия 9.5.16
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html).

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
База данных Azure для PostgreSQL автоматически управляет установкой исправлений для дополнительных версий. Сейчас обновление основной версии не поддерживается. Например, обновление с версии PostgreSQL 9.5 до PostgreSQL 9.6 не поддерживается. Чтобы выполнить обновление до следующей основной версии, [создайте дамп базы данных ](./howto-migrate-using-dump-and-restore.md) на сервере, который был создан с новой версией ядра.

> Обратите внимание, что до PostgreSQL версии 10 [Политика управления версиями PostgreSQL](https://www.postgresql.org/support/versioning/) считала обновлением _основной версии_ до увеличения первого _или_ второго числа (например, 9,5 до 9,6 считалось обновлением _основной_ версии).
> Начиная с версии 10, только изменение первого числа считается обновлением основной версии (например, 10,0 на 10,1 — это _дополнительное_ обновление версии, а 10 – 11 — обновление _основной_ версии).

## <a name="next-steps"></a>Следующие шаги
Сведения о поддержке различных расширений PostgreSQL см. в [этой статье](concepts-extensions.md).
