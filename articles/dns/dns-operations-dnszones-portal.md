---
title: Управление зонами DNS в службе DNS Azure — портал Azure | Документация Майкрософт
description: Зонами DNS можно управлять с помощью портала Azure. В этой статье описывается, как обновлять, удалять и создавать зоны DNS в службе DNS Azure.
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: allensu
ms.openlocfilehash: 5d4cc57c4cb5db7f04d604c8ccbc408df1a3e707
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211911"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Как управлять зонами DNS с помощью портала Azure

> [!div class="op_single_selector"]
> * [Портал](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Классический Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Интерфейс командной строки Azure](dns-operations-dnszones-cli.md)

В этой статье объясняется, как управлять зонами DNS с помощью портала Azure. Зонами DNS также можно управлять с помощью кроссплатформенного [Azure CLI](dns-operations-dnszones-cli.md) или Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Создание зоны DNS

1. Вход на портал Azure
2. В главном меню перейдите в **Создать ресурс > Сети > Зона DNS**, чтобы открыть колонку **Создание зоны DNS**.

    ![Зона DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. В колонке **Создание зоны DNS** введите следующие значения, а затем щелкните **Создать**.


   | **Параметр** | **Значение** | **Дополнительные сведения** |
   |---|---|---|
   |**Имя**|contoso.com|Имя зоны DNS.|
   |**Подписка**|[Ваша подписка]|Выберите подписку для создания зоны DNS.|
   |**Группа ресурсов**|**Создать:** contosoDNSRG|Создайте группу ресурсов. Имя группы ресурсов должно быть уникальным в пределах выбранной подписки. Дополнительные сведения о группах ресурсов см. в разделе [Группы ресурсов](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) статьи "Общие сведения об Azure Resource Manager".|
   |**Местоположение.**|Запад США||

> [!NOTE]
> Этот параметр относится к расположению группы ресурсов и никак не влияет на расположение зоны DNS. Расположение зоны DNS всегда является "глобальным" и не отображается.

## <a name="list-dns-zones"></a>Перечисление зон DNS

На портале Azure последовательно выберите **Больше служб** > **Сети** > **Зоны DNS**. Каждая зона DNS является отдельным ресурсом. В этом представлении отображаются такие сведения, как число наборов записей и серверы имен. Столбец **ИМЕНА СЕРВЕРОВ** не отображается в представлении по умолчанию. Чтобы добавить его, щелкните **Столбцы**, выберите **Имена серверов**, а затем нажмите **Готово**.

![Перечисление зон DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Удаление зоны DNS

Перейдите к зоне DNS на портале. В колонке **Зона DNS** щелкните **Удалить зону**. Затем отобразится запрос на подтверждение удаления зоны DNS. При удалении зоны DNS также удаляются все записи, которые содержатся в ней.

## <a name="next-steps"></a>Дополнительная информация

Сведения о работе с зоной и записями DNS см. в статье [Приступая к работе со службой DNS Azure с помощью портала Azure](dns-getstarted-portal.md).