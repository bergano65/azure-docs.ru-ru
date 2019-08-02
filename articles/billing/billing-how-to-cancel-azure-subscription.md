---
title: Отмена подписки Azure | Документация Майкрософт
description: В этой статьей объясняется, как отменить подписку Azure, например бесплатную пробную версию подписки.
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666435"
---
# <a name="cancel-your-azure-subscription"></a>Отмена подписки Azure

Вы можете отменить подписку Azure в портал Azure, если подписка больше не нужна. 

Прежде чем отменить подписку, сделайте следующее:
* Создайте резервную копию данных. Например, если вы храните данные в хранилище Azure или SQL, скачайте копию. При наличии виртуальной машины сохраните ее образ локально.
* Завершите работу служб. Перейдите на [страницу ресурсов на портале управления](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) и выберите команду **Остановить**, чтобы остановить работу всех запущенных виртуальных машин, приложений или других служб.
* Перенесите данные. Ознакомьтесь со статьей [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Перемещение ресурсов в новую группу ресурсов или подписку).
* Удалите все ресурсы и все группы ресурсов. Для отмены подписки необходимо удалить их. Каждая группа ресурсов должна удаляться по отдельности. Во время удаления группы ресурсов необходимо подтвердить удаление, введя имя группы ресурсов.
* Если у вас есть пользовательские роли, которые ссылаются на эту `AssignableScopes`подписку в, необходимо обновить эти пользовательские роли, чтобы удалить подписку. При попытке обновить пользовательскую роль после отмены подписки может возникнуть ошибка. Дополнительные сведения см. в статье Устранение неполадок [с пользовательскими ролями](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) и [пользовательскими ролями для ресурсов Azure](../role-based-access-control/custom-roles.md).

Если вы отменили платный план поддержки Azure, вам будет выставлен счет за остальную часть условия подписки. Дополнительные сведения см. на странице [Планы поддержки Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Отмена подписки в портал Azure

1. Выберите подписку на [странице "подписки" в портал Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Выберите подписку, которую нужно отменить.
3. Выберите **Обзор**, а затем — **Отменить подписку**.
    ![Снимок экрана, на котором показана кнопка "Отмена"](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Следуйте инструкциям и завершите отмену.


## <a name="who-can-cancel-a-subscription"></a>Кто может отменить подписку?

В следующей таблице описывается разрешение, необходимое для отмены подписки.

|Тип подписки     |Кто может отменить  |
|---------|---------|
|Подписки, созданные при регистрации в Azure с помощью веб-сайта Azure. Например, при регистрации в [бесплатной учетной записи Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) [учетная запись с оплатой по мере](https://azure.microsoft.com/offers/ms-azr-0003p/) использования или как [подписчик Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Администратор учетной записи, владельцы и участники подписки  |
|[Разработка и тестирование](https://azure.microsoft.com/offers/ms-azr-0148p/) [Microsoft Соглашение Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) и Enterprise     |  Владелец учетной записи, владельцы и участники подписки       |
|[План Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) и [план Azure для DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Владельцы и участники подписки      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Что происходит после отмены подписки?

После отмены выставление счетов немедленно прекращается. Но отображение отмены на портале может занять до 10 минут. При отмене в середине расчетного периода мы отправляем окончательный счет на обычную дату счета после окончания периода.

После отмены службы будут отключены. Это означает, что виртуальные машины и временные IP-адреса освобождаются, а хранилище становится доступным только для чтения.

Мы окончательно удаляем данные только через 90 дней, обеспечивая возможность доступа к ним, если вы измените свое решение. Мы не взимаем плату за хранение данных. Дополнительные сведения см. в разделе [What happens to your data if you leave the service](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Что происходит с данными, когда вы прекращаете использовать службу).

## <a name="reactivate-subscription"></a>Повторная активация подписки

Если вы случайно отменили подписку с оплатой по мере использования, вы можете [повторно активировать ее в центре управления учетными записями](billing-subscription-become-disable.md).

Если ваша подписка не является подпиской с оплатой по мере использования, обратитесь в службу поддержки в течение 90 дней с момента отмены для повторной активации подписки.

## <a name="need-help-contact-us"></a>Нужна помощь? Свяжитесь с нами.

Если у вас возникли вопросы или вам нужна помощь, [Создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).
