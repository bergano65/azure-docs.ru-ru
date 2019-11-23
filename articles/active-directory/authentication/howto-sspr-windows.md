---
title: Self-service password reset for Windows - Azure Active Directory
description: How to enable self-service password reset using forgot password at the Windows login screen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44e25efcb068fe51f05dbbde50e8a96da492a735
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381228"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>How to: Enable password reset from the Windows login screen

For machines running Windows 7, 8, 8.1, and 10 you can enable users to reset their password at the Windows login screen. Users no longer have to find a device with a web browser to access the [SSPR portal](https://aka.ms/sspr).

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Общие ограничения

- Password reset is not currently supported from a Remote Desktop or from Hyper-V enhanced sessions.
- Эта функция не поддерживается для сетей с развернутым решением аутентификации 802.1X и заданным параметром "Выполнять непосредственно перед входом пользователя". Чтобы включить эту функцию для сетей с развернутыми решением аутентификации 802.1X, используйте аутентификацию компьютера.
- Hybrid Azure AD joined machines must have network connectivity line of sight to a domain controller to use the new password and update cached credentials.
- If using an image, prior to running sysprep ensure that the web cache is cleared for the built-in Administrator prior to performing the CopyProfile step. More information about this step can be found in the support article [Performance poor when using custom default user profile](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- The following settings are known to interfere with the ability to use and reset passwords on Windows 10 devices
    - Если согласно политике версий Windows 10 до 1809 нужно нажать CTRL+ALT+DEL, **Сброс пароля** не будет работать.
    - Если уведомления на экране блокировки отключены, **Сброс пароля** не будет работать.
    - HideFastUserSwitching со значением Enabled (Вкл.) или 1;
    - DontDisplayLastUserName со значением Enabled (Вкл.) или 1;
    - NoLockScreen со значением Enabled (Вкл.) или 1;
    - EnableLostMode, установленный на устройстве;
    - файл Explorer.exe, замененный на файл пользовательской оболочки.
- The combination of the following specific three settings can cause this feature to not work.
    - Interactive logon: Do not require CTRL+ALT+DEL = Disabled
    - DisableLockScreenAppNotifications = 1 or Enabled
    - IsContentDeliveryPolicyEnforced = 1 or True

## <a name="windows-10-password-reset"></a>Windows 10 password reset

### <a name="windows-10-prerequisites"></a>Windows 10 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 10 devices 
       - Port 443 to `passwordreset.microsoftonline.com` and `ajax.aspnetcdn.com`
       - Windows 10 devices only support machine-level proxy configuration
- Run at least Windows 10, version April 2018 Update (v1803), and the devices must be either:
    - присоединение к Azure AD;
    - присоединение к Azure AD (гибридные устройства).

### <a name="enable-for-windows-10-using-intune"></a>Enable for Windows 10 using Intune

Самый гибкий метод — это развертывание изменений конфигурации для включения сброса пароля в окне входа в систему с помощью Intune. Intune позволяет развертывать изменения конфигурации для определенной группы компьютеров. Этот метод требует регистрации устройства в Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Создание политики конфигурации устройства в Intune

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **Intune**.
1. Создайте профиль конфигурации устройства, последовательно выбрав **Конфигурация устройства** > **Профили** > **Создать профиль**.
   - Введите понятное имя для профиля.
   - При необходимости введите содержательное описание профиля.
   - Выберите платформу **Windows 10 и более поздних версий**.
   - Для типа профиля укажите **Пользовательский**.
1. Настройте **параметры**.
   - В разделе "Параметры OMA-URI" **добавьте** следующий параметр, чтобы включить отображение ссылки сброса пароля.
      - Введите понятное имя, которое указывает на действия параметра.
      - При необходимости введите содержательное описание параметра.
      - Для параметра **OMA-URI** укажите значение `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`.
      - Для параметра **Тип данных** задайте значение **Целое число**.
      - Для параметра **Значение** установите значение **1**.
      - Щелкните **ОК**
   - Щелкните **ОК**
1. Нажмите кнопку **Создать**.
1. This policy can be assigned to specific users, devices, or groups. More information can be found in the article [Assign user and device profiles in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Enable for Windows 10 using the Registry

1. Войдите в Windows PC, используя административные учетные данные.
1. Запустите **regedit** от имени администратора.
1. Настройте следующий раздел реестра:
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Troubleshooting Windows 10 password reset

Журнал аудита Azure AD включают в себя сведения об IP-адресе и типе клиента (ClientType), на котором произошел сброс пароля.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

When users reset their password from the login screen of a Windows 10 device, a low-privilege temporary account called `defaultuser1` is created. Эта учетная запись используется для обеспечения безопасности процесса сброса пароля. Учетная запись имеет случайно сгенерированный пароль, который не отображается при входе в устройство и будет автоматически удален после сброса пользователем пароля. Multiple `defaultuser` profiles may exist but can be safely ignored.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8, and 8.1 password reset

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8, and 8.1 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 7, 8, and 8.1 devices
       - Port 443 to `passwordreset.microsoftonline.com`
- В операционной системе Windows 7 или Windows 8.1 должны быть установлены исправления.
- Должен быть включен протокол TLS 1.2 (см. руководство по [настройке параметров реестра для протокола TLS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)).
- If more than one 3rd party credential provider is enabled on your machine, users will see more than one user profile on the login screen.

> [!WARNING]
> TLS 1.2 must be enabled, not just set to auto negotiate

### <a name="install"></a>Установка

1. Скачайте соответствующий установщик для версии Windows, в которой следует включить этот протокол.
   - Программное обеспечение доступно в Центре загрузки Майкрософт по адресу [https://aka.ms/sspraddin](https://aka.ms/sspraddin).
1. Войдите на компьютер, где следует выполнить установку, и запустите программу установки.
1. После установки настоятельно рекомендуется выполнить перезагрузку.
1. After the reboot, at the login screen choose a user and click "Forgot password?" чтобы инициировать процесс сброса пароля.
1. Завершите процесс, следуя указаниям на экране для сброса пароля.

![Пример для Windows 7 со ссылкой "Забыли пароль?", SSPR flow](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Автоматическая установка

- Для автоматической установки воспользуйтесь командой msiexec /i SsprWindowsLogon.PROD.msi /qn.
- Для автоматического удаления воспользуйтесь командой msiexec /x SsprWindowsLogon.PROD.msi /qn.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Troubleshooting Windows 7, 8, and 8.1 password reset

События регистрируются в журнале как на компьютере, так и в Azure AD. События Azure AD включают в себя сведения об IP-адресе и типе клиента (ClientType), на котором произошел сброс пароля.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

При необходимости для включения подробного ведения журнала можно изменить соответствующий раздел реестра на компьютере. Подробное ведение журнала следует включать только для устранения неполадок.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- To enable verbose logging, create a `REG_DWORD: “EnableLogging”`, and set it to 1.
- To disable verbose logging, change the `REG_DWORD: “EnableLogging”` to 0.

## <a name="what-do-users-see"></a>Что видят пользователи?

Now that you have configured password reset for your Windows devices, what changes for the user? Как они узнают, что можно сбросить пароль на экране входа в систему?

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

When users attempt to sign in, they now see a **Reset password** or **Forgot password** link that opens the self-service password reset experience at the login screen. С помощью этой функции пользователи могут сбросить пароль, не используя другое устройство для получения доступа к браузеру.

Инструкции по использованию этой функции пользователи могут найти в статье [Я не помню свой пароль Azure AD](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="next-steps"></a>Дальнейшие действия

[Plan authentication methods to allow](concept-authentication-methods.md)

[Configure Windows 10](https://docs.microsoft.com/windows/configuration/)
