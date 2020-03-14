---
title: Настройка TLS — портал Azure — база данных Azure для MariaDB
description: Узнайте, как настроить конфигурацию TLS с помощью портал Azure для базы данных Azure для MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 063fc82131a66012c7329f590c0fdf2ce4d4da79
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375009"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Настройка параметров TLS в базе данных Azure для MariaDB с помощью портал Azure

В этой статье описывается, как настроить сервер базы данных Azure для MariaDB на принудительное использование подключений для минимальной версии TLS, чтобы пропускать все подключения с более низкой версией TLS, повышая таким образом сетевую безопасность.

Теперь клиенты могут применять версию TLS для подключения к базе данных Azure для MariaDB, настроив минимальную версию TLS для сервера базы данных. Например, если задать для параметра минимальной настройки TLS значение TLS 1,0, сервер разрешит подключения клиентов, использующих TLS 1,0, 1,1 и 1.2. Кроме того, если задать для этого параметра значение 1,2, то разрешаются подключения только от клиентов, использующих TLS 1,2, а все соединения с TLS 1,0 и TLS 1,1 будут отклонены.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Настройка конфигурации TLS для базы данных Azure для MariaDB

Выполните следующие действия, чтобы задать минимальную версию TLS сервера MariaDB:

1. В [портал Azure](https://portal.azure.com/)выберите существующий сервер базы данных Azure для MariaDB.

1. На странице сервер MariaDB в разделе **Параметры**щелкните **Безопасность подключения** , чтобы открыть страницу Настройка безопасности подключения.

1. В **минимальной версии TLS**выберите **1,2** , чтобы запретить подключения с использованием протокола tls версии ниже TLS 1,2 для сервера MariaDB.

    ![Конфигурация TLS базы данных Azure для MariaDB](./media/howto-tls-configurations/tls-configurations.png)

1. Чтобы сохранить изменения, нажмите кнопку **Сохранить**.

1. Уведомление подтверждает успешное включение параметра безопасности подключения.

    ![Настройка Azure Database для MariaDB TLS выполнена](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Следующие шаги

Узнайте [, как создавать оповещения по метрикам](howto-alert-metric.md).
