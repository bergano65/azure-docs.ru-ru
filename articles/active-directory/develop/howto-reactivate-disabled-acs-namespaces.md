---
title: Как повторно активировать отключенные пространства имен службы контроля доступа (ACS)
description: Узнайте, как найти и включить пространства имен службы контроля доступа (ACS) и запросить расширение, чтобы они оставались включенными до 4 февраля 2019 года.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 0f0de122dc3dbd770e91a8412430423bee222b30
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577974"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Практическое руководство. Повторная активация отключенных пространств имен службы контроля доступа

В ноябре 2017 года мы объявили, что служба контроля доступа Microsoft Azure (ACS), относящаяся к Azure Active Directory (Azure AD), прекратит функционирование 7 ноября 2018 года.

С тех пор мы отправили письма на электронную почту администратора подписки ACS о прекращении использования ACS за 12 месяцев, 9 месяцев, 6 месяцев, 3 месяца, 1 месяц, 2 недели, 1 неделю и 1 день до даты прекращения поддержки — 7 ноября 2018 года.

3 октября 2018 года мы объявили (по электронной почте и в [записи блога](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) о предложении расширения для клиентов, которые не могут завершить миграцию до 7 ноября 2018 года. В объявлении также содержались указания по запросам расширения.

## <a name="why-your-namespace-is-disabled"></a>Причины отключения пространства имен

Если вы еще не дали согласие на расширение, пространства имен ACS будут отключены начиная с 7 ноября 2018 года. Если вы пропустили сообщение, но все еще хотите дать согласие на расширение до 4 февраля 2019 года, следуйте указаниям в следующих разделах.

> [!NOTE]
> Чтобы запустить команды PowerShell и запросить расширение, необходимо иметь права администратора служб или соадминистратора подписки.

## <a name="find-and-enable-your-acs-namespaces"></a>Поиск и включение пространств имен ACS

Чтобы просмотреть список всех пространств имен ACS и повторно активировать те, которые были отключены, можно использовать ACS PowerShell.

1. Загрузка и установка ACS PowerShell
    1. Перейдите в коллекцию PowerShell и загрузите [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Установите модуль:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Получите список всех возможных команд:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Чтобы получить справку по конкретной команде, выполните следующую команду:

        ```
        Get-Help [Command-Name] -Full
        ```
    
        где `[Command-Name]` — это имя команды ACS.
1. Подключитесь к ACS с помощью командлета **Connect-AcsAccount**. 

    Прежде чем вы сможете выполнить команду, может потребоваться изменить политику выполнения путем выполнения команды **Set-ExecutionPolicy**.
1. Получите список доступных подписок Azure с помощью командлета **Get-AcsSubscription**.
1. Получите список пространств имен ACS с помощью командлета **Get-AcsNamespace**.
1. Удостоверьтесь, что пространства имен отключены. Для этого убедитесь, что для `State` задано значение `Disabled`.

    [![Подтверждение того, что пространства имен отключены](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Кроме того, можно использовать `nslookup {your-namespace}.accesscontrol.windows.net`, чтобы убедиться, что домен по-прежнему активен.

1. Включите пространства имен ACS с помощью командлета **Enable-AcsNamespace**.

    После включения пространств имен можно запросить расширение, чтобы их снова не отключили до 4 февраля 2019 года. После этой даты все запросы к ACS завершатся ошибкой.

## <a name="request-an-extension"></a>Запрос расширения

1. Перейдите на портал управления пространствами имен ACS по адресу `https://{your-namespace}.accesscontrol.windows.net`.
1. Нажмите кнопку **Прочитать условия**, чтобы ознакомиться с [обновленными условиями использования](https://azure.microsoft.com/support/legal/access-control/). При этом вы перейдете на страницу с условиями.

    [![Нажмите кнопку "Прочитать условия"](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. На баннере в верхней части страницы выберите **Запросить расширение**. Эта кнопка станет активна только после прочтения [обновленных условий использования](https://azure.microsoft.com/support/legal/access-control/).

    [![Нажмите кнопку "Запросить расширение"](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. После регистрации запроса расширения страница обновится, и в верхней части экрана появится новый баннер.

    [![Обновленная страница с баннером](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>Справка и поддержка

- Если вы столкнетесь с какими-либо проблемами после выполнения инструкций из этого пошагового руководства, обратитесь в [службу поддержки Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Свяжитесь с нами по адресу acsfeedback@microsoft.com, если у вас есть вопросы или предложения, связанные с прекращением поддержки ACS.

## <a name="next-steps"></a>Дополнительная информация

- Просмотрите сведения о прекращении поддержки ACS в статье [Руководство. Перенос из службы контроля доступа Azure](active-directory-acs-migration.md).
