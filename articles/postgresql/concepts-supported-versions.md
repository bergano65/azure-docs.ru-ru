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
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630597"
---
# <a name="supported-postgresql-database-versions"></a>Поддерживаемые версии базы данных PostgreSQL
Корпорация Майкрософт планирует реализовать поддержку ядра PostgreSQ версии n-2 в базе данных Azure для службы PostgreSQL (выпущенная основная версия (n) и две предыдущие основные версии (-2)).

В настоящее время база данных Azure для PostgreSQL поддерживает следующие версии:

## <a name="postgresql-version-104"></a>PostgreSQL версии 10.4
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/10/static/release-10-4.html).

## <a name="postgresql-version-969"></a>PostgreSQL версии 9.6.9
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html).

## <a name="postgresql-version-9513"></a>PostgreSQL версии 9.5.13
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html).

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
База данных Azure для PostgreSQL автоматически управляет установкой исправлений для обновления дополнительного номера версии. Сейчас обновление основной версии не поддерживается. Например, обновление с версии PostgreSQL 9.5 до PostgreSQL 9.6 не поддерживается. Чтобы выполнить обновление до следующей основной версии, [восстановите дамп](./howto-migrate-using-dump-and-restore.md) на сервере, который был создан с новой версией ядра.

## <a name="next-steps"></a>Дополнительная информация
Сведения о поддержке различных расширений PostgreSQL см. в [этой статье](concepts-extensions.md).
