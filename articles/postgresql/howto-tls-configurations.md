---
title: Настройка TLS — портал Azure — "база данных Azure для PostgreSQL — один сервер"
description: Узнайте, как настроить конфигурацию TLS с помощью портал Azure для базы данных Azure для PostgreSQL на одном сервере.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 049b30689c86881ce89d4fbcd54175ee1ee5497e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375113"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql---single-server-using-azure-portal"></a>Настройка параметров TLS в базе данных Azure для PostgreSQL — один сервер с помощью портал Azure

В этой статье описывается, как настроить базу данных Azure для PostgreSQL-Single Server для принудительного применения минимальной версии TLS для всех входящих подключений, что помогает повысить безопасность сети.

Теперь клиенты могут применять версию TLS для подключения к базе данных Azure для PostgreSQL-Single Server. Теперь у клиентов есть возможность задать минимальную версию TLS для сервера базы данных. Например, если задать для параметра минимальной настройки TLS значение TLS 1,0, сервер разрешит подключения клиентов, использующих TLS 1,0, 1,1 и 1.2. Кроме того, если задать для этого параметра значение 1,2, то разрешаются подключения только от клиентов, использующих TLS 1,2, а все соединения с TLS 1,0 и TLS 1,1 будут отклонены.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Настройка конфигурации TLS для базы данных Azure для PostgreSQL — один сервер

Чтобы задать PostgreSQL минимальную версию TLS для одного сервера, выполните следующие действия:

1. В [портал Azure](https://portal.azure.com/)выберите существующую базу данных Azure для PostgreSQL-Single Server.

1.  На странице База данных Azure для PostgreSQL — один сервер в разделе **Параметры**щелкните **Безопасность подключения** , чтобы открыть страницу Настройка безопасности подключения.

1. В **минимальной версии TLS**выберите **1,2** , чтобы запретить подключения с использованием протокола tls версии ниже TLS 1,2 для одного сервера PostgreSQL.

    ![Конфигурация TLS для одного сервера базы данных Azure для PostgreSQL](./media/howto-tls-configurations/setting-tls-value.png)

1. Чтобы сохранить изменения, нажмите кнопку **Сохранить**.

1. Уведомление подтверждает успешное включение параметра безопасности подключения.

    ![База данных Azure для PostgreSQL — Настройка TLS для одного сервера успешно выполнена](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Следующие шаги

Узнайте [, как создавать оповещения по метрикам](howto-alert-on-metric.md).
