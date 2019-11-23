---
title: Conditional Access require managed device - Azure Active Directory
description: Learn how to configure Azure Active Directory (Azure AD) device-based Conditional Access policies that require managed devices for cloud app access.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31b7aa906cbefc0ffda707a228f2a9d50be351a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380029"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>How To: Require managed devices for cloud app access with Conditional Access

Azure Active Directory (AAD) поддерживает единый вход для приложений и служб из любого расположения, что очень важно в современном мире мобильных и облачных технологий. Авторизованные пользователи могут обращаться к облачным приложениям, используя широкий спектр мобильных и других устройств, в том числе личные устройства. Но во многих средах используются такие приложения, доступ к которым следует разрешать только для тех устройств, для которых соблюдаются определенные стандарты соответствия и безопасности. Такие устройства называются управляемыми устройствами. 

This article explains how you can configure Conditional Access policies that require managed devices to access certain cloud apps in your environment. 

## <a name="prerequisites"></a>Технические условия

Requiring managed devices for cloud app access ties **Azure AD Conditional Access** and **Azure AD device management** together. Если вы не знакомы с одной из этих областей, ознакомьтесь со следующими статьями:

- **[Conditional Access in Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** - This article provides you with a conceptual overview of Conditional Access and the related terminology.
- **[Общие сведения об управлении устройствами в Azure Active Directory](../devices/overview.md)** . В этой статье описано несколько вариантов реализации контроля организации за устройствами. 

## <a name="scenario-description"></a>Описание сценария

Зачастую непросто поддерживать баланс между безопасностью и производительностью. Широкое разнообразие поддерживаемых устройств для доступа к облачным ресурсам помогает повысить эффективность работы пользователей. С другой стороны, вы наверняка захотите оградить некоторые ресурсы в корпоративной среде от доступа устройств с неизвестным уровнем защиты. Для всех таких ресурсов нужно настроить ограничение, разрешающее доступ только с управляемых устройств. 

With Azure AD Conditional Access, you can address this requirement with a single policy that grants access:

- к выбранным облачным приложениям;
- к выбранным пользователям и группам;
- только с управляемого устройства.

## <a name="managed-devices"></a>Управляемые устройства  

Проще говоря, управляемые устройства — это устройства, которые находится под *некоторым* контролем организации. В Azure AD чтобы устройство стало управляемым, его необходимо зарегистрировать в Azure AD. При регистрации устройства создается удостоверение устройства в виде объекта устройства. Azure использует этот объект для отслеживания сведений о состоянии устройства. Как администратор Azure AD вы уже могли использовать этот объект для переключения (включения или отключения) состояния устройства.
  
![Состояния на основе устройства](./media/require-managed-devices/32.png)

Существует три варианта регистрации устройств в Azure AD: 

- **Azure AD registered devices** - to get a personal device registered with Azure AD
- **Azure AD joined devices** - to get an organizational Windows 10 device that is not joined to an on-premises AD registered with Azure AD. 
- **Hybrid Azure AD joined devices** - to get a Windows 10 or supported down-level device that is joined to an on-premises AD registered with Azure AD.

These three options are discussed in the article [What is a device identity?](../devices/overview.md)

В качестве управляемых устройств можно использовать зарегистрированные устройства, которые **имеют гибридное присоединение к Azure AD** или **помечены как совместимые**.  

![Состояния на основе устройства](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Требовать устройства с гибридным присоединением к Azure AD

In your Conditional Access policy, you can select **Require Hybrid Azure AD joined device** to state that the selected cloud apps can only be accessed using a managed device. 

![Состояния на основе устройства](./media/require-managed-devices/10.png)

Этот параметр применяется только к устройствам с Windows 10 или устройствам нижнего уровня, например устройствам с Windows 7 или Windows 8, которые присоединены к локальной службе AD. Эти устройства можно зарегистрировать в Azure AD только с помощью гибридного присоединения к Azure AD. Это [автоматизированный процесс](../devices/hybrid-azuread-join-plan.md) для регистрации устройств Windows 10. 

![Состояния на основе устройства](./media/require-managed-devices/45.png)

Что делает устройство с гибридным присоединением к Azure AD управляемым устройством?  Для устройств, присоединенных к локальному AD, предполагается, что для управления этими устройствами используются такие решения, как **System Center Configuration Manager (SCCM)** или **групповая политика**. Так как Azure AD не может определить, был ли применен к устройству любой из этих методов, обязательное использование устройства с гибридным присоединением к Azure AD считается относительно слабым механизмом использования управляемых устройств. Вы как администратор должны решить, являются ли методы, применяемые к локальным устройствам, присоединенным к домену, достаточно надежными для определения управляемого устройства, если такое устройство также является гибридным устройством, присоединенным к Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Требовать, чтобы устройство было отмечено как соответствующее

Вариант *Требовать, чтобы устройство было отмечено как соответствующее* является самым надежным методом определения управляемого устройства.

![Состояния на основе устройства](./media/require-managed-devices/11.png)

Для использования этого варианта устройство должно быть зарегистрировано в Azure AD, а также помечено как совместимое с помощью:
         
- Intune.
- Система управления мобильными устройствами стороннего производителя, которая поддерживает устройства Windows 10 через интеграцию с Azure AD. Сторонние системы управления мобильными устройствами для ОС, отличных от Windows 10, не поддерживаются.
 
![Состояния на основе устройства](./media/require-managed-devices/46.png)

Для устройства, которое помечено как совместимое, можно предположить, что: 

- Мобильные устройства, которые ваши сотрудники используют для доступа к данным организации, являются управляемыми
- Мобильные приложения, которые используют ваши сотрудники, являются управляемыми
- Сведения о вашей организации защищены благодаря контролю над тем, как ваши сотрудники обращаются к этим данным и обмениваются ими
- Устройство и приложения на нем соответствуют требованиям безопасности организации

## <a name="next-steps"></a>Дальнейшие действия

Before configuring a device-based Conditional Access policy in your environment, you should take a look at the [best practices for Conditional Access in Azure Active Directory](best-practices.md).
