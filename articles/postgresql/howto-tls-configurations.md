---
title: Настройка TLS — портал Azure — "база данных Azure для PostgreSQL — один сервер"
description: Узнайте, как настроить конфигурацию TLS с помощью портал Azure для базы данных Azure для PostgreSQL на одном сервере.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: ed5d46bbb5a520ee28013210ba44045ad59c2cc2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84705710"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Настройка параметров TLS в базе данных Azure для PostgreSQL на одном сервере с помощью портал Azure

В этой статье описывается, как настроить базу данных Azure для PostgreSQL для принудительного применения минимальной версии TLS, разрешенной для подключений, и запретить все соединения с более ранней версией TLS по сравнению с настроенной минимальной версией TLS, таким образом улучшая сетевую безопасность.

Вы можете применить версию TLS для подключения к своей базе данных Azure для PostgreSQL. Теперь у клиентов есть возможность задать минимальную версию TLS для сервера базы данных. Например, если задать для параметра минимальной настройки TLS значение TLS 1,0, сервер разрешит подключения клиентов, использующих TLS 1,0, 1,1 и 1.2. Вместо этого, если задать для минимальной версии TLS значение 1,2 +, вы разрешите подключения только от клиентов, использующих TLS 1,2, и все соединения с TLS 1,0 и TLS 1,1 будут отклонены.

## <a name="prerequisites"></a>Предварительные условия

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Настройка конфигурации TLS для базы данных Azure для PostgreSQL — один сервер

Чтобы задать минимальную версию TLS PostgreSQL, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com/)выберите существующую базу данных Azure для PostgreSQL.

1.  На странице База данных Azure для PostgreSQL — один сервер в разделе **Параметры**щелкните **Безопасность подключения** , чтобы открыть страницу Настройка безопасности подключения.

1. В **минимальной версии TLS**выберите **1,2** , чтобы запретить подключения с использованием протокола tls версии ниже TLS 1,2 для одного сервера PostgreSQL.

    ![Конфигурация TLS для одного сервера базы данных Azure для PostgreSQL](./media/howto-tls-configurations/setting-tls-value.png)

1. Щелкните **Сохранить** , чтобы сохранить изменения.

1. Уведомление подтверждает успешное включение параметра безопасности подключения.

    ![База данных Azure для PostgreSQL — Настройка TLS для одного сервера успешно выполнена](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о [создании оповещений о метриках](howto-alert-on-metric.md)