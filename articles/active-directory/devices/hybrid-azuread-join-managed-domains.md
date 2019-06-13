---
title: Настройка гибридного присоединения к Azure Active Directory для управляемых доменов | Документация Майкрософт
description: Сведения о том, как настроить гибридное присоединение к Azure Active Directory для управляемых доменов.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: efa653ecf306f5ac5eefaddd61d98e81f919876d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513307"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Руководство по Настройка гибридного присоединения к Azure Active Directory для управляемых доменов

Подобно пользователю, устройство — это еще одно основное удостоверение, которое необходимо защитить, чтобы с его помощью также обеспечить постоянную защиту ресурсов независимо от того, где вы находитесь. Для этого разместите удостоверения своих устройств в Azure AD и управляйте такими удостоверениями с помощью одного из следующих методов:

- присоединение к Azure AD;
- гибридное присоединение к Azure AD;
- регистрация в Azure AD.

Размещение устройств в Azure AD позволяет увеличить эффективность работы пользователей благодаря выполнению единого входа для облачных и локальных ресурсов. В то же время вы можете защитить доступ к облачным и локальным ресурсам с помощью [условного доступа](../active-directory-conditional-access-azure-portal.md).

Из этого руководства вы узнаете, как настроить гибридное присоединение к Azure AD для компьютеров, подключенных к домену AD в управляемой среде. 

Управляемую среду можно развернуть при помощи [синхронизации хэша паролей](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) или [сквозной аутентификации](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) с [единым входом](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).
В этих сценариях не требуется настраивать сервер федерации для проверки подлинности.

> [!div class="checklist"]
> * Настройка гибридного присоединения к Azure AD.
> * Включение устройств Windows нижнего уровня.
> * Проверка присоединенных устройств.
> * Устранение неполадок

## <a name="prerequisites"></a>Предварительные требования

В данном руководстве предполагается, что вы ознакомлены со следующими темами.

