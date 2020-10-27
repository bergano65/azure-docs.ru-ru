---
title: Поддерживаемые версии — база данных Azure для PostgreSQL — гибкий сервер
description: Описание поддерживаемых основных и вспомогательных версий PostgreSQL в базе данных Azure для PostgreSQL-гибкого сервера.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: b139e2f14379e0cd52ca72c347d49bcb4512c473
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542086"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Поддерживаемые основные версии PostgreSQL в базе данных Azure для PostgreSQL-гибкого сервера

> [!IMPORTANT]
> Гибкий сервер Базы данных Azure для PostgreSQL предоставляется в режиме предварительной версии

База данных Azure для PostgreSQL — гибкий сервер в настоящее время поддерживает следующие основные версии:

## <a name="postgresql-version-12"></a>PostgreSQL версии 12

Текущий дополнительный выпуск — 12,4. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-4.html) .

## <a name="postgresql-version-11"></a>PostgreSQL версии 11

Текущий дополнительный выпуск — 11,9. Дополнительные сведения об улучшениях и исправлениях в этом дополнительном выпуске см. в [документации по PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-9.html) .

## <a name="postgresql-version-10-and-older"></a>PostgreSQL версии 10 и более ранних версий

Мы не поддерживаем PostgreSQL версии 10 и более ранних версий для базы данных Azure для PostgreSQL-гибкого сервера. Если требуются более старые версии, используйте вариант развертывания на [одном сервере](../concepts-supported-versions.md) .

## <a name="managing-upgrades"></a>Управление обновлениями

Проект PostgreSQL регулярно выдает дополнительные выпуски для исправления ошибок, о которых сообщили. База данных Azure для PostgreSQL автоматически закрывает серверы с дополнительными выпусками во время ежемесячных развертываний службы.

Автоматизация обновления основной версии пока не поддерживается. Например, в настоящее время автоматическое обновление с PostgreSQL 11 до PostgreSQL 12 отсутствует.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->