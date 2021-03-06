---
title: Устранение проблем со входом для подписки Azure
description: Эта статья поможет вам устранить проблемы со входом на портал Azure или в Центр управления учетной записью Azure.
services: azure
author: v-miegge
manager: dcscontentpm
editor: na
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 7b467e42553d992231f8f5d45afcff37f077a9d2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223394"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Устранение проблем со входом для подписки Azure

Это руководство поможет вам устранить проблемы со входом на портал Azure или в Центр управления учетной записью Azure. 

## <a name="issues"></a>Проблемы

### <a name="page-hangs-in-the-loading-status"></a>Страница зависает в состоянии загрузки

Если страница браузера зависает, поочередно выполните все описанные действия, пока не войдете на портал Azure.

- Обновите страницу.
- Воспользуйтесь другим браузером.
- Используйте в браузере режим закрытого просмотра. В Internet Explorer сделайте следующее: Щелкните **Средства** > **Безопасность** > **Просмотр InPrivate**, а затем войдите на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Выполняется автоматический вход от имени другого пользователя

Эта проблема может возникать, если вы используете при работе с браузером несколько учетных записей пользователя.

Для устранения этой проблемы воспользуйтесь одним из указанных ниже способов.

- Очистите кэш и удалите файлы cookie Интернета. В Internet Explorer щелкните **Средства** > **Свойства браузера** > **Удалить**. Убедитесь, что установлены флажки для временных файлов, файлов cookie, паролей и журнала браузера. Затем нажмите кнопку "Удалить".
- Выполните сброс параметров Internet Explorer, чтобы отменить какие-либо персональные настройки. Щелкните **Средства** > **Свойства браузера** > **Дополнительно** > выберите поле **Удалить личные настройки** > **Сброс**.
- Используйте в браузере режим закрытого просмотра. В Internet Explorer сделайте следующее:  Щелкните **Средства** > **Безопасность** > **Просмотр InPrivate**, а затем войдите на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Я могу войти, но вижу сообщение *Подписки не найдены*

Эта проблема возникает, если вы выбрали неправильный каталог или учетная запись не предоставляет необходимых разрешений.

**Сценарий 1.** Сообщение об ошибке получено на [портале Azure](https://portal.azure.com/)

Чтобы устранить эту проблему:

- Убедитесь, что выбран надлежащий каталог Azure, щелкнув учетную запись в правом верхнем углу.
- Если вы выбрали надлежащий каталог Azure, но по-прежнему получаете сообщение об ошибке, [добавьте учетную запись с правами владельца](billing-add-change-azure-subscription-administrator.md).

**Сценарий 2.** Сообщение об ошибке получено в [Центре управления учетной записью Azure](https://account.windowsazure.com/Subscriptions)

Убедитесь, что используете учетную запись администратора учетной записи. Чтобы проверить, кто является администратором учетной записи, сделайте следующее:

1. Войдите в [представление подписок на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Выберите подписку, которую требуется проверить, а затем просмотрите раздел **Параметры**.

3. Выберите **Свойства**. Администратор учетной записи подписки отобразится в поле **Администратор учетной записи** .

## <a name="additional-help-resources"></a>Дополнительные справочные ресурсы

Другие статьи по устранению проблем при выставлении счетов и с подписками Azure

- [Карта не принимается](billing-troubleshoot-declined-card.md)
- [Проблемы с регистрацией подписки](billing-troubleshoot-azure-sign-up.md)
- [Подписки не найдены](billing-no-subscriptions-found.md)
- [Просмотр стоимости Enterprise отключен](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Свяжитесь с нами для получения помощи

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Дополнительная информация

- [Документация по выставлению счетов в Azure](index.md)
