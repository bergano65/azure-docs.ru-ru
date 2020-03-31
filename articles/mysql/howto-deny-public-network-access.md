---
title: Отказ в доступе к общедоступной сети - Портал Azure - База данных Azure для MyS'L
description: Узнайте, как настроить "Отрицать доступ к публичной сети" с помощью портала Azure для базы данных Azure для MyS'L
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374957"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Запретить доступ к публичной сети в базе данных Azure для MyS'L с помощью портала Azure

В этой статье описывается, как можно настроить базу данных Azure для сервера MyS'L, чтобы отказать во всех общедоступных конфигурациях и разрешить только соединения через частные конечные точки для дальнейшего повышения безопасности сети.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для MyS'L](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Установить Запретить доступ к публичной сети

Выполните следующие действия, чтобы установить сервер MyS'L Deny Public Network Access:

1. На [портале Azure](https://portal.azure.com/)выберите существующую базу данных Azure для сервера MyS'L.

1. На странице сервера MyS'L, в **настройках,** нажмите **кнопку безопасности соединения,** чтобы открыть страницу конфигурации безопасности соединения.

1. В **отказать общественному доступу к сети**, выберите **Да,** чтобы включить открытый доступ для вашего сервера MyS'L.

    ![База данных Azure для доступа к сети MyS'L отрицает](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Чтобы сохранить изменения, нажмите кнопку **Сохранить**.

1. Уведомление подтвердит, что настройка безопасности соединения была успешно включена.

    ![База данных Azure для успешного доступа к сети MyS'L](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [том, как создавать оповещения по метрикам.](howto-alert-on-metric.md)