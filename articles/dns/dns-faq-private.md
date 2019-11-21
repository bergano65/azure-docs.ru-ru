---
title: 'Частная зона DNS: часто задаваемые вопросы'
description: In this article, learn frequently asked questions about Azure Private DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: de63799400a10afc1930cd373df0c8dd86320f78
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212009"
---
# <a name="azure-private-dns-faq"></a>Частная зона DNS: часто задаваемые вопросы

The following are frequently asked questions about Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>Поддерживает ли Azure DNS частные домены?

Private domains are supported using the Azure Private DNS zones feature. Private DNS zones are resolvable only from within specified virtual networks. Дополнительные сведения см. в статье [Использование Azure DNS для частных доменов](private-dns-overview.md).

Сведения о других внутренних параметрах DNS в Azure см. в статье [Разрешение имен ресурсов в виртуальных сетях Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Will Azure Private DNS zones work across Azure regions?

Да. Частные зоны поддерживаются для разрешения DNS между виртуальными сетями в разных регионах Azure. Частные зоны работают даже без явной настройки пиринга виртуальных сетей. All the virtual networks must be linked to the private DNS zone.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Нужно ли подключение к Интернету из виртуальных сетей для работы частных зон?

Нет. Частные зоны работают вместе с виртуальными сетями. You use them to manage domains for virtual machines or other resources within and across virtual networks. Для разрешения имен не нужно подключение к Интернету.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Можно ли использовать одну частную зону для разрешения имен в нескольких виртуальных сетях?

Да. You can link a private DNS zone with thousands of virtual networks. For more information, see [Azure DNS Limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Can a virtual network that belongs to a different subscription be linked to a private zone?

Да. Вы должны обладать разрешением на операцию записи для виртуальных сетей и частной зоны DNS. Разрешение на запись может быть предоставлено нескольким ролям RBAC. For example, the Classic Network Contributor RBAC role has write permissions to virtual networks and Private DNS zones Contributor role has write permissions on the private DNS zones. Дополнительные сведения о ролях RBAC см. в статье [Что такое управление доступом на основе ролей (RBAC)?](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Will the automatically registered virtual machine DNS records in a private zone be automatically deleted when you delete the virtual machine?

Да. If you delete a virtual machine within a linked virtual network with autoregistration enabled, the registered records are automatically deleted.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Can an automatically registered virtual machine record in a private zone from a linked virtual network be deleted manually?

Да. Однако вы можете перезаписать их записями DNS, созданными в зоне вручную. Чтобы узнать об этом больше, ознакомьтесь со следующими вопросом и ответом.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>What happens when I try to manually create a new DNS record into a private zone that has the same hostname as an automatically registered existing virtual machine in a linked virtual network?

You try to manually create a new DNS record into a private zone that has the same hostname as an existing, automatically registered virtual machine in a linked virtual network. При этом новая запись DNS перезаписывает автоматически зарегистрированную виртуальную машину. Повторное удаление этой созданной записи DNS из зоны происходит успешно. Автоматическая регистрация выполняется еще раз, пока существует виртуальная машина и к ней присоединен частный IP-адрес. Запись DNS автоматически не будет создана повторно в зоне.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>What happens when we unlink a linked virtual network from a private zone? Будут ли также удалены из зоны автоматически зарегистрированные записи виртуальных машин из виртуальной сети?

Да. To unlink a linked virtual network from a private zone, you update the DNS zone to remove the associated virtual network link. В этом процессе записи виртуальных машин, которые были автоматически зарегистрированы, удаляются из зоны.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>What happens when we delete a linked virtual network that's linked to a private zone? Do we have to manually update the private zone to unlink the virtual network as a linked virtual network from the zone?

Нет. When you delete a linked virtual network without unlinking it from a private zone first, your deletion operation succeeds and the links to the DNS zone are automatically cleared.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Will DNS resolution by using the default FQDN (internal.cloudapp.net) still work even when a private zone (for example, private.contoso.com) is linked to a virtual network?

Да. Private Zones don't replace the default Azure-provided internal.cloudapp.net zone. При использовании зоны internal.cloudapp.net, предоставляемой Azure, или собственной частной зоны используйте полное доменное имя зоны, выбранной для разрешения имен.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Изменится ли DNS-суффикс виртуальных машин в связанной виртуальной сети с учетом этой частной зоны?

Нет. В настоящее время DNS-суффикс виртуальных машин в связанной виртуальной сети сохраняет формат суффикса Azure по умолчанию ("*.internal.cloudapp.net"). Однако можно вручную изменить этот DNS-суффикс на виртуальных машинах, указав частную зону.
For guidance on how to change this suffix refer to [Use dynamic DNS to register hostnames in your own DNS server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>What are the usage limits for Azure DNS Private zones?

Refer to [Azure DNS limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits) for details on the usage limits for Azure DNS private zones.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Why don’t my existing private DNS zones show up in new portal experience?

If your existing private DNS zone were created using preview API, you must migrate these zones to new resource model. Private DNS zones created using preview API will not show up in new portal experience. See below for instructions on how to migrate to new resource model.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>How do I migrate my existing private DNS zones to the new model?

We strongly recommend that you migrate to the new resource model as soon as possible. Legacy resource model will be supported, however, further features will not be developed on top of this model. In future, we intend to deprecate it in favor of new resource model. For guidance on how to migrate your existing private DNS zones to new resource model see[migration guide for Azure DNS private zones](private-dns-migration-guide.md).

## <a name="next-steps"></a>Дальнейшие действия

- [Learn more about Azure Private DNS](private-dns-overview.md)
