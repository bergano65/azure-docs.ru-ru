---
title: Просмотр резервирований для ресурсов Azure | Документация Майкрософт
description: Узнайте, как просматривать резервирования на портале Azure.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 21bf96866c14615009a17279ff2fdd04bf4116ad
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490269"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Просмотр резервирований Azure на портале Azure

В зависимости от вашей подписки и разрешений существует несколько способов просматривать резервирования Azure.

## <a name="view-purchased-reservations"></a>Просмотр приобретенных резервирований

Если вы покупаете резервирование, вы и администратор можете просматривать резервирование по умолчанию. Вместе с администратором учетной записи вы можете получить роль владельца заказа на резервирование и самого резервирования. Чтобы разрешить другим просматривать резервирование, назначьте им роль **владельца** или **читателя** в заказе на резервирование или самом резервировании.

Дополнительные сведения см. в разделе [Добавление или изменение пользователей, которые могут управлять резервированием](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Чтобы просмотреть резервирование как владелец или читатель, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выполните поиск по слову **резервирования**.
    ![Снимок экрана: поиск на портале Azure](./media/billing-view-reservation/portal-reservation-search.png)  
3. Появится список резервирований, в котором можно выбрать роль владельца или читателя. Каждое резервирование показывает последний известный показатель использования в процентах.  
    ![Пример: список резервирований](./media/billing-view-reservation/view-reservations.png)
4. Выберите резервирование и просмотрите тенденцию использования за последние пять дней.  
    ![Пример: тенденция использования резервирования](./media/billing-view-reservation/reservation-utilization.png)
5. Вы также можете получить сведения об [использовании резервирования](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) с помощью API использования зарезервированного экземпляра или [пакета содержимого Microsoft Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

Если вам нужно изменить область резервирования, разделить резервирование или изменить тех, кто могут управлять резервированием, см. статью [Управление резервированиями для ресурсов Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Просмотр транзакций резервирования для Соглашений о регистрации Enterprise

 Если у вас есть партнерские соглашения о регистрации Enterprise, просматривайте резервирования, перейдя к**отчетам** на портале EA. Для других соглашений о регистрации Enterprise можно просмотреть резервирования на портале EA и на портале Azure. Только администраторы EA могут просматривать транзакции резервирований.

Чтобы просмотреть транзакции резервирования на портале Azure, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по фразе **Управление затратами + выставление счетов**.

    ![Снимок экрана, показывающий поиск на портале Azure](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Выберите **Транзакции резервирования**.
1. Чтобы отфильтровать результаты, выберите **Временной диапазон**, **Тип** или **Описание**.
1. Нажмите кнопку **Применить**.

    ![Снимок экрана, показывающий результаты в окне "Транзакции резервирования"](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Чтобы получить данные с помощью API, см. статью [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Получение сведений о расходах по транзакциям зарезервированных экземпляров для корпоративных клиентов).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о резервировании в Azure см. в следующих статьях:

- [Сведения о резервированиях для Azure](billing-save-compute-costs-reservations.md)
- [Управление резервированиями для Azure](billing-manage-reserved-vm-instance.md)

Приобретение плана обслуживания:

- [Предоплата ресурсов Azure Cosmos DB с резервной мощностью](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Предоплата вычислительных ресурсов Базы данных SQL Azure с получением резервной мощности](../sql-database/sql-database-reserved-capacity.md)
- [Предоплата виртуальных машин с помощью Azure Reserved Virtual Machine Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Приобретение плана программного обеспечения:

- [Предварительная оплата планов программного обеспечения Red Hat в резервированиях Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Предварительная оплата программного обеспечения SUSE в резервированиях Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Основные сведения об использовании:

- [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](billing-understand-reserved-instance-usage.md)
- [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Продажа Microsoft Azure Reserved VM Instances](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).
