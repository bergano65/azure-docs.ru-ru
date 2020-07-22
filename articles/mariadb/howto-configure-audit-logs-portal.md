---
title: Доступ к журналам аудита — портал Azure — база данных Azure для MariaDB
description: В этой статье описывается, как настроить и получить доступ к журналам аудита в базе данных Azure для MariaDB из портал Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: 30ca62305c325943e17ee574873cfbbbaee97f77
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113043"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Настройка и доступ к журналам аудита в портал Azure

В портал Azure можно настроить [журналы аудита базы данных Azure для MariaDB](concepts-audit-logs.md) и параметры диагностики.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [База данных Azure для сервера MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Настройка ведения журнала аудита

>[!IMPORTANT]
> Рекомендуется регистрировать только те типы событий и пользователей, которые необходимы для целей аудита, чтобы гарантировать, что производительность сервера не окажется сильной.

Включение и Настройка ведения журнала аудита.

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Выберите сервер Базы данных Azure для MariaDB.

1. В разделе **Параметры** на боковой панели выберите **Параметры сервера**.
    ![Параметры сервера](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Обновите параметр **audit_log_enabled** в значение ON.
    ![Включить журналы аудита](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Выберите [типы событий](concepts-audit-logs.md#configure-audit-logging) для записи, обновив параметр **audit_log_events** .
    ![События журнала аудита](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Добавьте всех пользователей MariaDB, которые будут исключены из ведения журнала, путем обновления параметра **audit_log_exclude_users** . Укажите пользователей, указав имя пользователя MariaDB.
    ![Исключить пользователей из журнала аудита](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. После изменения параметров нажмите кнопку **Сохранить**. Также вы можете нажать кнопку **Отменить**, чтобы отменить изменения.
    ![Сохранить](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Настройка журналов диагностики

1. В разделе **мониторинг** на боковой панели выберите **параметры диагностики**.

1. Щелкните "+ добавить параметр диагностики" ![ Добавить параметр диагностики](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Укажите имя параметра диагностики.

1. Укажите приемники данных для отправки журналов аудита (учетную запись хранения, концентратор событий и (или) рабочую область Log Analytics).

1. Выберите "Мисклаудитлогс" в качестве типа журнала.
![Настройка параметра диагностики](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. После настройки приемников данных для передачи журналов аудита в можно нажать кнопку **сохранить**.
![Сохранить параметр диагностики](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Получите доступ к журналам аудита, просмотрев их в настроенных приемниках данных. Для отображения журналов может потребоваться до 10 минут.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [журналах аудита](concepts-audit-logs.md) в базе данных Azure для MariaDB
- Узнайте, как настроить журналы аудита в [Azure CLI](howto-configure-audit-logs-cli.md)