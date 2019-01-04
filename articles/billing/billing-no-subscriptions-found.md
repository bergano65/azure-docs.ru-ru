---
title: Ошибка "Подписки не найдены" при попытке входа на портал Azure | Документация Майкрософт
description: Из этой статьи вы узнаете, как устранить ошибку "Подписки не найдены", которая возникает при входе на портал Azure или в Центр управления учетной записью Azure.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: cwatson
ms.custom: seodec18
ms.openlocfilehash: cef34d4e22732c2d718daf7ce10c23b25ff4649d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081309"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Ошибка "Подписки не найдены" при попытке входа на портал Azure или в Центр управления учетной записью Azure

При попытке войти на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions) может появиться сообщение об ошибке "Подписки не найдены". В этой статье предлагается решение указанной проблемы.

## <a name="symptom"></a>Симптом

При попытке входа на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions) отображается следующее сообщение об ошибке: "Подписки не найдены".

## <a name="cause"></a>Причина:

Эта проблема возникает, если вы выбрали неправильный каталог или учетная запись не предоставляет необходимых разрешений. 

## <a name="solution"></a>Решение

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Сценарий 1. Сообщение об ошибке получено на [портале Azure](https://portal.azure.com)

Чтобы устранить эту проблему:

* Убедитесь, что выбран надлежащий каталог Azure, щелкнув учетную запись в правом верхнем углу.

  ![Выберите каталог в верхней правой части портала Azure](./media/billing-no-subscriptions-found/directory-switch.png)
* Если вы выбрали надлежащий каталог Azure, но по-прежнему получаете сообщение об ошибке, [назначьте учетной записи роль владельца](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Сценарий 2. Сообщение об ошибке получено в [Центре управления учетной записью Azure](https://account.windowsazure.com/Subscriptions)

Убедитесь, что используете учетную запись администратора учетной записи. Чтобы проверить, кто является администратором учетной записи, сделайте следующее:

1. Войдите в [представление подписок на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Выберите подписку, которую требуется проверить, а затем просмотрите раздел **Параметры**.
1. Выберите **Свойства**. Администратор учетной записи подписки отобразится в поле **Администратор учетной записи** .  

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
