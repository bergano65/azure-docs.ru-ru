---
title: Access controls in Azure Active Directory Conditional Access
description: Learn how access controls in Azure Active Directory Conditional Access work.
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
ms.openlocfilehash: cac92da744b3d5b7aeaa325c7cc564a3d7e2abdd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380810"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>What are access controls in Azure Active Directory Conditional Access?

With [Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), you can control how authorized users access your cloud apps. In a Conditional Access policy, you define the response ("do this") to the reason for triggering your policy ("when this happens").

![Контроль](./media/controls/10.png)

In the context of Conditional Access,

- элемент "**в этом случае**" называется **условиями**;
- а "**следует сделать это**" называется **элементом управления доступом**.

The combination of a condition statement with your controls represents a Conditional Access policy.

![Контроль](./media/controls/61.png)

Каждый элемент управления определяет либо требование, которое должно быть выполнено осуществляющим вход пользователем или системой, либо ограничение действий пользователя после входа.

Существуют два типа элементов управления.

- **Элементы управления предоставлением прав** позволяют контролировать доступ.
- **Элементы управления сеансом** позволяют ограничить доступ в пределах сеанса.

This topic explains the various controls that are available in Azure AD Conditional Access. 

## <a name="grant-controls"></a>Элементы управления предоставлением прав

С помощью элементов управления предоставлением прав можно полностью запретить доступ или разрешить доступ с дополнительными требованиями, выбрав необходимые элементы управления. Если указано несколько элементов управления, то можно требовать, чтобы:

- были выполнены все выбранные элементы управления (*И*);
- был выполнен один выбранный элемент управления (*И*).

