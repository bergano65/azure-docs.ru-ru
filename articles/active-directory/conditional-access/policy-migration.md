---
title: Migrate Conditional Access policies - Azure Active Directory
description: Узнайте, что нужно для переноса классических политик на портал Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380553"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>What is a policy migration in Azure Active Directory Conditional Access? 

[Conditional Access](../active-directory-conditional-access-azure-portal.md) is a capability of Azure Active directory (Azure AD) that enables you to control how authorized users access your cloud apps. While the purpose is still the same, the release of the new Azure portal has introduced significant improvements to how Conditional Access works.

Мы рекомендуем перенести политики, созданные не на портале Azure, по следующим причинам:

- Теперь вы можете работать со сценариями, которые невозможно было обрабатывать ранее.
- Можно уменьшить количество политик, которыми необходимо управлять, объединяя их.   
- You can manage all your Conditional Access policies in one central location.
- Использование классического портала Azure будет прекращено.   

This article explains what you need to know to migrate your existing Conditional Access policies to the new framework.
 
## <a name="classic-policies"></a>Классические политики

In the [Azure portal](https://portal.azure.com), the [Conditional Access - Policies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) page is your entry point to your Conditional Access policies. However, in your environment, you might also have Conditional Access policies you have not created using this page. Эти политики называются *классическими политиками*. Classic policies are Conditional Access policies, you have created in:

- классического портала Azure;
- классического портала Intune;
- портала службы "Защита приложений Intune".

On the **Conditional Access** page, you can access your classic policies by clicking [**Classic policies (preview)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) in the **Manage** section. 

![Azure Active Directory](./media/policy-migration/71.png)

Представление **Классические политики** предоставляет возможность:

- Фильтровать классические политики.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Отключать классические политики.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Review the settings of a classic policy (and to disable it).

   ![Azure Active Directory](./media/policy-migration/74.png)

Отключение классический политики невозможно отменить. Поэтому вы можете изменить членство в группах в классической политике с помощью представления **Сведения**. 

![Azure Active Directory](./media/policy-migration/75.png)

Изменив выбранные группы или исключив определенные группы, можно проверить влияние отключения классический политики на несколько тестовых пользователей, прежде чем отключить ее для всех охваченных пользователей и групп. 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD Conditional Access policies

With Conditional Access in the Azure portal, you can manage all your policies in one central location. Because the implementation of how Conditional Access has changed, you should familiarize yourself with the basic concepts before migrating your classic policies.

См.:

- [What is Conditional Access in Azure Active Directory](../active-directory-conditional-access-azure-portal.md) to learn about the basic concepts and the terminology.
- [Best practices for Conditional Access in Azure Active Directory](best-practices.md) to get some guidance on deploying Conditional Access in your organization.
- [Require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md) to familiarize yourself with the user interface in the Azure portal.
 
## <a name="migration-considerations"></a>Рекомендации по переносу

In this article, Azure AD Conditional Access policies are also referred to as *new policies*.
Классические политики по-прежнему будут работать параллельно с новыми политиками, пока вы не отключите или не удалите их. 

В контексте объединения политик важны следующие аспекты.

- Классические политики привязаны к определенному облачному приложению, а для новой политики можно выбрать столько облачных приложений, сколько необходимо.
- Элементы управления классический политики и политики для облачного приложения требуют выполнения всех условий (логическая операция *И*). 
- В новой политике можно сделать следующее.
   - Объединить несколько условий, если это требуется для вашего сценария. 
   - Выбрать несколько требований для предоставления доступа в качестве элементов управления доступом и объединить их логической операцией *ИЛИ* (требуется один из выбранных элементов управления) или логической операцией *И* (требуются все выбранные элементы управления).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Если вы хотите перенести классические политики для **Office 365 Exchange Online**, которые включают в себя условие клиентских приложений **Exchange Active Sync**, возможно, вам не удастся объединить их в одну новую политику. 

Такое случается, например, если требуется поддержка всех типов клиентских приложений. В новой политике, содержащей условие клиентских приложений **Exchange Active Sync**, невозможно выбрать другие клиентские приложения.

![Azure Active Directory](./media/policy-migration/64.png)

Объединение в одну новую политику также невозможно, если классические политики содержат несколько условий. В новой политике, содержащей условие клиентских приложений **Exchange Active Sync**, невозможно указать другие условия.   

![Azure Active Directory](./media/policy-migration/08.png)

Если имеется новая политика, в которой настроено условие клиентских приложений **Exchange Active Sync**, то в ней не должны быть настроены какие-либо другие условия. 

![Azure Active Directory](./media/policy-migration/16.png)
 
Классические политики [на основе приложений](technical-reference.md#approved-client-app-requirement) для Office 365 Exchange Online, которые содержат условие клиентских приложений **Exchange Active Sync**, разрешают **поддерживаемые** и **неподдерживаемые** [платформы устройств](technical-reference.md#device-platform-condition). Хотя невозможно настроить отдельные платформы устройств в связанной новой политике, можно ограничить их поддержку только [поддерживаемыми платформами устройств](technical-reference.md#device-platform-condition). 

![Azure Active Directory](./media/policy-migration/65.png)

Можно объединить несколько классических политик, которые содержат **Exchange Active Sync** в качестве условия клиентских приложений, если они:

- содержат только **Exchange Active Sync** в качестве условия; 
- содержат несколько настроенных требований для предоставления доступа.

Одним из типичных сценариев является объединение:

- классической политики на основе устройств с классического портала Azure; 
- классической политики на основе приложений с портала службы "Защита приложений Intune". 
 
В этом случае классические политики можно объединить в одну новую политику, которая содержит оба выбранных требования.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Платформы устройств

Классические политики с [элементами управления на основе приложений](technical-reference.md#approved-client-app-requirement) предварительно настроены в iOS и Android в качестве [условий платформы устройств](technical-reference.md#device-platform-condition). 

В новой политике необходимо отдельно выбрать [платформы устройств](technical-reference.md#device-platform-condition), поддержка которых необходима.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, как настроить политику условного доступа, см. статью [Краткое руководство. Требование Многофакторной идентификации для конкретных приложений с помощью условного доступа Azure Active Directory](app-based-mfa.md).
- Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Best practices for Conditional Access in Azure Active Directory](best-practices.md) (Рекомендации по работе с условным доступом в Azure Active Directory). 
