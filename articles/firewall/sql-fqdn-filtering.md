---
title: Настройка правил применения приложения Azure Firewall с помощью F-DN СЗЛ
description: В этой статье вы узнаете, как настроить F-DN с S'L в правилах применения Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501493"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Настройка правил применения приложения Azure Firewall с помощью F-DN СЗЛ

> [!IMPORTANT]
> Правила применения приложения Azure Firewall с S'L ФЗНв в настоящее время находятся в открытом доступе.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Теперь можно настроить правила применения приложения Azure Firewall с помощью F-DN СЗЛ. Это позволяет ограничить доступ из виртуальных сетей только в указанные экземпляры сервера S'L.

С помощью СЗЛ ФЗДН можно фильтровать трафик:

- От VNets к базе данных Azure S'L или складу данных Azure S'L. Например: Разрешить доступ к *sql-server1.database.windows.net*только .
- От наемных помещений до управляемых экземпляров Azure S'L или S'L IaaS, работающих в ваших VNets.
- От выступления до управляемых экземпляров Azure S'L или S'L IaaS, работающих в ваших VNets.

Во время публичного предварительного просмотра фильтрация S'L F'DN поддерживается только в [прокси-режиме](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (порт 1433). Если вы используете S'L в режиме перенаправления по умолчанию, вы можете фильтровать доступ с помощью тега службы S'L как часть [сетевых правил.](overview.md#network-traffic-filtering-rules)
Если вы используете порты без дефолта для трафика S'L IaaS, вы можете настроить эти порты в правилах приложения брандмауэра.

Правила применения с S'L ФЗНв в настоящее время доступны во всех регионах через портал Azure, Azure CLI, REST и шаблоны.

## <a name="configure-using-azure-cli"></a>Настройка с помощью Azure CLI

1. Развертывание [брандмауэра Azure с помощью Azure CLI.](deploy-cli.md)
2. При фильтрации трафика в базу данных Azure S'L, Хранилище данных S'L или управляемый экземпляр S'L убедитесь, что режим подключения S'L настроен на **прокси.** Чтобы узнать, как переключить режим [Azure SQL Connectivity Settings](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)подключения, см.

   > [!NOTE]
   > *Прокси-режим* S'L может привести к большей задержке по сравнению с *перенаправлением.* Если вы хотите продолжать использовать режим перенаправления, который является по умолчанию для клиентов, подключенных к Azure, вы можете фильтровать доступ с помощью [тега службы](service-tags.md) S'L в [правилах сети](tutorial-firewall-deploy-portal.md#configure-a-network-rule)брандмауэра.

3. Навлажив правило приложения с помощью S'L ФЗДН, чтобы обеспечить доступ к серверу S'L:

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
1. Развертывание [брандмауэра Azure с помощью Azure CLI.](deploy-cli.md)
2. При фильтрации трафика в базу данных Azure S'L, Хранилище данных S'L или управляемый экземпляр S'L убедитесь, что режим подключения S'L настроен на **прокси.** Чтобы узнать, как переключить режим [Azure SQL Connectivity Settings](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)подключения, см.  

   > [!NOTE]
   > *Прокси-режим* S'L может привести к большей задержке по сравнению с *перенаправлением.* Если вы хотите продолжать использовать режим перенаправления, который является по умолчанию для клиентов, подключенных к Azure, вы можете фильтровать доступ с помощью [тега службы](service-tags.md) S'L в [правилах сети](tutorial-firewall-deploy-portal.md#configure-a-network-rule)брандмауэра.
3. Добавьте правило приложения с соответствующим протоколом, портовым и S'L ФЗДН, а затем выберите **Сохранить.**
   ![правило применения с Помощью СЗЛ ФЗДН](media/sql-fqdn-filtering/application-rule-sql.png)
4. Доступ к S'L с виртуальной машины в VNet, который фильтрует трафик через брандмауэр. 
5. Проверка [журналов Azure Firewall,](log-analytics-samples.md) показывающее, что трафик разрешен.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать о режимах прокси-серверов и перенаправления см. [Azure SQL database connectivity architecture](../sql-database/sql-database-connectivity-architecture.md)