---
title: Создание разделов счетов для упорядочения затрат
description: Узнайте, как упорядочить затраты с помощью разделов счетов.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: d70460f5a492c9699a6110d5ba164283934c584b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226130"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Создание разделов счетов для упорядочения затрат

Создайте раздел на вашем счету, чтобы упорядочить затраты по подразделению, среде разработки либо в соответствии с потребностями вашей организации. Потом предоставьте другим разрешение на создание подписок Azure, счета по которым выставляются в разделе. Счета по всем расходам на использование и покупки для подписок будут выставляться в разделе. Вы можете просмотреть общую сумму расходов за раздел в своем счете на портале Azure или просмотреть ее в разделе "Анализ затрат Azure". Подробную информацию см. в [Просмотр транзакций по разделам счетов](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

В этой статье рассматривается учетная запись выставления счетов для Клиентского соглашения Майкрософт. [Проверьте наличие доступа к Клиентскому соглашению Майкрософт](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Создание нового раздела счетов на портале Azure

Чтобы создать раздел счета, вы должны быть **владельцем учетной записи выставления счетов** или **поставщиком учетной записи выставления счетов**. Дополнительные сведения см. в разделе [Управление разделами счетов для профиля выставления счетов](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Войдите на [портале Azure](https://portal.azure.com).

2. Выполните поиск по фразе **Управление затратами + выставление счетов**.

   ![Снимок экрана, показывающий поиск на портале Azure](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Выберите **Разделы счетов** в левой панели. В зависимости от уровня вашего доступа вам, возможно, потребуется выбрать профиль выставления счетов или учетную запись выставления счетов выставления счетов, а затем — **Разделы счетов**.

   ![Снимок экрана, показывающий список разделов счетов](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. В верхней части страницы выберите **Добавить**.

5. Введите имя для раздела счета и выберите профиль счета. Эти разделы отображаются в счете профиля выставления счетов и содержат информацию об использовании для назначенных подписок и приобретенных продуктов. 

   ![Снимок экрана, показывающий страницу создания разделов счетов](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Нажмите кнопку **Создать**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверка доступа к Клиентскому соглашению Майкрософт
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Требуется помощь? Обращение в службу поддержки

Если вам нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.

## <a name="next-steps"></a>Дополнительная информация

- [Создание дополнительной подписки Azure для клиентского соглашения Майкрософт](billing-mca-create-subscription.md)
- [Управление ролями выставления счетов на портале Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Получение запроса права владения на выставление счетов в подписках от пользователей в других учетных записях выставления счетов](billing-mca-request-billing-ownership.md)
