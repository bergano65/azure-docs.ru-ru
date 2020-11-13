---
title: Пометка виртуальной машины с помощью шаблона
description: Сведения о добавлении тегов виртуальной машины с помощью шаблона.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: d1acbe82a086574a102e7897bbd3b99683c1185e
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595039"
---
# <a name="tagging-a-vm-using-a-template"></a>Добавление тегов для виртуальной машины с помощью шаблона


В этой статье описывается, как пометить виртуальную машину в Azure с помощью шаблона диспетчер ресурсов. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящее время Azure поддерживает до 50 тегов на ресурс и группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу.

[Этот шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) добавляет теги к следующим ресурсам: Compute (виртуальная машина), Storage (учетная запись хранения) и Network (общедоступный IP-адрес, виртуальная сеть и сетевой интерфейс). (Это шаблон для виртуальной машины Windows, но его можно приспособить для виртуальных машин Linux.)

Нажмите кнопку **Развернуть в Azure** , после того как перейдете по [ссылке на шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Откроется [портал Azure](https://portal.azure.com/) , где можно развернуть этот шаблон.

![Простое развертывание с тегами](./media/tag/deploy-to-azure-tags.png)

Этот шаблон включает следующие теги: *Отдел* , *Приложение* и *Создано*. Вы можете добавлять и редактировать эти теги непосредственно в шаблоне, если вам нужно изменить их имена.

![Теги Azure в шаблоне](./media/tag/azure-tags-in-a-template.png)

Как видите, теги определяются как пары "ключ-значение", разделенные двоеточием (:). Теги должны быть определены в следующем формате:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Завершив добавление нужных тегов в файл шаблона, сохраните его.

Затем вы можете указать значения для тегов в разделе **Редактирование параметров** .

![Редактирование тегов на портале Azure](./media/tag/edit-tags-in-azure-portal.png)

Нажмите **Создать** , чтобы развернуть шаблон со значениями тегов.


**Дальнейшие действия**

- Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения об Azure Resource Manager](../azure-resource-manager/management/overview.md) и [Использование тегов для организации ресурсов в Azure](../azure-resource-manager/management/tag-resources.md).
- Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) и [Получение ценных сведений о потреблении ресурсов Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
