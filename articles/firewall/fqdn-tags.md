---
title: Общие сведения о тегах FQDN для Брандмауэра Azure
description: Сведения о тегах FQDN в Брандмауэре Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/1/2018
ms.author: victorh
ms.openlocfilehash: 897ea3856516b5429ffb770164f863d71e7ae0dd
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419016"
---
# <a name="fqdn-tags-overview"></a>Общие сведения о тегах FQDN

Тег FQDN — это группа полных доменных имен (FQDN), связанных с известными службами Майкрософт. Теги FQDN можно использовать в правилах приложений, чтобы разрешить нужный исходящий сетевой трафик через брандмауэр.

Например, чтобы вручную разрешить сетевой трафик для Центра обновления Windows через брандмауэр, нужно создать несколько правил приложения согласно документации Майкрософт. А с помощью тегов FQDN можно создать правило приложения и включить тег **Центра обновлений Windows**. После этого сетевой трафик сможет проходить через брандмауэр к конечным точкам Центра обновления Windows.

Вы не можете создать собственные теги FQDN или указать, какие имена FQDN должны входить в тег. Майкрософт управляет полными доменными именами, включенными в теги FQDN, и обновляет тег, если эти имена меняются. 

<!--- screenshot of application rule with a FQDN tag.-->

В следующей таблице показаны текущие теги FQDN, которые можно использовать. Корпорация Майкрософт поддерживает эти теги и периодически будет добавлять новые.

|Тег FQDN  |ОПИСАНИЕ  |
|---------|---------|
|Центр обновления Windows     |Разрешает исходящий доступ к Центру обновления Майкрософт, как описано в разделе [Настройка брандмауэра для обновлений программного обеспечения](https://technet.microsoft.com/library/bb693717.aspx).|
|диагностика Windows.|Разрешает исходящий доступ ко всем [конечным точкам диагностики Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Служба Microsoft Active Protection Service (MAPS)|Разрешает исходящий доступ к [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Среда службы приложений (ASE).|Разрешает исходящий доступ к трафику платформы ASE. Этот тег не распространяется на конечные точки пользовательского хранилища и SQL, созданные ASE. Требуется включение с использованием [конечных точек службы](../virtual-network/tutorial-restrict-network-access-to-resources.md) или добавление вручную.<br><br>Дополнительные сведения об интеграции Брандмауэра Azure со средой службы приложений Azure см. в статье [Блокирование среды службы приложений](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Служба архивации Azure|Разрешает исходящий доступ к службам Azure Backup.

> [!NOTE]
> При выборе тега FQDN в правиле приложения поле protocol:port должно иметь значение **https**.

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о том, как [развернуть и настроить Брандмауэр Azure с помощью портала Azure](tutorial-firewall-deploy-portal.md).