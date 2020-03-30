---
title: Отказ в доступе к общедоступной сети - Портал Azure - База данных Azure для MariaDB
description: Узнайте, как настроить "Отказ в доступе к публичной сети" с помощью портала Azure для базы данных Azure для MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375243"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Запретить доступ к публичной сети в базе данных Azure для MariaDB с помощью портала Azure

В этой статье описывается, как можно настроить базу данных Azure для сервера MariaDB, чтобы отказать во всех общедоступных конфигурациях и разрешить только соединения через частные конечные точки для дальнейшего повышения безопасности сети.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Установить Запретить доступ к публичной сети

Выполните следующие действия, чтобы установить сервер MariaDB Deny Public Network Access:

1. На [портале Azure](https://portal.azure.com/)выберите существующую базу данных Azure для сервера MariaDB.

1. На странице сервера MariaDB, под **настройками,** нажмите **безопасности соединения,** чтобы открыть страницу конфигурации безопасности соединения.

1. В отказе в доступе к публичной сети выберите **«Да»,** чтобы отказать в открытом доступе для вашего сервера MariaDB.

    ![База данных Azure для доступа к сети MariaDB Deny](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Чтобы сохранить изменения, нажмите кнопку **Сохранить**.

1. Уведомление подтвердит, что настройка безопасности соединения была успешно включена.

    ![База данных Azure для успеха доступа к сети MariaDB Deny](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [том, как создавать оповещения по метрикам.](howto-alert-metric.md)