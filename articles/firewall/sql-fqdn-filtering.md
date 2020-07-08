---
title: Настройка правил приложений брандмауэра Azure с полными доменными именами SQL
description: В этой статье рассказывается о настройке полных доменных имен SQL в правилах приложений брандмауэра Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: e921ea707e4a0df968d162726fe53064491cb032
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610512"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Настройка правил приложений брандмауэра Azure с полными доменными именами SQL

Теперь можно настраивать правила приложений брандмауэра Azure с полными доменными именами SQL. Это позволяет ограничить доступ из виртуальных сетей заданными экземплярами SQL Server.

Полные доменные имена SQL позволяют фильтровать трафик

- из виртуальных сетей в базу данных SQL Azure или хранилище данных SQL Azure. Пример: Разрешить только доступ к *sql-server1.database.windows.net*.
- Из локальной среды в управляемые экземпляры SQL Azure или IaaS SQL, выполняемую в ваших виртуальных сетях.
- Из туннеля spoke-to-spoke в управляемые экземпляры SQL Azure или IaaS SQL, выполняемую в ваших виртуальных сетях.

Фильтрация полных доменных имен SQL поддерживается только в [прокси-режиме](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (порт 1433). При использовании SQL в режиме перенаправления по умолчанию можно фильтровать доступ, используя тег службы SQL в составе [сетевых правил](features.md#network-traffic-filtering-rules).
Если для трафика IaaS SQL используются порты не по умолчанию, можно настроить эти порты в правилах приложений брандмауэра.

## <a name="configure-using-azure-cli"></a>Настройка с использованием интерфейса командной строки Azure

1. Развертывание [брандмауэра Azure с использованием интерфейса командной строки Azure](deploy-cli.md).
2. Если вы фильтруете трафик, направляющийся в базу данных SQL Azure, хранилище данных SQL или управляемый экземпляр SQL, убедитесь, что задан режим подключения SQL через **прокси**. Сведения об изменении режима подключения SQL см. в разделе [Настройки подключения SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > В *прокси-* режиме SQL может наблюдаться увеличенная задержка по сравнению с режимом *перенаправления*. Если вы хотите продолжить пользоваться режимом перенаправления, который применяется для клиентов, подключающихся внутри Azure, по умолчанию, можно фильтровать доступ, используя [тег службы](service-tags.md) SQL в [сетевых правилах](tutorial-firewall-deploy-portal.md#configure-a-network-rule) брандмауэра.

3. Настройте правило приложения с полным доменным именем SQL, чтобы разрешить доступ к серверу SQL.

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Настройка с помощью портала Azure
1. Развертывание [брандмауэра Azure с использованием интерфейса командной строки Azure](deploy-cli.md).
2. Если вы фильтруете трафик, направляющийся в базу данных SQL Azure, хранилище данных SQL или управляемый экземпляр SQL, убедитесь, что задан режим подключения SQL через **прокси**. Сведения об изменении режима подключения SQL см. в разделе [Настройки подключения SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > В *прокси-* режиме SQL может наблюдаться увеличенная задержка по сравнению с режимом *перенаправления*. Если вы хотите продолжить пользоваться режимом перенаправления, который применяется для клиентов, подключающихся внутри Azure, по умолчанию, можно фильтровать доступ, используя [тег службы](service-tags.md) SQL в [сетевых правилах](tutorial-firewall-deploy-portal.md#configure-a-network-rule) брандмауэра.
3. Добавьте правило приложения с соответствующим протоколом, портом и полным доменным именем SQL, а затем нажмите **Сохранить**.
   ![Правило приложения с полным доменным именем SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Осуществляйте доступ к SQL с виртуальной машины в виртуальной сети, которая фильтрует трафик с использованием брандмауэра. 
5. Убедитесь, что в [журналах брандмауэра Azure](log-analytics-samples.md) указано, что трафик разрешен.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о прокси-сервере SQL и режимах перенаправления см. в статье [Архитектура подключения к базе данных SQL Azure](../azure-sql/database/connectivity-architecture.md).