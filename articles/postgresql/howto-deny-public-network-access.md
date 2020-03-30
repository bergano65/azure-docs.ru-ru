---
title: Отказать в доступе к публичной сети - Портал Azure - База данных Azure для PostgreS'L - Единый сервер
description: Узнайте, как настроить "Отказ в доступе к публичной сети" с помощью портала Azure для базы данных Azure для единого сервера PostgreS-L
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375126"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Запретить доступ к публичной сети в базе данных Azure для одного сервера PostgreS'L с помощью портала Azure

В этой статье описывается, как можно настроить базу данных Azure для единого сервера PostgreS'L, чтобы отрицать все общедоступные конфигурации и разрешать только соединения через частные конечные точки для дальнейшего повышения безопасности сети.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для одного сервера PostgreS'L](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Установить Запретить доступ к публичной сети

Выполните следующие действия, чтобы установить PostgreS'L Единый сервер Отказать в доступе к публичной сети:

1. На [портале Azure](https://portal.azure.com/)выберите существующую базу данных Azure для единого сервера PostgreS'L.

1. На странице одного сервера PostgreS'L, в **настройках,** нажмите **безопасности соединения,** чтобы открыть страницу конфигурации безопасности соединения.

1. В **deny Public Network Access**выберите **«Да»,** чтобы отказать в открытом доступе для вашего единого сервера PostgreS'L.

    ![База данных Azure для одного сервера PostgreS'L запретить доступ к сети](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Чтобы сохранить изменения, нажмите кнопку **Сохранить**.

1. Уведомление подтвердит, что настройка безопасности соединения была успешно включена.

    ![База данных Azure для одного сервера PostgreS'L откажет в успехе доступа к сети](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [том, как создавать оповещения по метрикам.](howto-alert-on-metric.md)