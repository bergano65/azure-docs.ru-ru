---
title: Создание полного доменного имени для виртуальной машины в портал Azure
description: Узнайте, как создать полное доменное имя (FQDN) для виртуальной машины в портал Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351930"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Создание полного доменного имени на портале Azure для виртуальной машины Linux

При создании виртуальной машины на [портале Azure](https://portal.azure.com) для нее автоматически создается ресурс общедоступного IP-адреса. Этот IP-адрес используется для удаленного доступа к данной виртуальной машине. Несмотря на то что портал не создает [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), его можно добавить после создания виртуальной машины. В этой статье показан процесс создания DNS-имени или полного доменного имени. 

## <a name="create-a-fqdn"></a>Создание полного доменного имени
Для работы с руководством требуется виртуальная машина. При необходимости на портале можно создать виртуальную машину [Linux](./linux/quick-create-portal.md) или [Windows](./windows/quick-create-portal.md) . Когда виртуальная машина будет готова, выполните следующие действия:


1. Выберите виртуальную машину на портале. В разделе **DNS-имя** выберите **настроить**.
2. Введите DNS-имя и нажмите кнопку **сохранить** в верхней части страницы.
3. Чтобы вернуться в колонку обзор виртуальной машины, закройте колонку **Конфигурация** , щелкнув значок **X** в правом верхнем углу. 
4. Убедитесь, что *DNS-имя* теперь отображается правильно.
   



## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у виртуальной машины есть общедоступный IP-адрес и DNS-имя, можно развернуть общие платформы приложений или службы, такие как nginx, MongoDB и DOCKER.

Изучите дополнительные сведения об [использовании Resource Manager](../azure-resource-manager/management/overview.md), чтобы получить советы по созданию развертываний Azure.

