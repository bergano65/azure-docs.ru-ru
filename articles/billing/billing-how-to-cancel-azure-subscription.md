---
title: Отмена подписки Azure | Документация Майкрософт
description: В этой статьей объясняется, как отменить подписку Azure, например бесплатную пробную версию подписки.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/23/2019
ms.author: banders
ms.openlocfilehash: 374224663323e75a1d1413c47490bd3aacd00267
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876822"
---
# <a name="cancel-your-subscription-for-azure"></a>Отмена подписки в Azure

[Администратор учетной записи](billing-subscription-transfer.md#whoisaa) может отменить подписку Azure. После отмены подписки вы больше не сможете получать доступ к службам и ресурсам Azure.

Прежде чем отменить подписку, сделайте следующее:

* Создайте резервную копию данных. Например, если вы храните данные в хранилище Azure или SQL, скачайте копию. При наличии виртуальной машины сохраните ее образ локально.
* Завершите работу служб. Перейдите на [страницу ресурсов на портале управления](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) и выберите команду **Остановить**, чтобы остановить работу всех запущенных виртуальных машин, приложений или других служб.
* Перенесите данные. Ознакомьтесь со статьей [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Перемещение ресурсов в новую группу ресурсов или подписку).

Если вы отмените подписку на платный план поддержки Azure, вам по-прежнему будут выставляться счета за оставшуюся часть срока действия подписки. Дополнительные сведения см. на странице [Планы поддержки Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Отмена подписки с помощью портала Azure

1. Выберите свою подписку на [странице подписок](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) на портале Azure.
2. Выберите подписку, которую нужно отменить.
3. Выберите **Обзор**, а затем — **Отменить подписку**.

    ![Снимок экрана, на котором показана кнопка "Отмена"](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Следуйте инструкциям и завершите отмену.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Что происходит после отмены подписки?

После отмены подписки немедленно прекратится выставление счетов. Но отображение отмены на портале может занять до 10 минут.

После этого службы будут отключены. Это означает, что виртуальные машины и временные IP-адреса освобождаются, а хранилище становится доступным только для чтения.

Если вы отменяете подписку в середине расчетного периода, мы отправим вам итоговый счет в день расчета после истечения периода тарификации. 

Мы окончательно удаляем данные только через 90 дней, обеспечивая возможность доступа к ним, если вы измените свое решение. Мы не взимаем плату за хранение данных. Дополнительные сведения см. в разделе [What happens to your data if you leave the service](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Что происходит с данными, когда вы прекращаете использовать службу).

## <a name="reactivate-subscription"></a>Повторная активация подписки

Если вы случайно отменили подписку с оплатой по мере использования, можно [повторно активировать ее в Центре управления учетной записью](billing-subscription-become-disable.md).

Если у вас подписка не по мере использования, обратитесь в службу поддержки в течение 90 дней после отмены подписки для повторной активации.

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или нужна помощь, [создать запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).
