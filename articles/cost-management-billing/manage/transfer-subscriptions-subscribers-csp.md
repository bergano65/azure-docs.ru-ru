---
title: Перенос подписок Azure между подписчиками и CSP
description: Сведения о переносе подписок Azure между подписчиками и CSP.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: banders
ms.openlocfilehash: 4e7e9ea61d74eb38f3c225d66a39906bc18fe6d2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200526"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Перенос подписок Azure между подписчиками и CSP

Эта статья содержит общие действия, которые используются для переноса подписок Azure между партнерами поставщика облачных решений (CSP) и их клиентами.

## <a name="transfer-ea-subscriptions"></a>Перенос подписок EA

Партнеры по прямым счетам CSP, сертифицированные как [Поставщик управляемых служб Azure Expert](https://partner.microsoft.com/membership/azure-expert-msp), могут запрашивать перенос подписок Azure для клиентов с прямым Соглашением Enterprise (EA). Перенос подписок разрешен только для клиентов, которые приняли Клиентское соглашение Майкрософт (MCA) и приобрели план Azure.

После утверждения запроса CSP может предоставить своим клиентам объединенный счет. Дополнительные сведения о передаче подписок с помощью CSP см. в разделе [Get billing ownership of Azure subscriptions to your MPA account](mpa-request-ownership.md) (Получение права владения на выставление счетов в подписках Azure).

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Передача другой подписки партнеру CSP

Чтобы перенести любые другие подписки Azure партнеру CSP, подписчику необходимо переместить ресурсы из исходных подписок в подписки CSP. Используйте следующие рекомендации для перемещения ресурсов между подписками.

1. Обратитесь к своему партнеру CSP, чтобы создать целевые подписки Azure CSP.
1. Убедитесь, что исходная и целевая подписки CSP находятся в одном арендаторе Azure Active Directory (Azure AD).  
    Арендатор Azure AD для подписки Azure CSP изменить нельзя. Вместо этого необходимо добавить исходную подписку или связать ее с клиентом Azure AD CSP. Дополнительные сведения см. в статье [Связывание или добавление подписки Azure в клиент Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - При связывании подписки с другим каталогом Azure AD пользователи, которым назначены роли с помощью [управления доступом на основе ролей](../../role-based-access-control/role-assignments-portal.md) теряют доступ. Администраторы классической подписки, включая администратора служб и соадминистраторов, также теряют доступ.
    > - Назначения политик также удаляются из подписки, если подписка связана с другим каталогом.
1. Учетная запись пользователя, используемая для перемещения, должна иметь [RBAC](add-change-subscription-administrator.md) доступ владельца для обеих подписок.
1. Прежде чем начать, [проверьте](/rest/api/resources/resources/validatemoveresources), могут ли перемещаться ресурсы Azure из исходной подписки в целевую.  
    Некоторые ресурсы Azure не могут перемещаться между подписками. Полный список ресурсов Azure, которые можно переместить, см. в статье [Move operation support for resources](../../azure-resource-manager/management/move-support-resources.md) (Поддержка операции перемещения для ресурсов).
    > [!IMPORTANT]
    >  - Azure CSP поддерживает только ресурсы Azure Resource Manager. Если какие бы то ни было ресурсы Azure в исходной подписке созданы с помощью классической модели развертывания Azure, перед миграцией их необходимо перенести в [Azure Resource Manager](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm). Для просмотра веб-страницы необходимо быть партнером.

1. Убедитесь, что все службы исходной подписки используют модель Azure Resource Manager. Затем перенесите ресурсы из исходной подписки в целевую с помощью [Перемещения ресурсов Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Перемещение ресурсов Azure между подписками, в зависимости от ресурсов в подписках, может привести к простою службы.

## <a name="all-subscription-transfers-from-a-csp-partner"></a>Все переносы подписок из Партнера CSP

Чтобы перенести любые другие подписки из предложения "Партнер CSP" в любое другое предложение Azure, подписчику необходимо переместить ресурсы между исходными подписками CSP и целевыми подписками.

1. Создайте целевые подписки Azure.
1. Убедитесь, что исходная и целевая подписки находятся в одном арендаторе Azure Active Directory (Azure AD). Дополнительные сведения об изменении клиента Azure AD см. в разделе [Associate or add an Azure subscription to your Azure Active Directory tenant](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) (Связывание или добавление подписки Azure в клиент Azure Active Directory).

    > [!IMPORTANT]
    >  - При связывании подписки с другим каталогом, пользователи, которым назначены роли с помощью [управления доступом на основе ролей](../../role-based-access-control/role-assignments-portal.md), теряют доступ. Администраторы классической подписки, включая администратора служб и соадминистраторов, также теряют доступ.
    >  - Назначения политик также удаляются из подписки, если подписка связана с другим каталогом.

1. Учетная запись пользователя, используемая для перемещения, должна иметь [RBAC](add-change-subscription-administrator.md) доступ владельца для обеих подписок.
1. Прежде чем начать, [проверьте](/rest/api/resources/resources/validatemoveresources), могут ли перемещаться ресурсы Azure из исходной подписки в целевую.
    > [!IMPORTANT]
    >  - Некоторые ресурсы Azure не могут перемещаться между подписками. Полный список ресурсов Azure, которые можно переместить, см. в статье [Move operation support for resources](../../azure-resource-manager/management/move-support-resources.md) (Поддержка операции перемещения для ресурсов).

1. Перенесите ресурсы из исходной подписки в целевую подписку с помощью [Перемещения ресурсов Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Перемещение ресурсов Azure между подписками, в зависимости от ресурсов в подписках, может привести к простою службы.

## <a name="next-steps"></a>Дальнейшие действия
- [Get billing ownership of Azure subscriptions for your MPA account](mpa-request-ownership.md) (Запрос прав владения подписками Azure для учетной записи MPA).
- Дополнительные сведения см. [Управление учетными записями и подписками с помощью API выставления счетов Azure](index.yml).
