---
title: Предельная сумма расходов Azure | Документация Майкрософт
description: В этой статье описывается, как работает предельная сумма расходов Azure и как ее удалить.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 0983e21e5ab2e895b2c12fe7cc45c5b9c9f0a405
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223581"
---
# <a name="azure-spending-limit"></a>Предельная сумма расходов Azure

Предельная сумма расходов в Azure предотвращает расходы, превышающие суммы по кредиту. Для всех новых клиентов, зарегистрированных с помощью бесплатной учетной записи Azure или типов подписки, включающих кредиты на несколько месяцев, ограничение расходов включено по умолчанию. Предельная сумма расходов равна сумме кредита и ее невозможно изменить. Например, если вы зарегистрировались с помощью бесплатной учетной записи Azure, предельная сумма расходов составляет 200 долларов и вы не сможете изменить ее на 500 долларов. Однако, предельную сумму расходов можно удалить. Таким образом, у вас либо не будет установлена предельная сумма расходов, либо она будет равна сумме кредита. Так вы избегаете большинства видов расходов. Предельная сумма расходов недоступна для подписок с планами обязательств или с оплатой по мере использования. См.[полный список типов подписки Azure и доступной предельной суммы расходов](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Достижение предельной суммы расходов

Если вы превысили сумму расходов, предусмотренную для вашей подписки, ваши развернутые службы отключаются до конца расчетного периода.

Например, если вы использовали всю сумму кредитов для вашей бесплатной учетной записи Azure, развернутые ресурсы Azure удаляются из производства, а виртуальные машины Azure останавливаются и выделенные для них ресурсы освобождаются. Данные в учетных записях хранения доступны только для чтения.

Если для вашей подписки предусмотрены кредиты на несколько месяцев, то в начале следующего расчетного периода ваша подписка будет снова активирована автоматически. После этого вы сможете повторно развернуть ресурсы Azure и получить полный доступ к своим учетным записям хранения и базам данных.

При достижении предельной суммы расходов Azure отправляет уведомления по электронной почте. Войдите на [портал Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), чтобы просмотреть уведомления о подписках, для которых достигнута предельная сумма расходов.

Если вы зарегистрировались с помощью бесплатной учетной записи Azure и достигли предельной суммы расходов, вы можете перейти на тарификацию [с оплатой по мере использования](billing-upgrade-azure-subscription.md), чтобы удалить предельную сумму расходов и автоматически включить подписку.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>Снятие ограничения предельной суммы расходов на портале Azure

Вы можете снять ограничение предельной суммы расходов в любой момент при условии, что для вашей подписки Azure указан допустимый метод оплаты. Для типов подписки с кредитом на несколько месяцев, таким как Visual Studio Enterprise и Visual Studio Professional, можно также включить предельную сумму расходов в начале следующего периода выставления счетов.

Чтобы снять ограничение, выполните следующие действия:

1. Войдите на [портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Выберите подписку. После достижения предельной суммы расходов подписка будет отключена.
1. Выберите **Снять ограничение предельной суммы расходов** в верхней части страницы.
1. Выберите нужный вам вариант.

![Выбор параметра для удаления предельной суммы расходов](./media/billing-spending-limit/remove-spending-limit.PNG)

| Параметр | Результат |
| --- | --- |
| Удалить предельную сумму расходов на неопределенный срок | Удаление предельной суммы расходов без ее автоматического включения в начале следующего расчетного периода. |
| Удалить предельную сумму расходов для текущего расчетного периода | Удаление предельной суммы расходов с ее автоматическим включением в начале следующего расчетного периода. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Возможные причины удаления предельной суммы расходов

Из-за установленной предельной суммы расходов у вас могут возникнуть проблемы с развертыванием или использованием некоторых сторонних служб, а также служб Майкрософт. Ниже описаны ситуации, в которых в подписке следует удалить ограничение на предельную сумму расходов.

-  Вы планируете развернуть образы от таких поставщиков, как Oracle, или служб, как Azure DevOps Services. Эта ситуация приводит к достижению предельной суммы расходов, которая приводит к отключению подписки.
- У вас есть службы, которые вы не хотите прерывать. После достижения предельной суммы расходов развернутые ресурсы Azure удаляются из производства, а виртуальные машины Azure останавливаются и выделенные для них ресурсы освобождаются. Если у вас есть службы, которые не следует прерывать, снимите ограничение предельной суммы расходов.
- У вас есть службы или ресурсы с определенными параметрами (например, виртуальные IP-адреса), которые вы не хотите терять. Эти настройки сбрасываются при достижении предельной суммы расходов, а выделенные для них ресурсы и службы освобождаются.

## <a name="turn-on-the-spending-limit-after-removing"></a>Включение ограничения предельной суммы расходов после удаления

Эта функция доступна, только если предельная сумма расходов была удалена на неопределенный срок для типов подписки, которые включают кредиты на несколько месяцев. Эту функцию можно использовать для автоматического включения предельной суммы расходов в начале следующего периода выставления счетов.

1. Войдите в [Центр управления учетной записью](https://account.windowsazure.com/Subscriptions).
1. Щелкните желтый баннер, чтобы изменить настройку для предельной суммы расходов.
1. Выберите **Включить функцию предельной суммы расходов для следующего периода выставления счетов \<дата начала расчетного периода\>** .

## <a name="custom-spending-limit"></a>Настраиваемая предельная сумма расходов

Пользовательские предельные суммы расходов недоступны.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Предельная сумма расходов не ограничивает все расходы

[Некоторые внешние службы, опубликованные в Azure Marketplace,](billing-understand-your-azure-marketplace-charges.md) используются отдельно от кредитов подписок. Работа с ними может дополнительно тарифицироваться, даже если установлена предельная сумма расходов. Примером таких служб могут быть лицензии на Visual Studio, служба Azure Active Directory Premium, планы поддержки и большинство сторонних служб. При подготовке новой внешней службы или ресурса отображается предупреждение о дополнительной тарификации:

![Предупреждение о покупке Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дополнительная информация
- Обновить до плана цены [с оплатой по мере использования](billing-upgrade-azure-subscription.md).
