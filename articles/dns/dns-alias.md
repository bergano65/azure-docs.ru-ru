---
title: Alias records overview - Azure DNS
description: In this article, learn about support for alias records in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: allensu
ms.openlocfilehash: da396d3e3db4acd1a9843977b8b3e3d51c33f021
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212325"
---
# <a name="azure-dns-alias-records-overview"></a>Обзор записей псевдонимов Azure DNS

Записи псевдонимов Azure DNS являются квалификациями на набор записей DNS. Они позволяют ссылаться на другие ресурсы Azure из вашей зоны DNS. Например, вы можете создать набор записей псевдонимов, который ссылается на общедоступный IP-адрес Azure вместо записи A. Псевдоним набора записей динамически указывает на экземпляр сервиса общедоступного IP-адреса Azure. В результате псевдоним набора записей просто обновляется во время разрешения DNS.

Набор записей псевдонимов поддерживается для следующих типов записей в зоне DNS Azure: 

- A
- AAAA
- CNAME

> [!NOTE]
> Если вы планируете использовать запись псевдонима для записей типов A или AAAA, чтобы указать на [профиль диспетчера трафика Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md), необходимо убедиться в том, что профиль диспетчера трафика имеет только [внешние конечные точки](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Укажите IPv4- или IPv6-адрес для внешних конечных точек в диспетчере трафика. You can't use fully-qualified domain names (FQDNs) in endpoints. Рекомендуется использовать статические IP-адреса.

## <a name="capabilities"></a>Возможности

- **Выберите ресурс общедоступного IP-адреса из набора DNS-записей A/AAAA**. You can create an A/AAAA record set and make it an alias record set to point to a public IP resource (standard or basic). The DNS record set changes automatically if the public IP address changes or is deleted. Несвязанные DNS-записи, которые указывают на неправильные IP-адреса, не используются.

   There is a current limit of 20 alias records sets per resource.

- **Выберите профиль диспетчера трафика из набора DNS-записей A/AAAA/CNAME**. Вы можете создать набор записей A/AAAA или CNAME и использовать записи псевдонимов, чтобы указать на профиль диспетчера трафика. It's especially useful when you need to route traffic at a zone apex, as traditional CNAME records aren't supported for a zone apex. Например, профиль диспетчера трафика — myprofile.trafficmanager.net, а зона DNS бизнеса — contoso.com. Вы можете создать набор записей типа A/AAAA (псевдоним) для contoso.com (вершина зоны) и указать на myprofile.trafficmanager.net.
- **Point to an Azure Content Delivery Network (CDN) endpoint**. This is useful when you create static websites using Azure storage and Azure CDN.
- **Выберите другой набор DNS-записей в пределах той же зоны**. Записи псевдонимов могут ссылаться на другие наборы записей одного типа. Например, набор записей DNS CNAME может быть псевдонимом для другого набора записей CNAME. Такой подход полезен в том случае, если вы хотите, чтобы некоторые наборы записей были псевдонимами, а некоторые ими не были.

## <a name="scenarios"></a>Сценарии

Существует несколько распространенных сценариев для записей псевдонимов.

### <a name="prevent-dangling-dns-records"></a>Предотвратить несвязанные DNS-записи

Одной из распространенных проблем с традиционными DNS-записями являются "несвязанные записи". For example, DNS records that haven't been updated to reflect changes to IP addresses. Проблема возникает особенно часто с типами записей A/AAAA или CNAME.

В традиционной записи зоны DNS, если целевой IP-адрес или CNAME больше не существуют, запись DNS, связанную с ними, необходимо обновить вручную. In some organizations, a manual update might not happen in time because of process issues or the separation of roles and associated permission levels. Например, роль, имеющая права на удаление записи CNAME или IP-адрес приложения. Но она не имеет прав на обновление записи DNS, указывающей на эти целевые объекты. Задержка при обновлении записи DNS может стать причиной сбоя для пользователей.

Записи псевдонимов предотвращают несвязанные ссылки, тесно связывая жизненный цикл записи DNS с ресурсом Azure. Например, рассмотрим запись DNS, которая является псевдонимом и указывает на общедоступный IP-адрес или профиль диспетчера трафика. If you delete those underlying resources, the DNS alias record becomes an empty record set. It no longer references the deleted resource.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Автоматическое обновление набора записей DNS при изменении IP-адресов приложения

Этот сценарий похож на предыдущий. Возможно, приложение перемещается, или основная виртуальная машина перезапускается. При изменении IP-адреса для основного общедоступного IP-ресурса запись псевдонима обновляется. Это позволяет избежать потенциальных угроз безопасности, возникающих в случае направления пользователей к другому приложению, для которого назначен старый общедоступный IP-адрес.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Размещайте приложения с балансировкой нагрузки в апексе зоны

Протокол DNS предотвращает назначение записей CNAME на вершине зоны. For example if your domain is contoso.com; you can create CNAME records for somelabel.contoso.com; but you can't create CNAME for contoso.com itself.
Это ограничение создает проблемы владельцам приложений, которые размещают приложения с балансировкой нагрузки за [диспетчером трафика Azure](../traffic-manager/traffic-manager-overview.md). Since using a Traffic Manager profile requires creation of a CNAME record, it isn't possible to point at the Traffic Manager profile from the zone apex.

This problem is solved using alias records. Unlike CNAME records, alias records are created at the zone apex and application owners can use it to point their zone apex record to a Traffic Manager profile that has external endpoints. Application owners point to the same Traffic Manager profile that's used for any other domain within their DNS zone.

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. Дополнительные сведения об использовании записей псевдонимов с профилями диспетчера трафика Azure см. в разделе "Дополнительная информация".

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Point zone apex to Azure CDN endpoints

Just like a Traffic Manager profile, you can also use alias records to point your DNS zone apex to Azure CDN endpoints. This is useful when you create static websites using Azure storage and Azure CDN. You can then access the website without prepending "www" to your DNS name.

For example, if your static website is named www.contoso.com, your users can access your site using contoso.com without the need to prepend www to the DNS name.

As described previously, CNAME records aren't supported at the zone apex. So, you can’t use a CNAME record to point contoso.com to your CDN endpoint. Instead, you can use an alias record to point the zone apex to a CDN endpoint directly.

> [!NOTE]
> Pointing a zone apex to CDN endpoints for Azure CDN from Akamai is currently not supported.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о записях псевдонима см. в следующих статьях:

- [Руководство по настройке записи псевдонима для ссылки на общедоступный IP-адрес Azure](tutorial-alias-pip.md)
- [Руководство. Настройка записи псевдонима для поддержки вершинных доменных имен с помощью диспетчера трафика](tutorial-alias-tm.md)
- [DNS: часто задаваемые вопросы](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
