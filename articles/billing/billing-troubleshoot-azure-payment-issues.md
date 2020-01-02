---
title: Устранение проблем с оплатой Azure
description: Устранение проблемы при обновлении учетной записи сведений об оплате на портале Microsoft Azure или в центре учетных записей.
services: azure
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: b449dbf455807aab8436f42c25f21f3eea43a678
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225853"
---
# <a name="troubleshoot-azure-payment-issues"></a>Устранение проблем с оплатой Azure

Вы можете столкнуться с проблемой или ошибкой при обновлении учетной записи сведений об оплате на портале Microsoft Azure или в центре учетных записей.

Чтобы устранить проблему, выберите раздел ниже, содержимое в котором сильнее всего похоже на вашу ошибку.

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Не удалось удалить кредитную карту из сохраненного метода оплаты счетов

Кредитную карту нельзя удалить из активной подписки. Это сделано специально.

Если необходимо удалить существующую карту, необходимо добавить новую карту в подписку, чтобы старое средство оплаты можно было успешно удалить, или отменить подписку. Это приведет к окончательному удалению подписки и карточки.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Не удалось удалить старый метод оплаты после добавления нового

Новое платежное средство может не быть связано с подпиской. Чтобы связать платежное средство с подпиской, см. раздел [Add, update, or remove a credit card for Azure](billing-how-to-change-credit-card.md) (Добавление, обновление или удаление кредитной карты для Azure).

Информацию об устранении неполадок, связанных с отклоненной картой, см. в разделе [How to troubleshoot a declined card at Azure sign-up](billing-troubleshoot-declined-card.md) (Устранение неполадок с отклоненной картой при регистрации в Azure).

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Не удалось удалить метод оплаты из-за ошибки *Не удается удалить метод оплаты*

Это происходит из-за задолженности. Погасите все задолженности, прежде чем удалять метод оплаты.

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Не удалось просмотреть подписки в учетной записи для изменения метода оплаты

Возможно, вы используете идентификатор электронной почты, отличающийся от того, который используется для подписок.

Для устранения этой проблемы см. [No subscriptions found sign-in error for Azure portal or Azure account center](billing-no-subscriptions-found.md) (Ошибка "Подписки не найдены" при попытке входа на портал Azure или в Центр управления учетной записью Azure).

## <a name="unable-to-make-payment-for-a-subscription"></a>Не удается выполнить оплату для подписки

При появлении ошибки: *Платеж просрочен. Возникла проблема с методом оплаты,* или *не удалось сохранить информацию. Закройте браузер и повторите попытку.* , после чего на карте появится ожидающий оплаты платеж, поскольку ваше финансовое учреждение отказало в выдаче карты.

Убедитесь, что кредитная карта имеет достаточный баланс для выполнения платежа. Если это не так, используйте другую карту для выполнения оплаты или обратитесь к финансовому учреждению, чтобы решить проблему.

Свяжитесь со своим банком по следующим вопросам:

- Международные транзакции не включены.
- У карты есть кредитный лимит, и остаток должен быть оплачен.
- На карте разрешены регулярные платежи.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Не удалось изменить метод оплаты из-за проблем с браузером (браузер не отвечает, не загружается и т. д.)

Выйдите из всех активных сеансов Azure, а затем выполните шаги, описанные в статье [Просмотр в режиме InPrivate в Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate), чтобы начать сеанс в режиме InPrivate в Microsoft Edge или Internet Explorer.

Во время закрытого сеанса работы выполните действия, описанные в пункте [Как изменить кредитную карту](billing-how-to-change-credit-card.md), чтобы обновить или изменить данные о кредитной карте.

Также можно выполнить следующие действия:

- Обновить страницу в браузере
- Использовать другой браузер
- Удалить кэшированные файлы cookie

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Моя подписка по-прежнему отключена после обновления метода оплаты.

Это происходит из-за задолженности. Погасите все задолженности, прежде чем удалять метод оплаты.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Не удалось изменить метод оплаты из-за страницы ответа "Ошибка XML"

Это сообщение появляется, если вы используете [портал Azure](https://portal.azure.com/) для добавления новой кредитной карты.

Чтобы добавить данные карты, войдите на портал управления учетными записями Azure, указав адрес электронной почты администратора учетной записи.

## <a name="additional-help-resources"></a>Дополнительные справочные ресурсы

Другие статьи по устранению проблем при выставлении счетов и с подписками Azure

- [Карта не принимается](billing-troubleshoot-declined-card.md)
- [Проблемы со входом в подписку](billing-troubleshoot-sign-in-issue.md)
- [Подписки не найдены](billing-no-subscriptions-found.md)
- [Просмотр стоимости Enterprise отключен](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Свяжитесь с нами для получения помощи

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Дополнительная информация

- [Документация по выставлению счетов в Azure](index.md)
