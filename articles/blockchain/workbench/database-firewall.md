---
title: Настройка брандмауэра базы данных SQL Azure Blockchain Workbench
description: Узнайте, как настроить брандмауэр базы данных SQL Azure Блокчейн Workbench Preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0153065ca0ccd6cf34456d630d7437d5ea7c5b48
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845231"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Настройка брандмауэра базы данных Azure Blockchain Workbench

В этой статье показано, как настроить правило брандмауэра с помощью портала Azure. Правила брандмауэра позволяют внешним клиентам или приложениям подключаться к базе данных Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Подключение к базе данных Blockchain Workbench

Чтобы подключиться к базе данных, в которой вы хотите настроить правило, сделайте следующее:

1. Войдите в портал Azure с учетной записью, имеющей разрешения **владельца** для ресурсов Azure блокчейн Workbench.
2. В левой области навигации выберите **Группа ресурсов**.
3. Выберите имя группы ресурсов для развертывания Blockchain Workbench.
4. Выберите **Тип** для сортировки списка ресурсов, а затем выберите **SQL Server**.
5. В примере списка ресурсов на следующем снимке экрана показано две базы данных: *master* и *lsgn-sdk*. Настройте правило брандмауэра на *lsgn-sdk*.

![Список ресурсов Blockchain Workbench](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Создание правила брандмауэра базы данных

Чтобы создать правило брандмауэра, сделайте следующее:

1. Выберите ссылку на базу данных lsgn-sdk.
2. В строке меню выберите **Настройка брандмауэра для сервера**.

   ![Настройка брандмауэра для сервера](./media/database-firewall/configure-server-firewall.png)

3. Чтобы создать правило для организации, сделайте следующее:

   * Введите **имя правила**.
   * Введите IP-адрес в поле **НАЧАЛЬНЫЙ IP-АДРЕС** диапазона адресов.
   * Введите IP-адрес в поле **КОНЕЧНЫЙ IP-АДРЕС** диапазона адресов.

   ![Создание правила брандмауэра](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Если требуется только добавить IP-адрес компьютера, выберите **+ Add client IP** (+ Добавить IP-адрес клиента).
        
1. Чтобы сохранить конфигурацию брандмауэра, щелкните **Сохранить**.
2. Проверьте диапазон IP-адресов, настроенных для базы данных, подключившись с помощью приложения или средства. Например, SQL Server Management Studio.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Представления базы данных в Azure Blockchain Workbench)