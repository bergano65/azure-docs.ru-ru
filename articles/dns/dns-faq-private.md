---
title: 'Частная зона DNS: часто задаваемые вопросы'
description: В этой статье узнайте часто задаваемые вопросы о Azure Private DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939423"
---
# <a name="azure-private-dns-faq"></a>Частная зона DNS: часто задаваемые вопросы

Часто задаются вопросы о частных DNS Azure.

## <a name="does-azure-dns-support-private-domains"></a>Поддерживает ли Azure DNS частные домены?

Частные домены поддерживаются с помощью функции Azure Private DNS зон. Частные зоны DNS разрешимы только из указанных виртуальных сетей. Дополнительные сведения см. в статье [Использование Azure DNS для частных доменов](private-dns-overview.md).

Сведения о других внутренних параметрах DNS в Azure см. в статье [Разрешение имен ресурсов в виртуальных сетях Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Будут ли зоны Azure Private DNS работать в регионах Azure?

Да. Частные зоны поддерживаются для разрешения DNS между виртуальными сетями в разных регионах Azure. Частные зоны работают даже без явной настройки пиринга виртуальных сетей. Все виртуальные сети должны быть связаны с частной зоной DNS.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Нужно ли подключение к Интернету из виртуальных сетей для работы частных зон?

Нет. Частные зоны работают вместе с виртуальными сетями. Вы используете их для управления доменами для виртуальных машин или других ресурсов внутри и через виртуальные сети. Для разрешения имен не нужно подключение к Интернету.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Можно ли использовать одну частную зону для разрешения имен в нескольких виртуальных сетях?

Да. Вы можете связать частную зону DNS с тысячами виртуальных сетей. Для получения дополнительной [информации](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) см.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Может ли виртуальная сеть, которая принадлежит к другой подписке, быть связана с частной зоной?

Да. Вы должны обладать разрешением на операцию записи для виртуальных сетей и частной зоны DNS. Разрешение на запись может быть предоставлено нескольким ролям RBAC. Например, роль вкладчика Classic Network RBAC записывает разрешения на виртуальные сети, а роль вкладчика частных DNS-зон написания разрешений в частных зонах DNS. Дополнительные сведения о ролях RBAC см. в статье [Что такое управление доступом на основе ролей (RBAC)?](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Будет ли автоматически зарегистрированная виртуальная машина DNS-записи в частной зоне автоматически удалена при удалении виртуальной машины?

Да. При удалении виртуальной машины в связанной виртуальной сети с включенной автоматической регистрацией зарегистрированные записи автоматически удаляются.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Можно ли удалить автоматически зарегистрированную виртуальную запись машины в частной зоне из связанной виртуальной сети вручную?

Да. Однако вы можете перезаписать их записями DNS, созданными в зоне вручную. Чтобы узнать об этом больше, ознакомьтесь со следующими вопросом и ответом.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Что происходит, когда я пытаюсь вручную создать новую запись DNS в частную зону с тем же хост-именем, что и автоматически зарегистрированная существующая виртуальная машина в связанной виртуальной сети?

Вы пытаетесь вручную создать новую запись DNS в частную зону, которая имеет то же имя хоста, что и существующая, автоматически зарегистрированная виртуальная машина в связанной виртуальной сети. При этом новая запись DNS перезаписывает автоматически зарегистрированную виртуальную машину. Повторное удаление этой созданной записи DNS из зоны происходит успешно. Автоматическая регистрация выполняется еще раз, пока существует виртуальная машина и к ней присоединен частный IP-адрес. Запись DNS автоматически не будет создана повторно в зоне.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Что происходит, когда мы отсоединяем связанную виртуальную сеть из частной зоны? Будут ли также удалены из зоны автоматически зарегистрированные записи виртуальных машин из виртуальной сети?

Да. Чтобы отсоединить связанную виртуальную сеть из частной зоны, вы обновите зону DNS, чтобы удалить связанную виртуальную сетевую ссылку. В этом процессе записи виртуальных машин, которые были автоматически зарегистрированы, удаляются из зоны.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Что происходит, когда мы удаляем связанную виртуальную сеть, связанную с частной зоной? Должны ли мы вручную обновлять частную зону, чтобы отсоединить виртуальную сеть как связанную виртуальную сеть из зоны?

Нет. При удалении связанной виртуальной сети, не отключая ее от частной зоны, операция удаления сменяется успешно, и ссылки на зону DNS автоматически удаляются.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Будет ли разрешение DNS с помощью по умолчанию ФЗН (internal.cloudapp.net) работать, даже если частная зона (например, private.contoso.com) связана с виртуальной сетью?

Да. Частные зоны не заменяют зону internal.cloudapp.net по умолчанию, предоставленную Azure. При использовании зоны internal.cloudapp.net, предоставляемой Azure, или собственной частной зоны используйте полное доменное имя зоны, выбранной для разрешения имен.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Изменится ли DNS-суффикс виртуальных машин в связанной виртуальной сети с учетом этой частной зоны?

Нет. В настоящее время DNS-суффикс виртуальных машин в связанной виртуальной сети сохраняет формат суффикса Azure по умолчанию ("*.internal.cloudapp.net"). Однако можно вручную изменить этот DNS-суффикс на виртуальных машинах, указав частную зону.
Для получения рекомендаций о том, как изменить этот суффикс, обратитесь к [использованию динамических DNS для регистрации имен хоста на вашем собственном DNS-сервере](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Каковы ограничения использования для частных зон Azure DNS?

Обратитесь к [ограничениям Azure DNS](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) для получения подробной информации об ограничениях использования для частных зон Azure DNS.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Почему мои существующие частные зоны DNS не отображаются в новом опыте портала?

Если существующая частная зона DNS была создана с помощью API предварительного просмотра, необходимо перенести эти зоны в новую модель ресурса. Частные Зоны DNS, созданные с помощью API предварительного просмотра, не будут отображаться в новом опыте портала. Ниже приведены инструкции о том, как перейти на новую модель ресурсов.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Как перенести существующие частные зоны DNS в новую модель?

Мы настоятельно рекомендуем как можно скорее перейти на новую модель ресурсов. Устаревшая модель ресурсов будет поддерживаться, однако, дополнительные функции не будут разработаны в верхней части этой модели. В будущем мы намерены обесценить его в пользу новой ресурсной модели. Для получения рекомендаций о том, как перенести существующие частные зоны DNS в новую модель ресурсов, см.[руководство по миграции для частных зон Azure DNS.](private-dns-migration-guide.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Узнайте больше о Частных DNS Azure](private-dns-overview.md)
