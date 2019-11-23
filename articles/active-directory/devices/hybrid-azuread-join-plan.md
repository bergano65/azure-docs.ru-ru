---
title: Plan hybrid Azure Active Directory join - Azure Active Directory
description: Сведения о настройке гибридных устройств, присоединенных к Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a4f85aeaf2fb263ba2df8f34a51f9e25c212aff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379317"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>How To: Plan your hybrid Azure Active Directory join implementation

Подобно пользователю, устройство — это еще одно основное удостоверение, которое необходимо защитить, чтобы с его помощью обеспечить постоянную защиту ресурсов независимо от того, где вы находитесь. Для этого разместите удостоверения своих устройств в Azure AD и управляйте такими удостоверениями с помощью одного из следующих методов:

- присоединение к Azure AD;
- гибридное присоединение к Azure AD;
- регистрация в Azure AD.

Размещение устройств в Azure AD позволяет увеличить эффективность работы пользователей благодаря выполнению единого входа для облачных и локальных ресурсов. At the same time, you can secure access to your cloud and on-premises resources with [Conditional Access](../active-directory-conditional-access-azure-portal.md).

If you have an on-premises Active Directory (AD) environment and you want to join your AD domain-joined computers to Azure AD, you can accomplish this by doing hybrid Azure AD join. В этой статье приведены соответствующие шаги для реализации гибридного присоединения устройств к Azure AD в вашей среде. 

## <a name="prerequisites"></a>Технические условия

