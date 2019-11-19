---
title: Общие сведения о тегах FQDN для Брандмауэра Azure
description: Тег FQDN — это группа полных доменных имен (FQDN), связанных с известными службами Майкрософт.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169050"
---
# <a name="fqdn-tags-overview"></a>Общие сведения о тегах FQDN

Тег FQDN — это группа полных доменных имен (FQDN), связанных с известными службами Майкрософт. Теги FQDN можно использовать в правилах приложений, чтобы разрешить нужный исходящий сетевой трафик через брандмауэр.

Например, чтобы вручную разрешить сетевой трафик для Центра обновления Windows через брандмауэр, нужно создать несколько правил приложения согласно документации Майкрософт. А с помощью тегов FQDN можно создать правило приложения и включить тег **Центра обновлений Windows**. После этого сетевой трафик сможет проходить через брандмауэр к конечным точкам Центра обновления Windows.

Вы не можете создать собственные теги FQDN или указать, какие имена FQDN должны входить в тег. Майкрософт управляет полными доменными именами, включенными в теги FQDN, и обновляет тег, если эти имена меняются. 

<!--- screenshot of application rule with a FQDN tag.-->

В следующей таблице показаны текущие теги FQDN, которые можно использовать. Корпорация Майкрософт поддерживает эти теги и периодически будет добавлять новые.

## <a name="current-fqdn-tags"></a>Текущие теги полного доменного имени

|Тег FQDN  |ОПИСАНИЕ  |
|---------|---------|
|Центр обновления Windows     |Разрешает исходящий доступ к Центру обновления Майкрософт, как описано в разделе [Настройка брандмауэра для обновлений программного обеспечения](https://technet.microsoft.com/library/bb693717.aspx).|
|диагностика Windows.|Разрешает исходящий доступ ко всем [конечным точкам диагностики Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Служба Microsoft Active Protection Service (MAPS)|Разрешает исходящий доступ к [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Среда службы приложений (ASE).|Разрешает исходящий доступ к трафику платформы ASE. Этот тег не распространяется на конечные точки пользовательского хранилища и SQL, созданные ASE. Требуется включение с использованием [конечных точек службы](../virtual-network/tutorial-restrict-network-access-to-resources.md) или добавление вручную.<br><br>Дополнительные сведения об интеграции Брандмауэра Azure со средой службы приложений Azure см. в статье [Блокирование среды службы приложений](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Служба архивации Azure|Разрешает исходящий доступ к службам Azure Backup.|
|Azure HDInsight|Разрешает исходящий доступ для трафика платформы HDInsight. Этот тег не охватывает специфические для клиента хранилища или трафик SQL из HDInsight. Включите их с помощью [конечных точек службы](../virtual-network/tutorial-restrict-network-access-to-resources.md) или добавьте их вручную.|

> [!NOTE]
> При выборе тега FQDN в правиле приложения поле protocol:port должно иметь значение **https**.

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о том, как [развернуть и настроить Брандмауэр Azure с помощью портала Azure](tutorial-firewall-deploy-portal.md).
