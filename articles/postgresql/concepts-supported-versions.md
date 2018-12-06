---
title: Поддерживаемые версии в службе "База данных Azure для PostgreSQL"
description: Описываются поддерживаемые версии в базе данных Azure для PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/12/2018
ms.openlocfilehash: a03ead5e577b261b99e635addf6f9a98d8f3cadd
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619702"
---
# <a name="supported-postgresql-database-versions"></a>Поддерживаемые версии базы данных PostgreSQL
Корпорация Майкрософт планирует реализовать поддержку ядра PostgreSQL версий n-2 в службе "База данных Azure для PostgreSQL". В Azure будут поддерживаться текущая основная версия (n) и две предыдущие основные версии (–2).

В настоящее время база данных Azure для PostgreSQL поддерживает следующие версии:

## <a name="postgresql-version-105"></a>PostgreSQL версии 10.5
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/10/static/release-10-5.html).

## <a name="postgresql-version-9610"></a>PostgreSQL версии 9.6.10
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.6/static/release-9-6-10.html).

## <a name="postgresql-version-9514"></a>PostgreSQL версии 9.5.14
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.5/static/release-9-5-14.html).

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
База данных Azure для PostgreSQL автоматически управляет установкой исправлений для дополнительных версий. Сейчас обновление основной версии не поддерживается. Например, обновление с версии PostgreSQL 9.5 до PostgreSQL 9.6 не поддерживается. Чтобы выполнить обновление до следующей основной версии, [создайте дамп базы данных ](./howto-migrate-using-dump-and-restore.md) на сервере, который был создан с новой версией ядра.

## <a name="next-steps"></a>Дополнительная информация
Сведения о поддержке различных расширений PostgreSQL см. в [этой статье](concepts-extensions.md).
