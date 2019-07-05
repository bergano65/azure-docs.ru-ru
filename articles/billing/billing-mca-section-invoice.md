---
title: Создать разделы на вашем счете, чтобы упорядочить затраты - Azure
description: Узнайте, как упорядочить свои расходы, воспользовавшись разделы счетов.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: eadaf34dc5bdd93af532362e8f8542de3f17f414
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490780"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Создать разделы на вашем счете, чтобы упорядочить свои затраты на

Создать разделы на счета для организации затрат по отделу, среды разработки или исходя из потребностей вашей организации. Затем предоставьте другим пользователям разрешение на создание подписок Azure, за которые выставляется в раздел. Все платежи за использование и покупки для подписок, затем взимается в раздел. Можно просмотреть общие платежи для раздела в ваш счет, на портале Azure, или просмотрите их в анализ затрат Azure. Дополнительные сведения см. в разделе [просматривать транзакции по разделы счетов](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Эта статья относится к учетной записи выставления счетов для клиента соглашение Microsoft. [Проверьте наличие доступа к соглашению клиента Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Создать раздел счета, на портале Azure

Чтобы создать раздел счета, вы должны быть **выставления счетов владельца профиля** или **выставления счетов участник профиля**. Дополнительные сведения см. в разделе [управления разделами счета для профиль выставления счетов](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Войдите на [портале Azure](https://portal.azure.com).

2. Поиск **Cost Management + выставление счетов**.

   ![Снимок экрана, показывающий поиск на портале Azure](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Выберите **выставляется счет разделах** в левой области. В зависимости от вашего доступа необходимо выбрать профиль выставления счетов или учетную запись выставления счетов, а затем выберите **выставляется счет разделах**.

   ![Снимок экрана, показывающий список секций счета](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. В верхней части страницы выберите **Добавить**.

5. Введите имя в разделе счета и выберите профиль выставления счетов. Вы увидите раздел в этот профиль выставления счетов счет отражения использование каждой подписки и покупки, назначенные в разделе. 

   ![Снимок экрана, показывающий страницу создания раздела счета](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Нажмите кнопку **Создать**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверьте доступ к соглашению клиента Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Требуется помощь? Обращение в службу поддержки

Если вам нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.

## <a name="next-steps"></a>Дальнейшие действия

- [Создайте дополнительную подписку Azure для клиентов соглашения Microsoft](billing-mca-create-subscription.md)
- [Предоставить другим пользователям разрешение на создание подписки Azure](billing-mca-create-subscription.md#give-others-permission)
- [Получение прав владения выставлением счетов для подписок Azure от пользователей в других выставления счетов](billing-mca-request-billing-ownership.md)
