---
title: Настройка правил приложения брандмауэра Azure с помощью полных доменных имен SQL
description: Из этой статьи вы узнаете, как настроить полные доменные имена SQL в правилах приложения брандмауэра Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/11/2020
ms.author: victorh
ms.openlocfilehash: 28252b42264dc6c1be403e99689f845d7143b1f7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200467"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Настройка правил приложения брандмауэра Azure с помощью полных доменных имен SQL

Теперь правила приложения брандмауэра Azure можно настроить с помощью полных доменных имен SQL. Это позволяет ограничить доступ в виртуальных сетях только указанными экземплярами SQL Server.

С помощью полных доменных имен SQL можно фильтровать трафик:

- Из виртуальных сетей в базу данных SQL Azure или хранилище данных SQL Azure. Например: разрешить доступ только к *SQL-Server1.Database.Windows.NET*.
- Из локальной среды в управляемые экземпляры SQL Azure или SQL IaaS, работающие в виртуальных сетей.
- От периферийных серверов до управляемых экземпляров SQL Azure или SQL IaaS, работающих в виртуальных сетей.

Фильтрация полных доменных имен SQL поддерживается только в [прокси-режиме](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (порт 1433). При использовании SQL в режиме перенаправления по умолчанию можно отфильтровать доступ с помощью тега службы SQL в рамках [сетевых правил](overview.md#network-traffic-filtering-rules).
Если для трафика IaaS SQL используются порты не по умолчанию, эти порты можно настроить в правилах брандмауэра приложения.

Правила приложений с полным доменным имен SQL в настоящее время доступны во всех регионах с помощью портал Azure, Azure CLI, других и шаблонов.

## <a name="configure-using-azure-cli"></a>Настройка с помощью Azure CLI

1. Разверните [брандмауэр Azure с помощью Azure CLI](deploy-cli.md).
2. Если вы фильтруете трафик в базу данных SQL Azure, хранилище данных SQL или SQL Управляемый экземпляр, убедитесь, что для режима подключения SQL задано значение **прокси**. Сведения о том, как переключить режим подключения SQL, см. в статье [Параметры подключения к SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > Режим *прокси* SQL может привести к увеличению задержки по сравнению с *перенаправлением*. Если вы хотите продолжить использовать режим перенаправления, который используется по умолчанию для клиентов, подключающихся в Azure, можно отфильтровать доступ с помощью [тега службы](service-tags.md) SQL в [правилах сети](tutorial-firewall-deploy-portal.md#configure-a-network-rule)брандмауэра.

3. Настройте правило приложения с полным доменным именем SQL, чтобы разрешить доступ к SQL Server:

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
1. Разверните [брандмауэр Azure с помощью Azure CLI](deploy-cli.md).
2. Если вы фильтруете трафик в базу данных SQL Azure, хранилище данных SQL или SQL Управляемый экземпляр, убедитесь, что для режима подключения SQL задано значение **прокси**. Сведения о том, как переключить режим подключения SQL, см. в статье [Параметры подключения к SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > Режим *прокси* SQL может привести к увеличению задержки по сравнению с *перенаправлением*. Если вы хотите продолжить использовать режим перенаправления, который используется по умолчанию для клиентов, подключающихся в Azure, можно отфильтровать доступ с помощью [тега службы](service-tags.md) SQL в [правилах сети](tutorial-firewall-deploy-portal.md#configure-a-network-rule)брандмауэра.
3. Добавьте правило приложения с соответствующим протоколом, портом и полным доменным именем SQL, а затем нажмите кнопку **сохранить**.
   ![правило приложения с полным доменным именем SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Получите доступ к SQL из виртуальной машины в виртуальной сети, которая фильтрует трафик через брандмауэр. 
5. Убедитесь, что в [журналах брандмауэра Azure](log-analytics-samples.md) разрешено отображение трафика.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о прокси-сервере SQL и режимах перенаправления см. в статье [Архитектура подключения к базе данных SQL Azure](../sql-database/sql-database-connectivity-architecture.md).