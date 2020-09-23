---
title: Запрет доступа к общедоступной сети — портал Azure — база данных Azure для MySQL.
description: Узнайте, как настроить запрет доступа к общедоступной сети с помощью портал Azure для базы данных Azure для MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: eb5c4e4c4dfb73b2f7c9dc9f2629296790790885
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896140"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Запрет доступа к общедоступной сети в базе данных Azure для MySQL с помощью портал Azure

В этой статье описывается, как настроить сервер базы данных Azure для MySQL для запрета всех общедоступных конфигураций и разрешить подключения только через частные конечные точки для дальнейшего повышения безопасности сети.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Установка запрета доступа к общедоступной сети

Выполните следующие действия, чтобы настроить MySQL Server для запрета доступа к общедоступной сети.

1. В [портал Azure](https://portal.azure.com/)выберите существующий сервер базы данных Azure для MySQL.

1. На странице "сервер MySQL" в разделе " **Параметры**" щелкните **Безопасность подключения** , чтобы открыть страницу настройки безопасности подключения.

1. В списке **запретить доступ к общедоступной сети**выберите **Да** , чтобы включить запрет общего доступа для сервера MySQL.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Служба "база данных Azure для MySQL" запрещает доступ к сети":::

1. **Сохраните** изменения.

1. Уведомление подтверждает успешное включение параметра безопасности подключения.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Служба "база данных Azure для MySQL" запрещает доступ к сети":::

## <a name="next-steps"></a>Дальнейшие действия

Узнайте [, как создавать оповещения по метрикам](howto-alert-on-metric.md).