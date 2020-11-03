---
title: Запрет доступа к общедоступной сети — портал Azure — "база данных Azure для PostgreSQL — один сервер"
description: Узнайте, как настроить запрет доступа к общедоступной сети с помощью портал Azure для базы данных Azure для PostgreSQL одного сервера.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: bd777fe42dc8fa3ec4643fa9607f5ca8b7aac795
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240330"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Запрет доступа к общедоступной сети в базе данных Azure для PostgreSQL с помощью портал Azure

В этой статье описывается, как настроить односерверную базу данных Azure для PostgreSQL, чтобы запретить все общедоступные конфигурации и разрешить подключения только через частные конечные точки для дальнейшего повышения безопасности сети.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [Один сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-deny-public-network-access"></a>Установка запрета доступа к общедоступной сети

Выполните следующие действия, чтобы настроить PostgreSQL Single Server для запрета доступа к общедоступной сети.

1. В [портал Azure](https://portal.azure.com/)выберите существующую базу данных Azure для PostgreSQL Single Server.

1. На странице PostgreSQL Single Server в разделе **Параметры** щелкните **Безопасность подключения** , чтобы открыть страницу Настройка безопасности подключения.

1. В списке **запретить доступ к общедоступной сети** выберите **Да** , чтобы разрешить запретить общий доступ для PostgreSQL одного сервера.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Сервер базы данных Azure для PostgreSQL запрещает доступ к сети":::

1. **Сохраните** изменения.

1. Уведомление подтверждает успешное включение параметра безопасности подключения.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Сервер базы данных Azure для PostgreSQL запрещает доступ к сети":::

## <a name="next-steps"></a>Дальнейшие действия

Узнайте [, как создавать оповещения по метрикам](howto-alert-on-metric.md).
