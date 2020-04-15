---
title: Журналы аудита доступа - Портал Azure - База данных Azure для MyS'L
description: В этой статье описывается, как настроить и получить доступ к журналам аудита в базе данных Azure для MyS'L с портала Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: fcd436f87b24d8cdc246d2f1d33a49311e72d46b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380305"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Настройка и доступ к журналам аудита для базы данных Azure для MyS'L на портале Azure

Вы можете настроить [базу данных Azure для журналов аудита MyS'L](concepts-audit-logs.md) и диагностических настроек с портала Azure.

> [!IMPORTANT]
> Функциональность журнала аудита в настоящее время находится в предварительном просмотре.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [База данных Azure для сервера MyS'L](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Настройка журналов аудита

Включить и настроить журнал аудита.

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Выберите сервер базы данных Azure для MySQL.

1. В разделе **Настройки** в боковой панели выберите **параметры сервера.**
    ![Параметры сервера](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Обновление параметра **audit_log_enabled** ON.
    ![Включить журналы аудита](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Выберите [типы событий,](concepts-audit-logs.md#configure-audit-logging) которые должны быть зарегистрированы путем обновления **параметра audit_log_events.**
    ![События журнала аудита](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Добавьте всех пользователей MyS'L, которые **audit_log_exclude_users** будут исключены из журнала, обновив audit_log_exclude_users параметр. Укажите пользователям, предоставив их имя пользователя MyS'L.
    ![Журнал аудита исключает пользователей](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. После изменения параметров нажмите кнопку **Сохранить**. Также вы можете нажать кнопку **Отменить**, чтобы отменить изменения.
    ![Сохранить](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Настройка журналов диагностики

1. В разделе **Мониторинг** в боковой панели выберите **Настройки Диагностики.**

1. Нажмите на кнопку !["Добавить диагностическую настройку" Добавить диагностическую настройку](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Укажите имя диагностической настройки.

1. Укажите, какие данные погружаются для отправки журналов аудита (учетная запись хранения, концентратор событий и/или рабочее пространство log Analytics).

1. Выберите "MySqlAuditLogLogs" в качестве типа журнала.
![Настройка диагностической настройки](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. После настройки приемников данных для сравщения журналов аудита можно нажать кнопку **«Сохранение».**
![Сохранение диагностической настройки](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Получите доступ к журналам аудита, исследуя их в настроенных данных. Появление журналов может занять до 10 минут.

## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о [журналах аудита](concepts-audit-logs.md) в базе данных Azure для MyS'L
- Узнайте, как настроить журналы аудита в [Azure CLI](howto-configure-audit-logs-cli.md)