This article assumes that you are familiar with the [Introduction to device identity management in Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> The minimum required domain controller version for Windows 10 hybrid Azure AD join is Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Планирование реализации

Чтобы спланировать гибридную реализацию Azure AD, вам нужно ознакомиться со следующими разделами:

|   |   |
| --- | --- |
| ![Проверка][1] | Просмотр списка поддерживаемых устройств |
| ![Проверка][1] | Ознакомление с важными сведениями |
| ![Проверка][1] | Review controlled validation of hybrid Azure AD join |
| ![Проверка][1] | Select your scenario based on your identity infrastructure |
| ![Проверка][1] | Review on-premises AD UPN support for hybrid Azure AD join |

## <a name="review-supported-devices"></a>Просмотр списка поддерживаемых устройств

Гибридное присоединение устройств к Azure AD поддерживает широкий спектр устройств Windows. Так как для настройки устройств под управлением старых версий Windows требуются дополнительные или другие шаги, поддерживаемые устройства сгруппированы в две категории.

### <a name="windows-current-devices"></a>Текущие устройства Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

For devices running the Windows desktop operating system, supported version are listed in this article [Windows 10 release information](https://docs.microsoft.com/windows/release-information/). As a best practice, Microsoft recommends you upgrade to the latest version of Windows 10.

### <a name="windows-down-level-devices"></a>Устройства Windows нижнего уровня

- Windows 8.1
- Windows 7. For support information on Windows 7, see [Support for Windows 7 is ending](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. For support information on Windows Server 2008 and 2008 R2, see [Prepare for Windows Server 2008 end of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

В качестве первого шага планирования вам следует просмотреть свою среду и определить, нужно ли вам поддерживать устройства Windows нижнего уровня.

## <a name="review-things-you-should-know"></a>Ознакомление с важными сведениями

Hybrid Azure AD join is currently not supported if your environment consists of a single AD forest synchronizing identity data to more than one Azure AD tenant.

If your environment uses virtual desktop infrastructure (VDI), see [Device identity and desktop virtualization](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

Hybrid Azure AD join is supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Hybrid Azure AD join. Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. Starting from WIndows 10 1903 release, TPMs 1.2 are not used for hybrid Azure AD join and devices with those TPMs will be considered as if they don't have a TPM.

Hybrid Azure AD join is not supported for Windows Server running the Domain Controller (DC) role.

Hybrid Azure AD join is not supported on Windows down-level devices when using credential roaming or user profile roaming or mandatory profile.

If you are relying on the System Preparation Tool (Sysprep) and if you are using a **pre-Windows 10 1809** image for installation, make sure that image is not from a device that is already registered with Azure AD as Hybrid Azure AD join.

If you are relying on a Virtual Machine (VM) snapshot to create additional VMs, make sure that snapshot is not from a VM that is already registered with Azure AD as Hybrid Azure AD join.

If your Windows 10 domain joined devices are [Azure AD registered](overview.md#getting-devices-in-azure-ad) to your tenant, it could lead to a dual state of Hybrid Azure AD joined and Azure AD registered device. We recommend upgrading to Windows 10 1803 (with KB4489894 applied) or above to automatically address this scenario. In pre-1803 releases, you will need to remove the Azure AD registered state manually before enabling Hybrid Azure AD join. In 1803 and above releases, the following changes have been made to avoid this dual state:

- Any existing Azure AD registered state would be automatically removed <i>after the device is Hybrid Azure AD joined</i>.
- You can prevent your domain joined device from being Azure AD registered by adding this registry key - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- In Windows 10 1803, if you have Windows Hello for Business configured, the user needs to re-setup Windows Hello for Business after the dual state clean up.This issue has been addressed with KB4512509

> [!NOTE]
> The Azure AD registered device will not be automatically removed if it is managed by Intune.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Review controlled validation of hybrid Azure AD join

When all of the pre-requisites are in place, Windows devices will automatically register as devices in your Azure AD tenant. The state of these device identities in Azure AD is referred as hybrid Azure AD join. More information about the concepts covered in this article can be found in the article [Introduction to device identity management in Azure Active Directory](overview.md).

Organizations may want to do a controlled validation of hybrid Azure AD join before enabling it across their entire organization all at once. Review the article [controlled validation of hybrid Azure AD join](hybrid-azuread-join-control.md) to understand how to accomplish it.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Select your scenario based on your identity infrastructure

Hybrid Azure AD join works with both, managed and federated environments depending on whether the UPN is routable or non-routable. See bottom of the page for table on supported scenarios.  

### <a name="managed-environment"></a>Управляемая среда

Управляемую среду можно развернуть при помощи [синхронизации хэша паролей](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) или [сквозной аутентификации](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) с [единым входом](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

В этих сценариях не требуется настраивать сервер федерации для проверки подлинности.

### <a name="federated-environment"></a>Федеративная среда

В федеративной среде должен быть поставщик удостоверений, поддерживающий следующие требования. Это не относится к федеративной среде, в которой используются службы федерации Active Directory (AD FS).

- **WIAORMULTIAUTHN claim:** This claim is required to do hybrid Azure AD join for Windows down-level devices.
- **WS-Trust protocol:** This protocol is required to authenticate Windows current hybrid Azure AD joined devices with Azure AD. При использовании AD FS нужно включить следующие конечные точки WS-Trust: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Также нужно включить **adfs/services/trust/2005/windowstransport** или **adfs/services/trust/13/windowstransport**, но только в качестве конечных точек с подключением к интрасети. Их НЕЛЬЗЯ предоставлять как конечные точки с подключением к экстрасети через прокси-сервер веб-приложения. Дополнительные сведения см. в статье об [отключении конечных точек WS-Trust в Windows на прокси-сервере](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). В разделе **Служба** > **Конечные точки** вы можете увидеть, какие конечные точки активированы в консоли управления AD FS.

> [!NOTE]
> Azure AD не поддерживает смарт-карты и сертификаты в управляемых доменах.

Начиная с версии 1.1.819.0 Azure AD Connect предоставляет мастер для настройки гибридного присоединения к Azure AD. Этот мастер позволяет значительно упростить настройку. Если установка нужной версии Azure AD Connect для вас не подходит, перейдите к [руководству по настройке гибридного присоединения устройств к Azure AD вручную](hybrid-azuread-join-manual.md). 

Based on the scenario that matches your identity infrastructure, see:

- [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
- [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Review on-premises AD UPN support for Hybrid Azure AD join

В некоторых случаях имена участников-пользователей локальной службы AD могут отличаться от имен участников-пользователей Azure Active Directory. В некоторых случаях присоединение к гибридной Azure Active Directory в Windows 10 обеспечивает ограниченную поддержку имен участников-пользователей локальной службы AD, которая определяется [методом проверки подлинности](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), типом домена и версией Windows 10. Существуют два типа имен участников-пользователей локальной службы AD, которые могут существовать в вашей среде.

- Routable UPN: A routable UPN has a valid verified domain, that is registered with a domain registrar. Например, если основным доменом является contoso.com в Azure Active Directory, то contoso.org является основным доменом в локальной службе AD, принадлежащим компании Contoso и [проверенным в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).
- Non-routable UPN: A non-routable UPN does not have a verified domain. Эго возможно использовать только в частной сети вашей организации. Например, если основным доменом в Azure Active Directory является contoso.com, то contoso.local является основным доменом в локальной службе AD, но не является проверяемым доменом в Интернете и используется только в сети компании Contoso.

В приведенной ниже таблице приведены сведения о поддержке имен участников-пользователей локальной службы AD для гибридного присоединения к Azure Active Directory в Windows 10.

| Тип имени участника-пользователя локальной службы AD | Тип домена | Версия Windows 10 | Описание |
| ----- | ----- | ----- | ----- |
| Маршрутизируемый | Федеративные | Начиная с выпуска 1703 | Общедоступная версия |
| Немаршрутизируемый | Федеративные | Начиная с выпуска 1803 | Общедоступная версия |
| Маршрутизируемый | Управляемое | Начиная с выпуска 1803 | Generally available, Azure AD SSPR on Windows lockscreen is not supported |
| Немаршрутизируемый | Управляемое | Не поддерживается | |

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
> [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
