---
title: Просмотр резервирований для ресурсов Azure | Документация Майкрософт
description: Узнайте, как просматривать резервирования на портале Azure.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2018
ms.author: cwatson
ms.openlocfilehash: c7522076987aacacc6fde6a0c9d2fa867a3f14aa
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314046"
---
# <a name="view-reservations-for-azure-in-the-azure-portal"></a>Просмотр резервирований на портале Azure

В зависимости от вашей подписки и разрешений существует несколько способов просмотра резервирований ресурсов Azure.

## <a name="view-reservations-as-owner-or-reader"></a>Просмотр резервирований в качестве владельца или читателя

Если вы покупаете резервирование, вы и администратор можете просматривать резервирование по умолчанию. Вместе с администратором учетной записи вы можете получить роль владельца резервирования. Чтобы позволить другим просматривать резервирование, требуется добавить их в качестве **владельца** или **читателя** резервирования. Дополнительные сведения см. в разделе [Добавление или изменение пользователей, которые могут управлять резервированием](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
 
Чтобы просмотреть резервирование как владелец или читатель, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по **Резервированиям**.

    ![Снимок экрана, показывающий поиск на портале Azure](./media/billing-view-reservation/portal-reservation-search.png)

1. Появится список резервирований, в котором можно выбирать роль владельца или читателя.

Если вам нужно изменить область резервирования, разделить резервирование или изменить тех, кто могут управлять резервированием, см. статью [Управление резервированиями для ресурсов Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Просмотр транзакций резервирования для Соглашений о регистрации Enterprise

 Если у вас есть партнерские соглашения о регистрации Enterprise, просматривайте резервирования, перейдя к**отчетам** на портале EA. Для других соглашений о регистрации Enterprise можно просмотреть резервирования на портале EA и на портале Azure. Только администраторы EA могут просматривать транзакции резервирований.

Чтобы просмотреть транзакции резервирования на портале Azure, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по **Управление затратами + выставление счетов**.

    ![Снимок экрана, показывающий поиск на портале Azure](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Выберите **Транзакции резервирования**.
1. Чтобы отфильтровать результаты, выберите **Временной диапазон**, **Тип** или **Описание**.
1. Нажмите кнопку **Применить**.

    ![Снимок экрана, показывающий результаты в окне "Транзакции резервирования"](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Чтобы получить данные с помощью API, см. статью [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Получение сведений о расходах по транзакциям зарезервированных экземпляров для корпоративных клиентов).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о резервировании в Azure см. в следующих статьях:

- [Основные сведения о резервировании в Azure](billing-save-compute-costs-reservations.md)
- [Предоплата ресурсов Azure Cosmos DB с резервной мощностью](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Предоплата вычислительных ресурсов Базы данных SQL Azure с получением резервной мощности](../sql-database/sql-database-reserved-capacity.md)
- [Предоплата виртуальных машин с помощью Azure Reserved Virtual Machine Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Управление зарезервированными экземплярами в Azure](billing-manage-reserved-vm-instance.md)
- [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](billing-understand-reserved-instance-usage.md)
- [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Продажа Microsoft Azure Reserved VM Instances](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
