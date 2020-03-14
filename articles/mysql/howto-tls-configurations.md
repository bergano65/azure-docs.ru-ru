---
title: Настройка TLS — портал Azure — база данных Azure для MySQL
description: Узнайте, как настроить конфигурацию TLS с помощью портал Azure для базы данных Azure для MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 495fea21c4fca12cd1ec6c1ea45105178d72fe1b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375295"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Настройка параметров TLS в базе данных Azure для MySQL с помощью портал Azure

В этой статье описывается, как настроить сервер базы данных Azure для MySQL на принудительное использование подключений для минимальной версии TLS, чтобы пропускать все подключения с более низкой версией TLS, повышая таким образом сетевую безопасность.

Теперь клиенты могут применять версию TLS для подключения к базе данных Azure для MySQL. Теперь у клиентов есть возможность задать минимальную версию TLS для сервера базы данных. Например, если установить для минимальной версии TLS значение 1,0, вы должны разрешить клиентам подключаться с помощью TLS 1.0, 1.1 и 1,2. Кроме того, значение 1,2 означает, что вы разрешаете клиентам подключаться с помощью TLS 1,2, а все входящие подключения с TLS 1,0 и TLS 1,1 будут отклонены.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Настройка конфигурации TLS для базы данных Azure для MySQL

Чтобы задать минимальную версию TLS сервера MySQL, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com/)выберите существующий сервер базы данных Azure для MySQL.

1. На странице "сервер MySQL" в разделе " **Параметры**" щелкните **Безопасность подключения** , чтобы открыть страницу настройки безопасности подключения.

1. В **минимальной версии TLS**выберите **1,2** , чтобы запретить подключения с использованием протокола tls версии ниже TLS 1,2 для сервера MySQL.

    ![Конфигурация TLS базы данных Azure для MySQL](./media/howto-tls-configurations/setting-tls-value.png)

1. Чтобы сохранить изменения, нажмите кнопку **Сохранить**.

1. Уведомление подтверждает успешное включение параметра безопасности подключения.

    ![Настройка TLS для базы данных Azure для MySQL](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Следующие шаги

Узнайте [, как создавать оповещения по метрикам](howto-alert-on-metric.md).
