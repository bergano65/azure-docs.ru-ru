---
title: Approved client apps with Conditional Access - Azure Active Directory
description: Learn how to require approved client apps for cloud app access with Conditional Access in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381122"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>How To: Require approved client apps for cloud app access with Conditional Access 

Ваши сотрудники используют мобильные устройства как в личных целях, так и для выполнения рабочих задач. Помимо обеспечения эффективности работы сотрудников требуется предотвратить возможную потерю данных. With Azure Active Directory (Azure AD) Conditional Access, you can restrict access to your cloud apps to approved client apps that can protect your corporate data.  

В этом разделе описывается настройка политик условного доступа, которые требуют использовать утвержденные клиентские приложения.

## <a name="overview"></a>Краткое описание

With [Azure AD Conditional Access](overview.md), you can fine-tune how authorized users can access your resources. Например, вы можете ограничить доступ к своим облачным приложениям, предоставив его только доверенным устройствам.

Можно использовать [политики защиты приложений Intune](https://docs.microsoft.com/intune/app-protection-policy), чтобы обеспечить безопасность данных компании. Политики защиты приложений Intune не требуют решения по управлению мобильными устройствами (MDM). Это позволяет защитить данные компании, используя или не используя регистрацию устройств в решении по управлению устройствами.

Azure Active Directory Conditional Access enables you to limit access to your cloud apps to client apps that support Intune app protection policies. Например, можно ограничить доступ приложения Outlook к Exchange Online.

In the Conditional Access terminology, these client apps are known as **approved client apps**.  

![Условный доступ](./media/app-based-conditional-access/05.png)

Список утвержденных клиентских приложений приведен в разделе [Требование утвержденного клиентского приложения](technical-reference.md#approved-client-app-requirement).

You can combine app-based Conditional Access policies with other policies such as [device-based Conditional Access policies](require-managed-devices.md) to provide flexibility in how to protect data for both personal and corporate devices.

## <a name="before-you-begin"></a>Перед началом работы

В данной статье предполагается, что вы знакомы со следующими технологиями и процедурами:

- Технический справочник по [требованию утвержденных клиентских приложений](technical-reference.md#approved-client-app-requirement).
- The basic concepts of [Conditional Access in Azure Active Directory](overview.md).
- How to [configure a Conditional Access policy](app-based-mfa.md).
- The [migration of Conditional Access policies](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Технические условия

To create an app-based Conditional Access policy, you must have an Enterprise Mobility + Security or an Azure Active Directory premium subscription, and the users must be licensed for EMS or Azure AD. 

## <a name="exchange-online-policy"></a>Политика Exchange Online 

This scenario consists of an app-based Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>Обзор сценария

В этом сценарии предполагается, что пользователь:

- настраивает сообщение электронной почты с помощью собственного почтового приложения на iOS или Android для подключения к Exchange;
- получает сообщение электронной почты, указывающее, что доступ предоставляется только с помощью приложения Outlook;
- скачивает приложение по ссылке;
- открывает приложение Outlook и входит с использованием учетных данных Azure AD;
- получает запрос на установку Authenticator (iOS) или корпоративного портала (Android), чтобы продолжить;
- устанавливает приложение и может вернуться в приложение Outlook для продолжения;
- получает запрос на регистрацию устройства;
- может получить доступ к электронной почте.

Any Intune app protection policies are activated at the time the access corporate data and may prompt the user to restart the application, use an additional PIN etc. (if configured for the application and platform).

### <a name="configuration"></a>Настройка 

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Облачные приложения.** Для облачных приложений необходимо выбрать **Office 365 Exchange Online**.
1. **Условия.** В качестве **условий** необходимо настроить **платформы устройств** и **клиентские приложения**.
   1. В разделе **Платформы устройств** выберите **Android** и **iOS**.
   1. Для параметра **Клиентские приложения (предварительная версия)** выберите варианты **Мобильные и классические приложения** и **Клиенты с современной проверкой подлинности**.
1. В разделе **Элементы управления доступом** необходимо установить флажок **Требовать утвержденное клиентское приложение (предварительная версия)** .

   ![Условный доступ](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Облачные приложения.** Для облачных приложений необходимо выбрать **Office 365 Exchange Online**.
1. **Условия.** Для параметра **Условия** следует настроить **Клиентские приложения (предварительная версия)** . 
   1. Для параметра **Клиентские приложения (предварительная версия)** выберите варианты **Мобильные и классические приложения** и **Клиенты Exchange ActiveSync**.
   1. В разделе **Элементы управления доступом** необходимо установить флажок **Требовать утвержденное клиентское приложение (предварительная версия)** .

      ![Условный доступ](./media/app-based-conditional-access/05.png)

**Шаг 3. Настройка политики защиты приложений Intune для клиентских приложений iOS и Android**

Дополнительные сведения см. в статье [Защита приложений и данных с помощью Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="exchange-online-and-sharepoint-online-policy"></a>Политика Exchange Online и SharePoint Online

This scenario consists of a Conditional Access with mobile app management policy for access to Exchange Online and SharePoint Online with approved apps.

### <a name="scenario-playbook"></a>Обзор сценария

В этом сценарии предполагается, что пользователь:

- пытается использовать приложение SharePoint для подключения и просмотра корпоративных сайтов;
- Attempts to sign in with the same credentials as the Outlook app credentials
- не должен выполнять повторную регистрацию и может получить доступ к ресурсам.

### <a name="configuration"></a>Настройка

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Облачные приложения.** Для облачных приложений необходимо выбрать **Office 365 Exchange Online** и **Office 365 SharePoint Online**. 
1. **Условия.** В качестве **условий** необходимо настроить **платформы устройств** и **клиентские приложения**.
   1. В разделе **Платформы устройств** выберите **Android** и **iOS**.
   1. Для параметра **Клиентские приложения (предварительная версия)** выберите варианты **Мобильные приложения и классические клиенты** и **Клиенты с современной проверкой подлинности**.
1. В разделе **Элементы управления доступом** необходимо установить флажок **Требовать утвержденное клиентское приложение (предварительная версия)** .

   ![Условный доступ](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Облачные приложения.** Для облачных приложений необходимо выбрать **Office 365 Exchange Online**. В сети 
1. **Условия.** В качестве **условий** необходимо настроить **клиентские приложения**.
   1. Для параметра **Клиентские приложения (предварительная версия)** выберите варианты **Мобильные и классические приложения** и **Клиенты Exchange ActiveSync**.
   1. В разделе **Элементы управления доступом** необходимо установить флажок **Требовать утвержденное клиентское приложение (предварительная версия)** .

      ![Условный доступ](./media/app-based-conditional-access/05.png)

**Шаг 3. Настройка политики защиты приложений Intune для клиентских приложений iOS и Android**

![Условный доступ](./media/app-based-conditional-access/09.png)

Дополнительные сведения см. в статье [Защита приложений и данных с помощью Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Политика на основе приложений или политика соответствующего устройства для Exchange Online и SharePoint Online

This scenario consists of an app-based or compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>Обзор сценария

В этом сценарии предполагается, что:
 
- Some users are already enrolled (with or without corporate devices)
- пользователям, не зарегистрированным в Azure AD с использованием защищенного приложения, нужно зарегистрировать устройство для доступа к ресурсам;
- зарегистрированным пользователям, использующим защищенное приложение, не нужно повторно регистрировать устройство.

### <a name="configuration"></a>Настройка

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Облачные приложения.** Для облачных приложений необходимо выбрать **Office 365 Exchange Online** и **Office 365 SharePoint Online**. 
1. **Условия.** В качестве **условий** необходимо настроить **платформы устройств** и **клиентские приложения**. 
   1. В разделе **Платформы устройств** выберите **Android** и **iOS**.
   1. Для параметра **Клиентские приложения (предварительная версия)** выберите варианты **Мобильные приложения и классические клиенты** и **Клиенты с современной проверкой подлинности**.
1. В разделе **Элементы управления доступом** необходимо выбрать следующие параметры:
   - **Требовать, чтобы устройство было отмечено как соответствующее**
   - **Требовать утвержденное клиентское приложение (предварительная версия)**
   - **Требовать один из выбранных элементов управления**   
 
      ![Условный доступ](./media/app-based-conditional-access/11.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Облачные приложения.** Для облачных приложений необходимо выбрать **Office 365 Exchange Online**. 
1. **Условия.** В качестве **условий** необходимо настроить **клиентские приложения**. 
   1. Для параметра **Клиентские приложения (предварительная версия)** выберите варианты **Мобильные и классические приложения** и **Клиенты Exchange ActiveSync**.
1. В разделе **Элементы управления доступом** необходимо установить флажок **Требовать утвержденное клиентское приложение (предварительная версия)** .
 
   ![Условный доступ](./media/app-based-conditional-access/11.png)

**Шаг 3. Настройка политики защиты приложений Intune для клиентских приложений iOS и Android**

![Условный доступ](./media/app-based-conditional-access/09.png)

Дополнительные сведения см. в статье [Защита приложений и данных с помощью Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Политика на основе приложений и политика соответствующего устройства для Exchange Online и SharePoint Online

This scenario consists of an app-based and compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>Обзор сценария

В этом сценарии предполагается, что пользователь:
 
- настраивает сообщение электронной почты с помощью собственного почтового приложения на iOS или Android для подключения к Exchange;
- получает сообщение электронной почты, указывающее, что для доступа требуется регистрация устройства;
- скачивает корпоративный портал и выполняет вход на него;
- проверяет электронную почту и получает запрос на использование приложения Outlook;
- скачивает приложение Outlook;
- открывает приложение Outlook и вводит учетные данные, используемые для регистрации;
- может получить доступ к электронной почте.

Любые политики защиты приложений Intune активируются во время получения доступа к корпоративным данным. Пользователю может быть предложено перезапустить приложение, использовать дополнительный PIN-код и т. д. (если он настроен для приложения и платформы).

### <a name="configuration"></a>Настройка

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Облачные приложения.** Для облачных приложений необходимо выбрать **Office 365 Exchange Online** и **Office 365 SharePoint Online**. 
1. **Условия.** В качестве **условий** необходимо настроить **платформы устройств** и **клиентские приложения**. 
   1. В разделе **Платформы устройств** выберите **Android** и **iOS**.
   1. Для параметра **Клиентские приложения (предварительная версия)** выберите варианты **Мобильные и классические приложения** и **Клиенты с современной проверкой подлинности**.
1. В разделе **Элементы управления доступом** необходимо выбрать следующие параметры:
   - **Требовать, чтобы устройство было отмечено как соответствующее**
   - **Требовать утвержденное клиентское приложение (предварительная версия)**
   - **Требовать все выбранные элементы управления**   
 
      ![Условный доступ](./media/app-based-conditional-access/13.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **Облачные приложения.** Для облачных приложений необходимо выбрать **Office 365 Exchange Online**. 
1. **Условия.** Для параметра **Условия** следует настроить **Клиентские приложения (предварительная версия)** . 
   1. Для параметра **Клиентские приложения (предварительная версия)** выберите варианты **Мобильные и классические приложения** и **Клиенты Exchange ActiveSync**.

   ![Условный доступ](./media/app-based-conditional-access/92.png)

1. В разделе **Элементы управления доступом** необходимо выбрать следующие параметры:
   - **Требовать, чтобы устройство было отмечено как соответствующее**
   - **Требовать утвержденное клиентское приложение (предварительная версия)**
   - **Требовать все выбранные элементы управления**   

**Шаг 3. Настройка политики защиты приложений Intune для клиентских приложений iOS и Android**

![Условный доступ](./media/app-based-conditional-access/09.png)

Дополнительные сведения см. в статье [Защита приложений и данных с помощью Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как настроить политику условного доступа, см. статью [Краткое руководство. Требование Многофакторной идентификации для конкретных приложений с помощью условного доступа Azure Active Directory](app-based-mfa.md).

Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Best practices for Conditional Access in Azure Active Directory](best-practices.md) (Рекомендации по работе с условным доступом в Azure Active Directory). 
