---
title: Block legacy authentication - Azure Active Directory
description: Learn how to improve your security posture by blocking legacy authentication using Azure AD Conditional Access.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a65145fe9752a90e3328c308ce603c8626d8708
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380857"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>How to: Block legacy authentication to Azure AD with Conditional Access   

Чтобы предоставить пользователям удобный доступ к облачным приложениям, Azure Active Directory (AAD) поддерживает широкий набор протоколов проверки подлинности, часть из которых уже устарела. Проблема заключается в том, что устаревшие протоколы не поддерживают многофакторную проверку подлинности (MFA). Во многих средах MFA является стандартным методом защиты от кражи идентификаторов. 

If your environment is ready to block legacy authentication to improve your tenant's protection, you can accomplish this goal with Conditional Access. This article explains how you can configure Conditional Access policies that block legacy authentication for your tenant.

## <a name="prerequisites"></a>Технические условия

В данной статье предполагается, что вы знакомы с: 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>Описание сценария

AAD поддерживает несколько широко распространенные протоколы проверки подлинности и авторизации, часть из которых уже устарела. Устаревшими считаются все протоколы, которые используют обычную проверку подлинности. Эти протоколы обычно не поддерживают какие-либо методы двухфакторной идентификации. Вот несколько примеров приложений, которые используют устаревшую проверку подлинности:

- устаревшие приложения Microsoft Office;
- любые приложения, поддерживающие почтовые протоколы POP, IMAP и SMTP.

В наше время одного фактора аутентификации (например, имени пользователя и пароля) уже недостаточно. Пароли работают плохо, так как их можно легко угадать, и люди не умеют выбирать правильные пароли. Пароли очень уязвимы к нескольким видам атак, таких как фишинг и подбор. Одним из простейших методов защиты от компрометации паролей является многофакторная проверка подлинности. При наличии многофакторной проверки подлинности злоумышленник не сможет пройти аутентификацию и получить доступ к данным, даже если получит пароль пользователя в свое распоряжение.

Как же запретить доступ к ресурсам клиента всем приложениям, которые используют устаревшую проверку подлинности? The recommendation is to just block them with a Conditional Access policy. Если потребуется, вы сможете разрешить отдельным пользователям использовать конкретные приложения с устаревшей проверкой подлинности из конкретных сетевых расположений.

Политики условного доступа применяются после того, как пользователь прошел однофакторную аутентификацию. Поэтому условный доступ не может предотвратить атаки типа "отказ в обслуживании" (DoS). Но этот механизм может использовать сигналы, поступающие от этих событий (например, уровень риска при входе, расположение запроса и т. д.), для определения доступа.

## <a name="implementation"></a>Реализация

This section explains how to configure a Conditional Access policy to block legacy authentication. 

### <a name="identify-legacy-authentication-use"></a>Identify legacy authentication use

Before you can block legacy authentication in your directory, you need to first understand if your users have apps that use legacy authentication and how it affects your overall directory. Azure AD sign-in logs can be used to understand if you’re using legacy authentication.

1. Navigate to the **Azure portal** > **Azure Active Directory** > **Sign-ins**.
1. Add the Client App column if it is not shown by clicking on **Columns** > **Client App**.
1. **Add filters** > **Client App** > select all of the options for **Other clients** and click **Apply**.

Filtering will only show you sign-in attempts that were made by legacy authentication protocols. Clicking on each individual sign-in attempt will show you additional details. The **Client App** field under the **Basic Info** tab will indicate which legacy authentication protocol was used.

These logs will indicate which users are still depending on legacy authentication and which applications are using legacy protocols to make authentication requests. For users that do not appear in these logs and are confirmed to not be using legacy authentication, implement a Conditional Access policy for these users only.

### <a name="block-legacy-authentication"></a>Блокировка устаревших методов проверки подлинности 

In a Conditional Access policy, you can set a condition that is tied to the client apps that are used to access your resources. Учет клиентских приложений позволяет включить в область действия условия все приложения, которые используют устаревшие методы проверки подлинности. Для этого выберите **Другие клиенты** в параметре **Мобильные приложения и настольные клиенты**.

![Другие клиенты](./media/block-legacy-authentication/01.png)

Чтобы заблокировать доступ для этих приложений, выберите действие **Заблокировать доступ**.

![Заблокировать доступ](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Выбор пользователей и облачных приложений

Возможно, вы хотите заблокировать устаревшую проверку подлинности в вашей организации, выбрав следующие параметры:

- Все пользователи
- Все облачные приложения
- Заблокировать доступ

![Назначения](./media/block-legacy-authentication/03.png)

Azure has a safety feature that prevents you from creating a policy like this because this configuration violates the  [best practices](best-practices.md) for Conditional Access policies.
 
![Неподдерживаемая политика конфигурации](./media/block-legacy-authentication/04.png)

Такая защита нужна потому, что политика *блокировать всех пользователей и все облачные приложения* потенциально может запретить доступ к клиенту всей организации. Минимальные рекомендации требуют исключить из области действия политики хотя бы одного пользователя. Можно также исключить роль каталога.

![Неподдерживаемая политика конфигурации](./media/block-legacy-authentication/05.png)

Чтобы выполнить требования этой защитной функции, исключите из политики одного пользователя. В идеале у вас должно быть несколько [административных учетных записей AAD для аварийного доступа](../users-groups-roles/directory-emergency-access.md), для которых настроены исключения из этой политики.

## <a name="policy-deployment"></a>Развертывание политики

Прежде чем включить эту политику в рабочей среде, обдумайте и учтите следующие аспекты.
 
- **Учетные записи служб**. Определите, какие учетные записи пользователей используются в качестве учетных записей служб или устройств, например настроены на телефонах в конференц-зале. Создайте для этих учетных записей надежные пароли и добавьте их в группу исключений.
- **Отчеты о входах**. Просмотрите отчеты о входах и проверьте трафик от **других клиентов**. Определите, какие из них используются чаще всего и по каким причинам. Чаще всего такой трафик создается старыми клиентами Office, которые не используют современную проверку подлинности, или почтовыми приложениями сторонних производителей. Составьте план отказа от всех этих приложений, а если они не очень важны для работы, просто известите пользователей о том, что они больше не смогут использовать эти приложения.
 
Дополнительные сведения см. в разделе [Как развертывать новую политику?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Необходимая информация

Blocking access using **Other clients** also blocks Exchange Online PowerShell and Dynamics 365 using basic auth.

Если задать для политики параметр **Другие клиенты**, доступ из определенных клиентов, например SPConnect, заблокируется для всей организации. Эта блокировка выполняется, так как клиенты предыдущих версий выполняют аутентификацию непредвиденным образом. Эта проблема не касается основных приложений Office, таких как клиенты Office прежних версий.

Чтобы политика вступила в силу, может потребоваться до 24 часов.

You can select all available grant controls for the **Other clients** condition; however, the end-user experience is always the same - blocked access.

If you block legacy authentication using the **Other clients** condition, you can also set the device platform and location condition. Например, если вы только хотите заблокировать устаревшую аутентификацию для мобильных устройств, задайте условие **платформ устройств**, выбрав:

- Android
- iOS
- Windows Phone

![Неподдерживаемая политика конфигурации](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Дальнейшие действия

- If you are not familiar with configuring Conditional Access policies yet, see [require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md) for an example.
- For more information about modern authentication support, see [How modern authentication works for Office 2013 and Office 2016 client apps](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
