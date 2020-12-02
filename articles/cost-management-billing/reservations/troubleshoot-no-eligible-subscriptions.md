---
title: Устранение неполадок в случае подходящих подписок на портале Azure
description: Эта статья поможет вам устранить неполадки с сообщением об ошибке "Нет подходящих подписок" на портале Azure при попытке приобретения резервирования.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.openlocfilehash: 1b36577c3c0940687f98394f8ea4faae83f371be
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887201"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Устранение неполадок с неподходящими подписками

Эта статья поможет вам устранить неполадки с сообщением об ошибке *Нет подходящих подписок* на портале Azure при попытке приобретения резервирования.

## <a name="symptoms"></a>Симптомы

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к разделу **Резервирования**.
1. Выберите **Добавить**, а затем выберите службу.
1. Отображается следующее сообщение об ошибке:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. В области **Select the product you want to purchase** (Выбор приобретаемого продукта) разверните список **Подписка для выставления счетов**, чтобы увидеть причину, по которой для конкретной подписки не удается приобрести зарезервированный экземпляр. На следующем рисунке показаны примеры того, почему не удается приобрести резервирование.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Пример, в котором показано, почему не удается купить резервирование" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Причина

Чтобы купить зарезервированный экземпляр Azure, необходимо иметь по крайней мере одну подписку, которая соответствует следующим требованиям.

- Подписка должна быть поддерживаемым типом предложения. Поддерживаются следующие типы предложений. Оплата по мере использования, поставщик облачных решений (CSP), Microsoft Azure Enterprise или Клиентское соглашение Майкрософт.
- В подписке у вас должна быть роль владельца или покупателя резервирования.

Если у вас нет подписки, удовлетворяющей требованиям, вы получите ошибку `No eligible subscriptions`.

### <a name="cause-1"></a>Причина 1

Подписка должна быть поддерживаемым типом предложения. Поддерживаются следующие типы предложений. Оплата по мере использования, поставщик облачных служб, Microsoft Azure Enterprise или Клиентское соглашение Майкрософт. Ваш тип подписки не поддерживается. При выборе подписки с типом предложения, который не поддерживает резервирование, отображается следующая ошибка.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Пример с отображением сообщения об ошибке подписки, которая не подходит для покупки" :::

### <a name="cause-2"></a>Причина 2

В подписке у вас должна быть роль владельца или покупателя резервирования. Если у вас нет нужных разрешений, вы увидите следующую ошибку.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Решение

- Если ваше текущее предложение не поддерживает резервирования, необходимо создать новую подписку Azure.
- Если у вас нет доступа к существующему резервированию, вы можете получить к нему доступ у текущего владельца.

### <a name="solution-1"></a>Решение 1

Чтобы приобрести резервирование, необходимо создать новую подписку Azure, которая поддерживает резервирование.

- Если у вас бесплатная пробная версия Azure, можно [обновить подписку](../manage/upgrade-azure-subscription.md).
- Вы можете создать новую подписку Azure, используя [тарифы с оплатой по мере использования](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Зарегистрируйтесь в [Клиентском соглашении Майкрософт](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) и создайте новую подписку.
- Приобретите новую подписку с помощью [CSP](https://www.microsoft.com/solution-providers/home) и создайте новую подписку.

### <a name="solution-2"></a>Решение 2

Чтобы получить доступ владельца к резервированию, необходимо получить доступ к одному из следующих компонентов.

- Заказ на резервирование, с которым было приобретено резервирование
- Само резервирование

Текущий владелец заказа на резервирование или владелец резервирования может делегировать вам доступ, выполнив следующие операции.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **Все службы** > **Резервирование**, чтобы отобразить список доступных вам резервирований.
1. Выберите резервирование, доступ к которому требуется делегировать другим пользователям.
1. Выберите **Управление доступом (IAM)** .
1. Выберите **Добавить назначение ролей** > **Роль** > **Владелец**. Если вы хотите предоставить ограниченный доступ, выберите другую роль.
1. Укажите адрес электронной почты пользователя, для которого хотите добавить роль "Владелец".
1. Выберите пользователя, а затем щелкните **Сохранить**.

Дополнительные сведения см. в разделе [Добавление или изменение пользователей, которые могут управлять резервированием](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="next-steps"></a>Дальнейшие шаги

- Если нужно, чтобы владелец резервирования предоставил вам доступ к резервированию, см. статью [Добавление или изменение пользователей, которые могут управлять резервированием](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
