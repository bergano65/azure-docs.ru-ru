---
title: Часто задаваемые вопросы об управлении устройствами в Azure Active Directory | Документация Майкрософт
description: Часто задаваемые вопросы об управлении устройствами в Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a27c9ae1b75b9517bd3af92486df96434c5b34fb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207382"
---
# <a name="azure-active-directory-device-management-faq"></a>Часто задаваемые вопросы об управлении устройствами в Azure Active Directory

## <a name="general-faq"></a>General FAQ

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Q: I registered the device recently. Почему оно отсутствует в моих сведениях о пользователе на портале Azure? Or why is the device owner marked as N/A for hybrid Azure Active Directory (Azure AD) joined devices?

**A:** Windows 10 devices that are hybrid Azure AD joined don't show up under **USER devices**.
Откройте представление **Все устройства** на портале Azure. Вы также можете использовать командлет PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0).

В списке **устройств пользователей** перечислены только следующие устройства:

- Все личные устройства без гибридного присоединения к Azure AD. 
- Все устройства не на платформе Windows 10 или Windows Server 2016.
- Все устройства не на платформе Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Q: How do I know what the device registration state of the client is?

**A:** In the Azure portal, go to **All devices**. Найдите устройство по его идентификатору. Посмотрите значение в столбце "Тип соединения". Иногда может оказаться, что выполнялся сброс или пересоздание образа устройства. Таким образом, важно также проверить состояние регистрации устройства на самом устройстве:

