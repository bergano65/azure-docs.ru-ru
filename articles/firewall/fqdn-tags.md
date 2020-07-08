---
title: Общие сведения о тегах FQDN для Брандмауэра Azure
description: Тег FQDN — это группа полных доменных имен (FQDN), связанных с известными службами Майкрософт.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: e29e568786881f663414dcdf3eff72d4d72ab181
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610614"
---
# <a name="fqdn-tags-overview"></a>Общие сведения о тегах FQDN

Тег FQDN — это группа полных доменных имен (FQDN), связанных с известными службами Майкрософт. Теги FQDN можно использовать в правилах приложений, чтобы разрешить нужный исходящий сетевой трафик через брандмауэр.

Например, чтобы вручную разрешить сетевой трафик для Центра обновления Windows через брандмауэр, нужно создать несколько правил приложения согласно документации Майкрософт. А с помощью тегов FQDN можно создать правило приложения и включить тег **Центра обновлений Windows**. После этого сетевой трафик сможет проходить через брандмауэр к конечным точкам Центра обновления Windows.

Вы не можете создать собственные теги FQDN или указать, какие имена FQDN должны входить в тег. Майкрософт управляет полными доменными именами, включенными в теги FQDN, и обновляет тег, если эти имена меняются. 

<!--- screenshot of application rule with a FQDN tag.-->

В следующей таблице показаны текущие теги FQDN, которые можно использовать. Корпорация Майкрософт поддерживает эти теги и периодически будет добавлять новые.

## <a name="current-fqdn-tags"></a>Текущие теги полного доменного имени

|Тег FQDN  |Описание:  |
|---------|---------|
|Центр обновления Windows     |Разрешает исходящий доступ к Центру обновления Майкрософт, как описано в разделе [Настройка брандмауэра для обновлений программного обеспечения](https://technet.microsoft.com/library/bb693717.aspx).|
|диагностика Windows.|Разрешает исходящий доступ ко всем [конечным точкам диагностики Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Служба Microsoft Active Protection Service (MAPS)|Разрешает исходящий доступ к [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Среда службы приложений (ASE).|Разрешает исходящий доступ к трафику платформы ASE. Этот тег не распространяется на конечные точки пользовательского хранилища и SQL, созданные ASE. Требуется включение с использованием [конечных точек службы](../virtual-network/tutorial-restrict-network-access-to-resources.md) или добавление вручную.<br><br>Дополнительные сведения об интеграции Брандмауэра Azure со средой службы приложений Azure см. в статье [Блокирование среды службы приложений](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Разрешает исходящий доступ к службам Azure Backup.|
|Azure HDInsight|Разрешает исходящий доступ для трафика платформы HDInsight. Этот тег не охватывает специфические для клиента хранилища или трафик SQL из HDInsight. Включите их с помощью [конечных точек службы](../virtual-network/tutorial-restrict-network-access-to-resources.md) или добавьте их вручную.|
|Виндовсвиртуалдесктоп (ВВД)|Разрешает исходящий трафик платформы виртуальных рабочих столов Windows. Этот тег не охватывает специфические для развертывания хранилища и конечные точки служебной шины, созданные ВВД. Кроме того, требуются правила сети DNS и службы KMS. Дополнительные сведения об интеграции брандмауэра Azure с ВВД см. в статье [Использование брандмауэра Azure для защиты развертывания виртуальных рабочих столов в Windows](protect-windows-virtual-desktop.md).|
|Служба Azure Kubernetes (AKS)|Разрешает исходящий доступ к AKS. Дополнительные сведения см. [в статье Использование брандмауэра Azure для защиты развертываний Azure Kubernetes Service (AKS)](protect-azure-kubernetes-service.md).|

> [!NOTE]
> При выборе тега FQDN в правиле приложения поле protocol:port должно иметь значение **https**.

## <a name="next-steps"></a>Дальнейшие шаги

См. дополнительные сведения о том, как [развернуть и настроить Брандмауэр Azure с помощью портала Azure](tutorial-firewall-deploy-portal.md).
