---
title: Настройка TLS — портал Azure — база данных Azure для MySQL
description: Узнайте, как настроить конфигурацию TLS с помощью портал Azure для базы данных Azure для MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 46eaa6a3b97967da9c4743d0cf1f6edc8f90b1ce
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119790"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Настройка параметров TLS в базе данных Azure для MySQL с помощью портал Azure

В этой статье описывается, как настроить сервер базы данных Azure для MySQL для применения минимальной версии TLS, разрешенной для подключений, чтобы пропускать и отклонять все соединения с более ранней версией TLS по сравнению с настроенной минимальной версией TLS, тем самым улучшая сетевую безопасность.

Вы можете применить версию TLS для подключения к своей базе данных Azure для MySQL. Теперь у клиентов есть возможность задать минимальную версию TLS для сервера базы данных. Например, если установить для минимальной версии TLS значение 1,0, вы должны разрешить клиентам подключаться с помощью TLS 1.0, 1.1 и 1,2. Кроме того, если задать для этого параметра значение 1,2, то разрешить только клиенты, подключающиеся по протоколу TLS 1.2 +, а все входящие подключения с TLS 1,0 и TLS 1,1 будут отклонены.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

* [База данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Настройка конфигурации TLS для базы данных Azure для MySQL

Чтобы задать минимальную версию TLS сервера MySQL, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com/)выберите существующий сервер базы данных Azure для MySQL.

1. На странице "сервер MySQL" в разделе " **Параметры**" щелкните **Безопасность подключения** , чтобы открыть страницу настройки безопасности подключения.

1. В **минимальной версии TLS**выберите **1,2** , чтобы запретить подключения с использованием протокола tls версии ниже TLS 1,2 для сервера MySQL.

    ![Конфигурация TLS базы данных Azure для MySQL](./media/howto-tls-configurations/setting-tls-value.png)

1. Щелкните **Сохранить** , чтобы сохранить изменения.

1. Уведомление подтверждает успешное включение параметра безопасности подключения.

    ![Настройка TLS для базы данных Azure для MySQL](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [создании оповещений о метриках](howto-alert-on-metric.md)