- Для устройств с Windows 10 и Windows Server 2016 или более поздней версии запустите `dsregcmd.exe /status`.
- Для версий ОС нижнего уровня запустите `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**A:** For troubleshooting information, see these articles:
- [Troubleshooting devices using dsregcmd command](troubleshoot-device-dsregcmd.md)
- [Устранение неполадок на устройствах под управлением Windows 10 и Windows Server 2016 с гибридным присоединением к Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Устранение неполадок на устройствах нижнего уровня с гибридным присоединением к Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Q: I see the device record under the USER info in the Azure portal. Для устройства отображается состояние "Зарегистрировано". Am I set up correctly to use Conditional Access?

**A:** The device join state, shown by **deviceID**, must match the state on Azure AD and meet any evaluation criteria for Conditional Access. For more information, see [Require managed devices for cloud app access with Conditional Access](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>Q: Why do my users see an error message saying "Your organization has deleted the device" or "Your organization has disabled the device" on their Windows 10 devices ?

**A:** On Windows 10 devices joined or registered with Azure AD, users are issued a [Primary refresh token (PRT)](concept-primary-refresh-token.md) which enables single sign on. The validity of the PRT is based on the validaity of the device itself. Users see this message if the device is either deleted or disabled in Azure AD without initiating the action from the device itself. A device can be deleted or disabled in Azure AD one of the following scenarios: 

- User disables the device from the My Apps portal. 
- An administrator (or user) deletes or disables the device in the Azure portal or by using PowerShell
- Hybrid Azure AD joined only: An administrator removes the devices OU out of sync scope resulting in the devices being deleted from Azure AD
- Upgrading Azure AD connect to the version 1.4.xx.x. [Understanding Azure AD Connect 1.4.xx.x and device disappearance](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-device-disappearance).


See below on how these actions can be rectified.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Q: I disabled or deleted my device in the Azure portal or by using Windows PowerShell. But the local state on the device says it's still registered. Что мне делать?

**A:** This operation is by design. In this case, the device doesn't have access to resources in the cloud. Administrators can perform this action for stale, lost or stolen devices to prevent unauthorized access. If this action was performed unintentionally, you'll need to re-enable or re-register the device as described below

- If the device was disabled in Azure AD, an administrator with sufficient privileges can enable it from the Azure AD portal  
  > [!NOTE]
  > If you are syncing devices using Azure AD Connect, hybrid Azure AD joined devices will be automatically re-enabled during the next  sync cycle. So, if you need to disable a hybrid Azure AD joined device, you need to disable it from your on-premises AD

 - If the device is deleted in Azure AD, you need to re-register the device. To re-register, you must take a manual action on the device. See below for instructions for re-registration based on the device state. 

      To re-register hybrid Azure AD joined Windows 10 and Windows Server 2016/2019 devices, take the following steps:

      1. Запустите командную строку от имени администратора.
      1. Укажите `dsregcmd.exe /debug /leave`.
      1. Выполните выход и вход, чтобы запустить запланированную задачу, которая повторно регистрирует устройство в Azure AD. 

      For down-level Windows OS versions that are hybrid Azure AD joined, take the following steps:

      1. Запустите командную строку от имени администратора.
      1. Укажите `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Укажите `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      For Azure AD joined devices Windows 10 devices, take the following steps:

      1. Запустите командную строку от имени администратора.
      1. Enter `dsregcmd /forcerecovery` (Note: You need to be an administrator to perform this action).
      1. Click "Sign in" in the dialog that opens up and continue with the sign in process.
      1. Sign out and sign in back to the device to complete the recovery.

      For Azure AD registered Windows 10 devices, take the following steps:

      1. Go to **Settings** > **Accounts** > **Access Work or School**. 
      1. Select the account and select **Disconnect**.
      1. Click on "+ Connect" and register the device again by going through the sign in process.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Q: Why do I see duplicate device entries in the Azure portal?

**Ответ.**

- Несколько попыток отсоединить, а затем присоединить одно и то же устройство Windows 10 или Windows Server 2016 могут привести к появлению повторяющихся записей. 
- Каждый пользователь Windows, нажавший кнопку **Add Work or School Account** (Добавить рабочую или учебную учетную запись), создает запись устройства с тем же именем.
- Для версий ОС Windows нижнего уровня, присоединенных к локальному домену Azure Directory, с помощью автоматической регистрации для каждого пользователя домена, который входит в устройство, создается запись устройства с тем же именем. 
- Присоединенный к Azure AD компьютер, на котором были выполнены очистка, повторная установка и повторное присоединение с тем же именем, будет отображен как еще одна запись с тем же именем устройства.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Q: Does Windows 10 device registration in Azure AD support TPMs in FIPS mode?

**A:** Windows 10 device registration only supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Azure AD join or Hybrid Azure AD join. Note that Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. 

---

**Q: Why can a user still access resources from a device I disabled in the Azure portal?**

**A:** It takes up to an hour for a revoke to be applied from the time the Azure AD device is marked as disabled.

>[!NOTE] 
>Для зарегистрированного устройства рекомендуется очистить его, чтобы пользователи не смогли получить доступ к его ресурсам. Дополнительные сведения см. в статье [Что такое регистрация устройств?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>Q: Why are there devices marked as "Pending" under the REGISTERED column in the Azure portal?

**A**:  Pending indicates that the device is not registered. This state indicates that a device has been synchronized using Azure AD connect from an on-premises AD and is ready for device registration. These devices have the JOIN TYPE set to "Hybrid Azure AD joined". Learn more on [how to plan your hybrid Azure Active Directory join implementation](hybrid-azuread-join-plan.md).

>[!NOTE]
>A device can also change from having a registered state to "Pending"
>* If a device is deleted from Azure AD first and re-synchronized from an on-premises AD.
>* If a device is removed from a sync scope on Azure AD Connect and added back.
>
>In both cases, you must re-register the device manually on each of these devices. To review whether the device was previously registered, you can [troubleshoot devices using the dsregcmd command](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Часто задаваемые вопросы о присоединении к Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin an Azure AD joined device locally on the device?

**A:** For pure Azure AD joined devices, make sure you have an offline local administrator account or create one. Вы не сможете выполнить вход с учетными данными пользователя Azure AD. Затем перейдите в раздел **Параметры** > **Учетные записи** > **Доступ к учетной записи места работы или учебного заведения**. Выберите учетную запись и щелкните **Отключить**. Следуйте подсказкам и укажите учетные данные локального администратора. Перезапустите устройство, чтобы завершить процесс отсоединения.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Q: Can my users' sign in to Azure AD joined devices that are deleted or disabled in Azure AD?

**Ответ.** Да. В Windows есть возможность кэширования имени пользователя и пароля, позволяющая пользователям, которые выполнили вход ранее, быстро получать доступ к рабочему столу даже без подключения к сети. 

Если устройство удалено или отключено в Azure AD, то это неизвестно на устройстве Windows. Таким образом, пользователи, которые выполнили вход ранее, по-прежнему могут получать доступ к рабочему столу с использованием кэшированного имени пользователя и пароля. But as the device is deleted or disabled, users can't access any resources protected by device-based Conditional Access. 

У пользователей, которые не выполнили вход ранее, нет доступа к устройству. Для них не включено кэширование имени пользователя и пароля. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Q: Can a disabled or deleted user sign in to an Azure AD joined devices

**A:** Yes, but only for a limited time. Когда пользователь удален или отключен в Azure AD, то об этом не сразу становится известно на устройстве Windows. Таким образом, у пользователей, которые выполнили вход ранее, есть доступ к рабочему столу с помощью кэшированного имени пользователя и пароля. 

Как правило, устройство получает сведения о состоянии пользователя менее чем через четыре часа. Затем Windows блокирует доступ этих пользователей к рабочему столу. После удаления или отключения пользователя в Azure AD все его токены отменяются. Таким образом, у него нет доступа к ресурсам. 

У удаленных или отключенных пользователей, которые не выполнили вход ранее, нет доступа к устройству. Для них не включено кэширование имени пользователя и пароля. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Azure AD joined devices after changing their UPN?

**A:** Currently, UPN changes are not fully supported on Azure AD joined devices. Поэтому их проверка подлинности с Azure AD завершается ошибкой после изменения UPN. Таким образом, у пользователей возникают проблемы с единым входом и условным доступом на своих устройствах. Сейчас пользователи должны входить в Windows с помощью плитки "Другой пользователь", используя новое UPN для устранения этой проблемы. Сейчас мы работаем над решением этой проблемы. Тем не менее пользователи, которые входят в систему с помощью Windows Hello для бизнеса, не сталкиваются с этой проблемой. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Q: My users can't search printers from Azure AD joined devices. How can I enable printing from those devices?

**A:** To deploy printers for Azure AD joined devices, see [Deploy Windows Server Hybrid Cloud Print with Pre-Authentication](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Для развертывания гибридной облачной печати требуется локальный сервер Windows Server. Сейчас облачная служба печати недоступна. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Q: How do I connect to a remote Azure AD joined device?

**A:** See [Connect to remote Azure Active Directory-joined PC](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Q: Why do my users see *You can’t get there from here*?

**A:** Did you configure certain Conditional Access rules to require a specific device state? Если устройство не соответствует критериям, пользователи заблокированы и видят это сообщение. Evaluate the Conditional Access policy rules. Чтобы это сообщение не отображалось, устройство должно соответствовать критериям.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Q: Why don't some of my users get Azure Multi-Factor Authentication prompts on Azure AD joined devices?

**A:** A user might join or register a device with Azure AD by using Multi-Factor Authentication. Само устройство становится вторым надежным фактором для этого пользователя. Каждый раз, когда этот пользователь входит на устройство и получает доступ к приложению, Azure AD рассматривает устройство как второй фактор. Таким образом, этот пользователь может легко получать доступ к приложениям без дополнительных запросов Многофакторной идентификации. 

Эта реакция на событие

- Применяется для присоединенных или зарегистрированных устройств Azure AD, а не для присоединенных устройств Azure AD с гибридным развертыванием.
- не применяется к остальным пользователям, вошедшим в учетную запись устройства. Поэтому все остальные пользователи, получившие доступ к этому устройству, получают запрос на Многофакторную идентификацию. Они могут обращаться к приложениям, для которых требуется Многофакторная идентификация.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Q: Why do I get a *username or password is incorrect* message for a device I just joined to Azure AD?

**A:** Common reasons for this scenario are as follows:

- Ваши учетные данные недействительны.
- Вашему компьютеру не удалось связаться с Azure Active Directory. Наличие проблем с сетевым подключением.
- Для использования федеративных имен для входа требуется, чтобы сервер федерации поддерживал включенные и доступные конечные точки WS-Trust. 
- Вы включили сквозную аутентификацию. Таким образом, при входе в систему необходимо изменить временный пароль.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q: Why do I see the *Oops… an error occurred!* dialog when I try to Azure AD join my PC?

**A:** This error happens when you set up Azure Active Directory enrollment with Intune. Убедитесь в том, что пользователю, который пытается выполнить операцию присоединения к Azure AD, назначена соответствующая лицензия Intune. Дополнительные сведения см. в статье [Настройка регистрации для устройств Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Q: Why did my attempt to Azure AD join a PC fail, although I didn't get any error information?

**A:** A likely cause is that you signed in to the device by using the local built-in administrator account. Создайте другую локальную учетную запись, прежде чем использовать присоединение к Azure Active Directory, чтобы завершить настройку. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What are the MS-Organization-P2P-Access certificates present on our Windows 10 devices?

**A:** The MS-Organization-P2P-Access certificates are issued by Azure AD to both, Azure AD joined and hybrid Azure AD joined devices. Эти сертификаты используются для включения доверия между устройствами в одном и том же клиенте для сценариев удаленного рабочего стола. Один сертификат выдается устройству, а другой — пользователю. Сертификат устройства находится в папке `Local Computer\Personal\Certificates` и действителен в течение одного дня. Этот сертификат обновляется (путем выдачи нового сертификата), если устройство по-прежнему активно в Azure AD. Сертификат пользователя находится в папке `Current User\Personal\Certificates` и также является действительным в течение одного дня, но он выдается по требованию, когда пользователь пытается создать сеанс подключения к удаленному рабочему столу другого устройства, присоединенного к Azure AD. Он не обновляется по истечении срока действия. Оба эти сертификата выдаются с использованием сертификата MS-Organization-P2P-Access, который находится в папке `Local Computer\AAD Token Issuer\Certificates`. Этот сертификат выдается Azure AD во время регистрации устройства. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why do I see multiple expired certificates issued by MS-Organization-P2P-Access on our Windows 10 devices? How can I delete them?

**A:** There was an issue identified on Windows 10 version 1709 and lower where expired MS-Organization-P2P-Access certificates continued to exist on the computer store because of cryptographic issues. Your users could face issues with network connectivity, if you are using any VPN clients (for example, Cisco AnyConnect) that cannot handle the large number of expired certificates. Эта проблема исправлена в выпуске Windows 10 версии 1803, что подразумевает автоматическое удаление таких просроченных сертификатов MS-Organization-P2P-Access. Эту проблему можно устранить, обновив устройства до Windows 10 версии 1803. Если выполнить обновление невозможно, можно удалить эти сертификаты без какого-либо неблагоприятного воздействия.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Часто задаваемые вопросы о гибридном присоединении к Azure AD

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin a Hybrid Azure AD joined device locally on the device?

**A:** For hybrid Azure AD joined devices, make sure to turn off automatic registration. Таким образом запланированная задача не зарегистрирует его снова. Затем запустите командную строку от имени администратора и введите `dsregcmd.exe /debug /leave`. Вместо этого можно выполнить следующую команду в качестве сценария на нескольких устройствах, чтобы отсоединить устройства в пакетном режиме.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Q: Where can I find troubleshooting information to diagnose hybrid Azure AD join failures?

**A:** For troubleshooting information, see these articles:

- [Устранение неполадок на устройствах под управлением Windows 10 и Windows Server 2016 с гибридным присоединением к Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Устранение неполадок на устройствах нижнего уровня с гибридным присоединением к Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Q: Why do I see a duplicate Azure AD registered record for my Windows 10 hybrid Azure AD joined device in the Azure AD devices list?

**A:** When your users add their accounts to apps on a domain-joined device, they might be prompted with **Add account to Windows?** Если ввести **Yes** (Да) в командной строке, устройство будет зарегистрировано в Azure AD. Тип доверия помечается как зарегистрированный в Azure AD. Включив гибридное присоединение к Azure AD для вашей организации, вы также присоедините устройство к гибридной Azure AD. После этого для одного устройства будут отображаться два состояния устройства. 

Присоединение к гибридной Azure AD имеет приоритет над регистрацией в Azure AD. So your device is considered hybrid Azure AD joined for any authentication and Conditional Access evaluation. Вы можете удалить запись устройства о регистрации в Azure AD на портале Azure AD. Узнайте, как [избежать двух состояний или удалить их на компьютере с Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Windows 10 hybrid Azure AD joined devices after changing their UPN?

**A:** Currently UPN changes are not fully supported with hybrid Azure AD joined devices. Если пользователи могут войти на устройство и имеют доступ к своим локальным приложениям, проверка подлинности с помощью Azure AD завершится ошибкой после изменения UPN. Таким образом, у пользователей возникают проблемы с единым входом и условным доступом на своих устройствах. At this time, you need to unjoin the device from Azure AD (run "dsregcmd /leave" with elevated privileges) and rejoin (happens automatically) to resolve the issue. Сейчас мы работаем над решением этой проблемы. Тем не менее пользователи, которые входят в систему с помощью Windows Hello для бизнеса, не сталкиваются с этой проблемой. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Q: Do Windows 10 hybrid Azure AD joined devices require line of sight to the domain controller to get access to cloud resources?

**A:** No, except when the user's password is changed. After Windows 10 hybrid Azure AD join is complete, and the user has signed in at least once, the device doesn't require line of sight to the domain controller to access cloud resources. Windows 10 can get single sign-on to Azure AD applications from anywhere with an internet connection, except when a password is changed. Users who sign in with Windows Hello for Business continue to get single sign-on to Azure AD applications even after a password change, even if they don't have line of sight to their domain controller. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Q: What happens if a user changes their password and tries to login to their Windows 10 hybrid Azure AD joined device outside the corporate network?

**A:** If a password is changed outside the corporate network (for example, by using Azure AD SSPR), then the user sign in with the new password will fail. For hybrid Azure AD joined devices, on-premises Active Directory is the primary authority. When a device does not have line of sight to the domain controller, it is unable to validate the new password. So, user needs to establish connection with the domain controller (either via VPN or being in the corporate network) before they're able to sign in to the device with their new password. Otherwise, they can only sign in with their old password because of cached sign in capability in Windows. However, the old password is invalidated by Azure AD during token requests and hence, prevents single sign-on and fails any device-based Conditional Access policies. This issue doesn't occur if you use Windows Hello for Business. 

---

## <a name="azure-ad-register-faq"></a>Часто задаваемые вопросы о регистрации Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>Q: How do I remove an Azure AD registered state for a device locally?

**Ответ.** 
- For Windows 10 Azure AD registered devices, Go to **Settings** > **Accounts** > **Access Work or School**. Выберите учетную запись и щелкните **Отключить**. Device registration is per user profile on Windows 10.
- For iOS and Android, you can use the Microsoft Authenticator application **Settings** > **Device Registration** and select **Unregister device**.
- For macOS, you can use the Microsoft Intune Company Portal application to un-enroll the device from management and remove any registration. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>Q: How can I block users from adding additional work accounts (Azure AD registered) on my corporate Windows 10 devices?

**A:** Enable the following registry to block your users from adding additional work accounts to your corporate domain joined, Azure AD joined or hybrid Azure AD joined Windows 10 devices. This policy can also be used to block domain joined machines from inadvertently getting Azure AD registered with the same user account. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Q: Can I register Android or iOS BYOD devices?

**A:** Yes, but only with the Azure device registration service and for hybrid customers. Не поддерживается с локальной службой регистрации устройств в службах федерации Active Directory (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>Q: How can I register a macOS device?

**A:** Take the following steps:

1.  [Создайте политику соответствия](https://docs.microsoft.com/intune/compliance-policy-create-mac-os).
1.  [Define a Conditional Access policy for macOS devices](../active-directory-conditional-access-azure-portal.md) 

**Примечания:**

- The users included in your Conditional Access policy need a [supported version of Office for macOS](../conditional-access/technical-reference.md#client-apps-condition) to access resources. 
- Во время первой попытки доступа пользователям предлагается зарегистрировать устройство с помощью корпоративного портала.

---
## <a name="next-steps"></a>Дальнейшие действия

- Подробнее о [зарегистрированных устройствах в Azure AD](concept-azure-ad-register.md)
- Подробнее о [присоединенных к Azure AD устройствах](concept-azure-ad-join.md)
- Подробнее о[присоединенных к Azure AD с помощью гибридного присоединения устройствах](concept-azure-ad-join-hybrid.md)
