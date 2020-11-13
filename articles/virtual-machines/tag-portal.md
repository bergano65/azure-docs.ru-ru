---
title: Пометка виртуальной машины с помощью портал Azure
description: Сведения о добавлении тегов виртуальной машины с помощью портал Azure.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595038"
---
# <a name="tagging-a-vm-using-the-portal"></a>Добавление тегов для виртуальной машины с помощью портала

В этой статье описывается, как добавить теги в виртуальную машину с помощью портала. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящее время Azure поддерживает до 50 тегов на ресурс и группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу.


1. Перейдите к виртуальной машине на портале.
1. В разделе **основные компоненты** выберите **щелкните здесь, чтобы добавить теги**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Снимок экрана с разделом основных компонентов на странице виртуальной машины.":::

1. Добавьте значение для параметра **имя** и **значение** и нажмите кнопку **сохранить**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Снимок экрана страницы для добавления пары &quot;ключ-значение&quot; в качестве тега.":::



**Дальнейшие действия**

- Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения об Azure Resource Manager](../azure-resource-manager/management/overview.md) и [Использование тегов для организации ресурсов в Azure](../azure-resource-manager/management/tag-resources.md).
- Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) и [Получение ценных сведений о потреблении ресурсов Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).