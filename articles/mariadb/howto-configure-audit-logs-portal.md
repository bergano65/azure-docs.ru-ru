---
title: Настройка и доступа к журналам аудита для базы данных Azure для MariaDB на портале Azure
description: В этой статье описывается, как настроить и получить доступ к журналам аудита в базе данных Azure для MariaDB с портала Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 8fadcc8cb418063662df45d4134bef0578ffccda
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444822"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Настроить и получить доступ к журналам аудита на портале Azure

Можно настроить [база данных Azure для журналов аудита MariaDB](concepts-audit-logs.md) и параметров диагностики с помощью портала Azure.

> [!IMPORTANT]
> Функция журнала аудита в настоящий момент в предварительной версии.

## <a name="prerequisites"></a>Технические условия

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [База данных Azure для сервера MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Настройка ведения журнала аудита

Включите и настройте ведение журнала аудита.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. Выберите сервер Базы данных Azure для MariaDB.

1. В разделе **параметры** раздел на боковой панели, выберите **параметров сервера**.
    ![Параметры сервера](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Обновление **audit_log_enabled** значение ON.
    ![Включить журналы аудита](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Выберите [типы событий](concepts-audit-logs.md#configure-audit-logging) записывается в журнал, обновив **audit_log_events** параметра.
    ![Событий журнала аудита](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Добавьте пользователей должны быть исключены из ведения журнала, обновив MariaDB **audit_log_exclude_users** параметра. Укажите пользователей, предоставляя свои имя пользователя MariaDB.
    ![Пользователи exclude журналов аудита](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. После изменения параметров нажмите кнопку **Сохранить**. Также вы можете нажать кнопку **Отменить**, чтобы отменить изменения.
    ![Сохранить](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Настройка журналов диагностики

1. В разделе **мониторинг** раздел на боковой панели, выберите **параметров диагностики**.

1. Щелкните «+ Добавить параметр диагностики» ![добавьте параметр диагностики](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Укажите имя параметра диагностики.

1. Укажите, приемников данных для отправки журналов аудита (учетной записи хранения, концентратор событий или рабочую область Log Analytics).

1. Выберите «MySqlAuditLogs» в качестве типа журнала.
![Настройка параметров диагностики](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. После настройки приемника данных для передачи журналов аудита для, можно щелкнуть **Сохранить**.
![Сохранение параметров диагностики](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Доступ к журналам аудита, просматривая их в приемниками данных, которые вы настроили. Может занять до 10 минут для отображения журналов.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [журналы аудита](concepts-audit-logs.md) в базе данных Azure для MariaDB.