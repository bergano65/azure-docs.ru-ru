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
ms.author: banders
ms.openlocfilehash: bf18d845b7128c8d6f740555f1a0f791767240ae
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650229"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Просмотреть резервирование Azure на портале Azure

В зависимости от типа подписки и разрешения существует несколько способов просмотреть резервирования для Azure.

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о резервировании в Azure см. в следующих статьях:

- [Что такое резервирования для Azure?](billing-save-compute-costs-reservations.md)
- [Управление резервирования для Azure](billing-manage-reserved-vm-instance.md)

Купите план обслуживания:

- [Предоплата ресурсов Azure Cosmos DB с резервной мощностью](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Предоплата вычислительных ресурсов Базы данных SQL Azure с получением резервной мощности](../sql-database/sql-database-reserved-capacity.md)
- [Предоплата виртуальных машин с помощью Azure Reserved Virtual Machine Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Приобрести план программного обеспечения:

- [Оплатите Red Hat планы программного обеспечения из Azure резервирования](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Предварительная оплата программного обеспечения SUSE в резервированиях Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Общие сведения об использовании:

- [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](billing-understand-reserved-instance-usage.md)
- [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Продажа Microsoft Azure Reserved VM Instances](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами

Если у вас есть вопросы или нужна помощь, [создать запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).
