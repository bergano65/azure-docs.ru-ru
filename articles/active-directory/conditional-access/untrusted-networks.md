---
title: Require MFA from untrusted networks - Azure Active Directory
description: Learn how to configure a Conditional Access policy in Azure Active Directory (Azure AD) to for access attempts from untrusted networks.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379991"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>How to: Require MFA for access from untrusted networks with Conditional Access   

Azure Active Directory (Azure AD) обеспечивает единый вход на устройства, в приложения и службы откуда угодно. Пользователи могут получать доступ к облачным приложениям не только из сети вашей организации, но и из любого ненадежного расположения в Интернете. Общая рекомендация для предоставления доступа из ненадежных сетей — требовать Многофакторную идентификацию (MFA).

This article gives you the information you need to configure a Conditional Access policy that requires MFA for access from untrusted networks. 

## <a name="prerequisites"></a>Технические условия

В данной статье предполагается, что вы знакомы с: 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>Описание сценария

Чтобы управлять балансом между безопасностью и производительностью, при входе из сети вашей организации может быть достаточно требовать ввода пароля. Однако при доступе из расположения в ненадежной сети повышается риск входа незаконными пользователями. Чтобы устранить эту проблему, можно заблокировать доступ из ненадежных сетей. В качестве альтернативы также можете задать требование многофакторной проверки подлинности (MFA), чтобы получить дополнительную уверенность в том, что попытка была сделана законным владельцем учетной записи. 

With Azure AD Conditional Access, you can address this requirement with a single policy that grants access: 

- к выбранным облачным приложениям;
- к выбранным пользователям и группам;  
- по требованию многофакторной проверки подлинности; 
- При доступе из: 
   - ненадежного расположения.

## <a name="implementation"></a>Реализация

The challenge of this scenario is to translate *access from an untrusted network location* into a Conditional Access condition. In a Conditional Access policy, you can configure the [locations condition](location-condition.md) to address scenarios that are related to network locations. Условие расположения позволяет выбрать именованные расположения, которые представляют собой логическое группирование диапазонов IP-адресов, стран и регионов.  

Typically, your organization owns one or more address ranges, for example, 199.30.16.0 - 199.30.16.15.
Чтобы настроить именованное расположение, сделайте следующее:

- Specifying this range (199.30.16.0/28) 
- Назначьте описательное имя, например **Корпоративная сеть**. 

Вместо того, чтобы пытаться определить ненадежные расположения, вы можете сделать следующее:

- Включить все расположения 

   ![Условный доступ](./media/untrusted-networks/02.png)

- Исключить все надежные расположения. 

   ![Условный доступ](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Развертывание политики

With the approach outlined in this article, you can now configure a Conditional Access policy for untrusted locations. Чтобы убедиться, что политика работает должным образом, рекомендуется протестировать ее перед развертыванием в рабочей среде. В идеале для проверки правильности работы новой политики следует использовать тестовый клиент. Дополнительные сведения см. в разделе [Как развертывать новую политику?](best-practices.md#how-should-you-deploy-a-new-policy) 

## <a name="next-steps"></a>Дальнейшие действия

If you would like to learn more about Conditional Access, see [What is Conditional Access in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
