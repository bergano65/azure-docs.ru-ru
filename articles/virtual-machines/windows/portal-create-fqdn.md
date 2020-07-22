---
title: Создание полного доменного имени для виртуальной машины Windows в портал Azure
description: Узнайте, как создать полное доменное имя (FQDN) для виртуальной машины на основе Resource Manager на портале Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ece0877c97d3c2cda30aab1b3f7fe56f20080996
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82101660"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Создание полного доменного имени на портале Azure для виртуальной машины Windows

При создании виртуальной машины на [портале Azure](https://portal.azure.com) для нее автоматически создается ресурс общедоступного IP-адреса. Этот IP-адрес используется для удаленного доступа к данной виртуальной машине. Несмотря на то что портал не создает [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), его можно создать после создания виртуальной машины. В этой статье показан процесс создания DNS-имени или полного доменного имени.

## <a name="create-a-fqdn"></a>Создание полного доменного имени
Для работы с руководством требуется виртуальная машина. При необходимости ее можно создать [на портале](quick-create-portal.md) или с помощью [Azure PowerShell](quick-create-powershell.md). Когда виртуальная машина будет готова, выполните следующие действия:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Теперь вы можете удаленно подключиться к виртуальной машине с помощью DNS-имени, например, используя протокол удаленного рабочего стола (RDP).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у виртуальной машины имеется общедоступный IP-адрес и DNS-имя, можно развернуть общие программные платформы или службы, такие как IIS, SQL или SharePoint.

Изучите дополнительные сведения об [использовании Resource Manager](../../azure-resource-manager/management/overview.md), чтобы получить советы по созданию развертываний Azure.

