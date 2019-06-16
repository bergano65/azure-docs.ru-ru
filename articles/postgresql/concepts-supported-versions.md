---
title: 'Поддерживаемые версии в базе данных Azure для PostgreSQL: один сервер'
description: 'Описываются поддерживаемые версии в базе данных Azure для PostgreSQL: один сервер.'
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 0fc4eb90d9bed3877b4618fd839d9e8bf1608718
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063738"
---
# <a name="supported-postgresql-database-versions"></a>Поддерживаемые версии базы данных PostgreSQL
Корпорация Майкрософт стремится поддерживать n-2 версии ядра PostgreSQL в базе данных Azure для PostgreSQL: один сервер. В Azure будут поддерживаться текущая основная версия (n) и две предыдущие основные версии (–2).

В настоящее время база данных Azure для PostgreSQL поддерживает следующие версии:

## <a name="postgresql-version-112"></a>Версия PostgreSQL 11.2
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/11/static/release-11-2.html).

>[!NOTE]
> PostgreSQL версии 11 доступна в предварительной версии. Поддержка создания с помощью портала Azure будет развертываться и еще недоступен в вашем регионе. Можно использовать [Azure CLI](quickstart-create-server-database-azure-cli.md) создание сервера Postgres 11 в любом регионе. Например, `az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11`.

## <a name="postgresql-version-107"></a>Версия PostgreSQL 10.7
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/10/static/release-10-7.html).

## <a name="postgresql-version-9612"></a>Версия PostgreSQL 9.6.12
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html).

## <a name="postgresql-version-9516"></a>Версия PostgreSQL 9.5.16
Сведения об улучшениях и исправлениях в этой дополнительной версии PostgreSQL см. в [документации по этому решению](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html).

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
База данных Azure для PostgreSQL автоматически управляет установкой исправлений для дополнительных версий. Сейчас обновление основной версии не поддерживается. Например, обновление с версии PostgreSQL 9.5 до PostgreSQL 9.6 не поддерживается. Чтобы выполнить обновление до следующей основной версии, [создайте дамп базы данных ](./howto-migrate-using-dump-and-restore.md) на сервере, который был создан с новой версией ядра.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о поддержке различных расширений PostgreSQL см. в [этой статье](concepts-extensions.md).
