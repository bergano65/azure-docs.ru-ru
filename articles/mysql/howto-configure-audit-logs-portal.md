---
title: Доступ к журналам аудита — портал Azure — база данных Azure для MySQL
description: В этой статье описывается, как настроить и получить доступ к журналам аудита в базе данных Azure для MySQL из портал Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: ff1a6c63b6eb99acdef955806a138e3e22b8902a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773716"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Настройка и доступ к журналам аудита для базы данных Azure для MySQL в портал Azure

Вы можете настроить [журналы аудита базы данных Azure для MySQL](concepts-audit-logs.md) и параметры диагностики из портал Azure.

> [!IMPORTANT]
> Функции журнала аудита в настоящее время доступны в предварительной версии.

## <a name="prerequisites"></a>Технические условия

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md);

## <a name="configure-audit-logging"></a>Настройка ведения журнала аудита

Включение и Настройка ведения журнала аудита.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. Выберите сервер базы данных Azure для MySQL.

1. В разделе **Параметры** на боковой панели выберите **Параметры сервера**.
    ![Параметры сервера](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Обновите параметр **audit_log_enabled** в значение ON.
    ![включить журналы аудита](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Выберите [типы событий](concepts-audit-logs.md#configure-audit-logging) для записи, обновив параметр **audit_log_events** .
    ![событий журнала аудита](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Добавьте пользователей MySQL, которые будут исключены из ведения журнала, путем обновления параметра **audit_log_exclude_users** . Укажите пользователей, указав имя пользователя MySQL.
    журнал аудита ![исключение пользователей](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. После изменения параметров нажмите кнопку **Сохранить**. Также вы можете нажать кнопку **Отменить**, чтобы отменить изменения.
    ![Сохранить](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Настройка журналов диагностики

1. В разделе **мониторинг** на боковой панели выберите **параметры диагностики**.

1. Щелкните "+ добавить параметр диагностики" ![добавить параметр диагностики](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Укажите имя параметра диагностики.

1. Укажите приемники данных для отправки журналов аудита (учетную запись хранения, концентратор событий и (или) рабочую область Log Analytics).

1. Выберите "Мисклаудитлогс" в качестве типа журнала.
![настроить параметр диагностики](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. После настройки приемников данных для передачи журналов аудита в можно нажать кнопку **сохранить**.
![сохранить параметр диагностики](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Получите доступ к журналам аудита, просмотрев их в настроенных приемниках данных. Для отображения журналов может потребоваться до 10 минут.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [журналах аудита](concepts-audit-logs.md) в базе данных Azure для MySQL.