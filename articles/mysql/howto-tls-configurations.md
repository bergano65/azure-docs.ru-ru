---
title: Настройка TLS — портал Azure — база данных Azure для MySQL
description: Узнайте, как настроить конфигурацию TLS с помощью портал Azure для базы данных Azure для MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: d94e589a19b29e68883c0217b62b883f9d026789
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902739"
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

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Конфигурация TLS базы данных Azure для MySQL":::

1. **Сохраните** изменения.

1. Уведомление подтверждает успешное включение параметра безопасности подключения.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Конфигурация TLS базы данных Azure для MySQL":::

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о [создании оповещений о метриках](howto-alert-on-metric.md)