- [Общие сведения об управлении устройствами в Azure Active Directory](../device-management-introduction.md)
- [Как планировать гибридную реализацию присоединения к Azure Active Directory](hybrid-azuread-join-plan.md)
- [Как выполнить контролируемую проверку гибридного присоединения к Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD не поддерживает смарт-карты и сертификаты в управляемых доменах.

Чтобы настроить сценарий в этой статье, вам потребуется [последняя версия Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 или более поздняя версия).

Проверьте, синхронизированы ли в Azure AD Connect объекты-компьютеры гибридных устройств, которые нужно присоединить к Azure AD. Если объекты-компьютеры принадлежат конкретным подразделениям (OU), эти подразделения также нужно настроить для синхронизации в Azure AD Connect. Дополнительные сведения о том, как синхронизировать объекты-компьютеры с помощью Azure AD Connect, см. в статье о [настройке фильтрации с помощью Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Начиная с версии 1.1.819.0 Azure AD Connect предоставляет мастер для настройки гибридного присоединения к Azure AD. Этот мастер позволяет значительно упростить настройку. Связанный мастер настраивает точки подключения службы (SCP) для регистрации устройств.

Действия по настройке в этой статье основаны на данном мастере.

Для гибридного присоединения к Azure AD требуется, чтобы устройства имели доступ к следующим ресурсам Майкрософт из сети организации:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- [https://autologon.microsoftazuread-sso.com](`https://autologon.microsoftazuread-sso.com`)(если вы используете или планируете использовать простой единый вход).

Если вашей организации требуется доступ к Интернету через исходящий прокси-сервер, корпорация Майкрософт рекомендует [реализовать автоматическое обнаружение веб-прокси (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)), чтобы обеспечить регистрацию устройств в Azure AD на компьютерах Windows 10. Если у вас возникли проблемы с настройкой и администрированием WPAD, перейдите к статье об [устранении неполадок с автоматическим обнаружением]https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10). 

Если вы не используете WPAD и вам требуется настроить параметры прокси-сервера на компьютере, в Windows 10, начиная с версии 1709, для этого нужно [настроить параметры WinHTTP с использованием объекта групповой политики (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Если вы настроите параметры прокси-сервера на компьютере с помощью параметров WinHTTP, любые компьютеры, которым не удается подключиться к настроенному прокси-серверу, не смогут подключиться к Интернету.

Если вашей организации требуется доступ в Интернет через аутентифицированный исходящий прокси-сервер, вы должны убедиться, что ваши компьютеры с Windows 10 могут успешно пройти аутентификацию на этом прокси-сервере. Так как компьютеры с Windows 10 выполняют регистрацию устройства с помощью контекста компьютера, необходимо настроить аутентификацию исходящего прокси-сервера с использованием контекста компьютера. Обратитесь к поставщику исходящего прокси-сервера, чтобы узнать требования к конфигурации.

## <a name="configure-hybrid-azure-ad-join"></a>Настройка гибридного присоединения к Azure AD.

Чтобы настроить гибридное присоединение к Azure AD с помощью Azure AD Connect, вам потребуется следующее:

- учетные данные глобального администратора для клиента Azure AD;  
- учетные данные администратора предприятия для каждого леса;

**Чтобы настроить гибридное присоединение к Azure AD с использованием Azure AD Connect, сделайте следующее.**

1. Запустите Azure AD Connect и щелкните **Настройка**.

   ![Экран приветствия](./media/hybrid-azuread-join-managed-domains/11.png)

1. На странице **Дополнительные задачи** выберите **Настройка параметров устройств** и нажмите кнопку **Далее**.

   ![Дополнительные задачи](./media/hybrid-azuread-join-managed-domains/12.png)

1. На странице **Обзор** щелкните **Далее**.

   ![Обзор](./media/hybrid-azuread-join-managed-domains/13.png)

1. На странице **Подключение к Azure AD** укажите учетные данные глобального администратора для клиента Azure AD.  

   ![Подключение к Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. На странице **Параметры устройств** выберите **Настроить гибридное присоединение к Azure AD** и щелкните **Далее**.

   ![Параметры устройств](./media/hybrid-azuread-join-managed-domains/15.png)

1. На странице **SCP** выполните приведенные ниже действия с каждым лесом, для которого требуется с помощью Azure AD Connect настроить конечную точку подключения службы, после чего щелкните **Далее**.

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Выберите лес.
   1. Выберите службу аутентификации.
   1. Щелкните **Добавить**, чтобы ввести учетные данные администратора предприятия.

1. На странице **Операционные системы устройств** выберите операционные системы, используемые устройствами в вашей среде Active Directory, и щелкните **Далее**.

   ![Операционная система устройства](./media/hybrid-azuread-join-managed-domains/17.png)

1. На экране **Готово к настройке** щелкните **Настроить**.

   ![Готово к настройке](./media/hybrid-azuread-join-managed-domains/19.png)

1. На странице **Конфигурация завершена** щелкните **Выйти**.

   ![Завершение настройки](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Включение устройств Windows нижнего уровня.

Если некоторые из присоединенных к домену устройств являются устройствами Windows нижнего уровня, выполните указанные ниже действия.

- Настройка параметров локальной интрасети для регистрации устройств.
- Настройка простого единого входа
- Установка Microsoft Workplace Join для компьютеров Windows нижнего уровня

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Настройка параметров локальной интрасети для регистрации устройств.

Чтобы успешно выполнить гибридное присоединение устройств Windows нижнего уровня к Azure AD и избежать запросов на предоставление сертификатов при аутентификации этих устройств в Azure AD, можно отправить на присоединенные к домену устройства политику для добавления следующих URL-адресов в зону локальной интрасети в Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Кроме того, необходимо включить параметр **Allow updates to status bar via script** (Разрешить обновление строки состояния с помощью сценария) в локальной зоне интрасети пользователя.

### <a name="configure-seamless-sso"></a>Настройка простого единого входа

Для успешного завершения гибридного присоединения к Azure AD устройств Windows нижнего уровня в управляемом домене, использующем [синхронизацию хэша паролей (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) или [сквозную проверку подлинности (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) для проверки подлинности в Azure AD, необходимо также включить [простой единый вход](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Установка Microsoft Workplace Join для компьютеров Windows нижнего уровня

Чтобы зарегистрировать устройства Windows нижнего уровня, необходимо установить в организации [Microsoft Workplace Join для компьютеров, на которых не используется Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Это средство доступно в Центре загрузки Майкрософт.

Развернуть пакет можно с помощью системы распространения программного обеспечения, например  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Этот пакет поддерживает параметры стандартной автоматической установки с использованием параметра quiet. В текущей ветви System Center Configuration Manager доступны дополнительные преимущества предыдущих версий, такие как возможность отслеживать ход регистрации.

Установщик создает в системе запланированную задачу, которая выполняется в контексте пользователя. Задача запускается в момент входа пользователя в систему Windows. Эта задача автоматически присоединяет устройство к Azure AD, используя учетные данные пользователя, после аутентификации с помощью Azure AD.

## <a name="verify-the-registration"></a>Проверка регистрации

Чтобы проверить состояние регистрации устройства в клиенте Azure, можно использовать командлет **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** в **[модуле PowerShell для Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

Командлет **Get-MSolDevice** можно использовать, чтобы получить сведения о службе.

- Должен существовать объект с **идентификатором устройства**, который совпадает с идентификатором в клиенте Windows.
- Значением **DeviceTrustType** должно быть **Присоединено к домену**. Это эквивалентно состоянию **Гибридные устройства, присоединенные к Azure AD** на странице "Устройства" на портале Azure AD.
- Для устройств, которые используются при условном доступе, значением **Включено** должно быть **True**, а значением **DeviceTrustLevel** — **Управляемый**.

**Вот как можно просмотреть сведения о службе.**

1. Запустите **Windows PowerShell** от имени администратора.
1. Введите `Connect-MsolService` для подключения к своему клиенту Azure.  
1. Введите `get-msoldevice -deviceId <deviceId>`.
1. Убедитесь, что параметр **Включено** имеет значение **True**.

## <a name="troubleshoot-your-implementation"></a>Устранение неполадок реализации

Если возникают проблемы с настройкой гибридного присоединения к Azure AD для устройств Windows, присоединенных к домену, ознакомьтесь со следующими разделами:

- [Устранение неполадок на устройствах под управлением Windows 10 и Windows Server 2016 с гибридным присоединением к Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Troubleshooting hybrid Azure Active Directory joined down-level devices](troubleshoot-hybrid-join-windows-legacy.md) (Устранение неполадок на устройствах нижнего уровня с гибридным присоединением к Azure Active Directory)

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения об управлении удостоверениями устройств на портале Azure AD см. [здесь](device-management-azure-portal.md).