![Контроль](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Многофакторная Идентификация

Этот элемент управления позволяет требовать прохождение Многофакторной идентификации для доступа к указанному облачному приложению. Данный элемент управления поддерживает следующие поставщики Многофакторной идентификации:

- Многофакторная идентификация Azure
- локальный поставщик многофакторной проверки подлинности, используемый совместно со службами федерации Active Directory (AD FS).

Многофакторная идентификация позволяет защитить ресурсы от несанкционированного входа пользователя, который получил доступ к основным учетным данным действительного пользователя.

### <a name="compliant-device"></a>Устройства, соответствующие требованиям

You can configure Conditional Access policies that are device-based. The objective of a device-based Conditional Access policy is to only grant access to the selected cloud apps from [managed devices](require-managed-devices.md). Один из вариантов ограничения доступа к управляемым устройствам — требование отметки устройства в качестве соответствующего. Устройство может быть помечено как соответствующее службой Intune (для любой операционной системы устройства) или сторонней системой управления мобильными устройствами для устройств Windows 10. Сторонние системы управления мобильными устройствами для ОС, отличных от Windows 10, не поддерживаются. 

Ваше устройство должно быть зарегистрировано в Azure AD, прежде чем его можно будет пометить как соответствующее. Чтобы зарегистрировать устройство, у вас есть три варианта: 

- Устройства, зарегистрированные в Azure AD
- Устройства, присоединенные к Azure AD  
- Гибридные устройства, присоединенные к Azure AD

These three options are discussed in the article [What is a device identity?](../devices/overview.md)

For more information, see [how to require managed devices for cloud app access with Conditional Access](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Гибридные устройства, присоединенные к Azure AD

Requiring a Hybrid Azure AD joined device is another option you have to configure device-based Conditional Access policies. Это требование относится к настольным компьютерам, ноутбукам и корпоративным планшетам, присоединенным к локальному каталогу Active Directory. If this option is selected, your Conditional Access policy grants access to access attempts made with devices that are joined to your on-premises Active Directory and your Azure Active Directory.  

For more information, see [set up Azure Active Directory device-based Conditional Access policies](require-managed-devices.md).

### <a name="approved-client-app"></a>Утвержденное клиентское приложение

Так как ваши сотрудники используют мобильные устройства как в личных целях, так и для выполнения рабочих задач, вам может потребоваться возможность защитить данные компании, предоставляемые на устройствах, даже в том случае, если вы не управляете этими устройствами.
Можно использовать [политики защиты приложений Intune](https://docs.microsoft.com/intune/app-protection-policy), чтобы защитить данные компании независимо от какого-либо решения по управлению мобильными устройствами (MDM).

Используя утвержденные клиентские приложения, можно требовать поддержку [политик защиты приложений Intune](https://docs.microsoft.com/intune/app-protection-policy) клиентским приложением, которое пытается получить доступ к вашим облачным приложениям. Например, можно ограничить доступ приложения Outlook к Exchange Online. A Conditional Access policy that requires approved client apps is  also known as [app-based Conditional Access policy](app-based-conditional-access.md). Список поддерживаемых утвержденных клиентских приложений приведен в разделе [Требование утвержденного клиентского приложения](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>App protection policy (preview)

Так как ваши сотрудники используют мобильные устройства как в личных целях, так и для выполнения рабочих задач, вам может потребоваться возможность защитить данные компании, предоставляемые на устройствах, даже в том случае, если вы не управляете этими устройствами.
Можно использовать [политики защиты приложений Intune](https://docs.microsoft.com/intune/app-protection-policy), чтобы защитить данные компании независимо от какого-либо решения по управлению мобильными устройствами (MDM).

With app protection policy, you can limit access to client applications that have reported to Azure AD has having received [Intune app protection policies](https://docs.microsoft.com/intune/app-protection-policy). For example, you can restrict access to Exchange Online to the Outlook app that has an Intune app protection policy. A Conditional Access policy that requires app protection policy is also known as [app protection-based Conditional Access policy](app-protection-based-conditional-access.md). 

Your device must be registered to Azure AD before an application can be marked as policy protected.

For a list of supported policy protected client apps, see [app protection policy requirement](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Условия использования

Вы можете потребовать от пользователя принять условия использования перед получением доступа к ресурсу. Как администратор вы можете настроить и изменить условия использования, отправив документ в формате PDF. Если пользователь находится в области этого элемента управления, то этот пользователь получит доступ к приложению только после принятия условий использования.

## <a name="custom-controls-preview"></a>Настраиваемые элементы управления (предварительная версия)

Пользовательские элементы управления являются функцией выпуска Azure Active Directory Premium P1. При использовании пользовательских элементов управления пользователи перенаправляются на совместимую службу для удовлетворения дальнейших требований вне Azure Active Directory. В этом случае пользователь перенаправляется на внешнюю службу, выполняет все необходимые действия для собственной проверки или проверки подлинности и затем перенаправляется обратно в Azure Active Directory. Azure Active Directory verifies the response and, if the user was successfully authenticated or validated, the user continues in the Conditional Access flow.

These controls allow the use of certain external or custom services as Conditional Access controls, and generally extend the capabilities of Conditional Access.

Сейчас поставщики включают следующие совместимые службы:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust);
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html);
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Дополнительные сведения об этих службах можно получить у поставщиков служб.

### <a name="creating-custom-controls"></a>Создание пользовательских элементов управления

Чтобы создать пользовательский элемент управления, сначала обратитесь к поставщику, которым вы хотите воспользоваться. Each non-Microsoft provider has its own process and requirements to sign up, subscribe, or otherwise become a part of the service, and to indicate that you wish to integrate with Conditional Access. После выполнения этих требований поставщик предоставит вам блок данных в формате JSON. This data allows the provider and Conditional Access to work together for your tenant, creates the new control and defines how Conditional Access can tell if your users have successfully performed verification with the provider.

Custom controls cannot be used with Identity Protection's automation requiring multi-factor authentication or to elevate roles in Privileged Identity Manager (PIM).

Скопируйте данные JSON и вставьте их в соответствующее текстовое поле. Не изменяйте данные JSON, если точно не знаете, что делаете. Изменение данных JSON может привести к разрыву связи между поставщиком и корпорацией Майкрософт и к блокировке учетных записей ваших пользователей.

The option to create a custom control is in the **Manage** section of the **Conditional Access** page.

![Контроль](./media/controls/82.png)

Щелкните **Создать пользовательский элемент управления**. Откроется колонка с текстовым полем, в котором нужно указать данные JSON элемента управления.  

![Контроль](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Удаление пользовательских элементов управления

To delete a custom control, you must first ensure that it isn’t being used in any Conditional Access policy. После завершения:

1. Перейдите к списку пользовательских элементов управления
1. Нажмите кнопку ...  
1. Нажмите кнопку **Удалить**.

### <a name="editing-custom-controls"></a>Изменение пользовательских элементов управления

Чтобы изменить пользовательский элемент управления, необходимо удалить текущий элемент управления и создать новый элемент управления с обновленными данными.

## <a name="session-controls"></a>Элементы управления сеансов

Элементы управления сеансом позволяют ограничивать возможности работы в облачном приложении. Элементы управления сеансов используются облачным приложением и зависят от дополнительных сведений, предоставляемых Azure AD в приложение о сеансе.

![Контроль](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Использование примененных к приложению ограничений

Этот элемент управления используется, чтобы требовать от Azure AD передачу сведений об устройстве в выбранные облачные приложения. По этим сведениям об устройстве облачные приложения могут определить, инициируется ли подключение с соответствующего требованиям устройства или устройства, присоединенного к домену. Этот элемент управления поддерживает только SharePoint Online и Exchange Online в качестве выбранных облачных приложений. Выбранное облачное приложение использует сведения об устройствах для предоставления пользователям ограниченных или полных возможностей в зависимости от состояния устройства.

Дополнительные сведения см. на следующих ресурсах:

- [Включение ограниченного доступа с помощью SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Включение ограниченного доступа с помощью Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, как настроить политику условного доступа, см. статью [Краткое руководство. Требование Многофакторной идентификации для конкретных приложений с помощью условного доступа Azure Active Directory](app-based-mfa.md).
- Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Best practices for Conditional Access in Azure Active Directory](best-practices.md) (Рекомендации по работе с условным доступом в Azure Active Directory).
