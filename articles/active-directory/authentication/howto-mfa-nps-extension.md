---
title: Provide Azure MFA capabilities using NPS - Azure Active Directory
description: Добавьте возможности двухфакторной облачной проверки подлинности в существующую инфраструктуру проверки подлинности
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8388d5b22cddcf148c68f35758ccdf797abbcd9e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420647"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Интеграция существующей инфраструктуры NPS с Многофакторной идентификацией Azure

Расширение сервера политики сети (NPS) для MFA Azure добавляет облачные функции многофакторной идентификации в инфраструктуру проверки подлинности на базе существующих серверов. Расширение NPS позволяет добавить телефонный звонок, текстовое сообщение или запрос на подтверждение в мобильном приложении в существующий процесс аутентификации, позволяя обойтись без установки, настройки и поддержания новых серверов. 

Это расширение было создано для организаций, которым необходимо защитить VPN-подключения, не развертывая сервер Azure MFA. Расширение NPS выполняет функции адаптера между RADIUS и облачной службой Azure MFA, предоставляя второй фактор проверки подлинности для федеративных или синхронизированных пользователей.

Если вы используете расширение NPS для многофакторной идентификации Azure, процесс проверки подлинности состоит из следующих компонентов: 

1. **NAS или VPN-сервер** получает запросы от клиентов, использующих VPN-подключение, и преобразует их в запросы RADIUS, направляемые на серверы политики сети. 
2. **Сервер политики сети** подключается к Active Directory и выполняет первичную проверку подлинности по запросам RADIUS. В случае успеха он передает запрос в установленные расширения.  
3. **Расширение NPS** активирует запрос к MFA Azure для проведения дополнительной проверки подлинности. Когда расширение получает ответ (при условии, что запрос MFA выполнен успешно), расширение завершает процесс проверки подлинности, возвращая серверу NPS маркеры безопасности, которые включают утверждение многофакторной идентификации, выданное службой маркеров безопасности Azure.  
4. **Azure MFA** обращается к Azure Active Directory для получения сведений о пользователе и выполняет дополнительную проверку подлинности по тому методу, который настроен для этого пользователя.

На следующей схеме обобщенно представлен поток запросов на проверку подлинности. 

![Схема процесса проверки подлинности](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Планирование развертывания

Расширение NPS автоматически обрабатывает избыточность, поэтому специальная настройка не требуется.

Можно создать столько NPS-серверов с поддержкой Azure MFA, сколько требуется. При установке нескольких серверов для каждого из них следует использовать отдельный сертификат клиента. Создание сертификата для каждого сервера означает, что можно обновлять каждый сертификат по отдельности и не беспокоиться об одновременных простоях всех серверов.

VPN-серверы перенаправляют запросы на аутентификацию, поэтому они должны знать о новых NPS-серверах с поддержкой Многофакторной идентификации Azure.

## <a name="prerequisites"></a>Технические условия

Расширение NPS предназначено для работы с существующей инфраструктурой. Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.

### <a name="licenses"></a>Лицензии

Расширение NPS для Многофакторной идентификации Azure доступно для клиентов с [лицензией Azure MFA](multi-factor-authentication.md) (входит в состав отдельных лицензий Azure AD Premium, EMS и MFA). Лицензии на основе потребления Azure MFA, такие как лицензии на пользователя или лицензии на аутентификацию, несовместимы с расширением NPS. 

### <a name="software"></a>Программное обеспечение

Windows Server 2008 R2 с пакетом обновления 1 (SP1) или более поздняя версия.

### <a name="libraries"></a>Библиотеки

Эти библиотеки устанавливаются автоматически вместе с расширением.

- [Распространяемые пакеты Visual C++ для Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Модуль Microsoft Azure Active Directory для Windows PowerShell, версия 1.1.166](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Если модуль Microsoft Azure Active Directory для Windows PowerShell отсутствует, то он устанавливается с помощью сценария настройки, который запускается в процессе установки. Нет необходимости устанавливать этот модуль заранее.

### <a name="azure-active-directory"></a>Azure Active Directory

Все пользователи, использующие расширение NPS, должны синхронизироваться с Azure Active Directory с помощью Azure AD Connect и должны быть зарегистрированы в службе MFA.

Для установки расширения потребуются учетные данные администратора и идентификатор каталога для вашего клиента Azure AD. Идентификатор каталога вы можете найти на [портале Azure](https://portal.azure.com). Войдите от имени администратора. Search for and select the **Azure Active Directory**, then select **Properties**. Скопируйте значение GUID в поле **Directory ID** (Идентификатор каталога) и сохраните его. Этот GUID используется как идентификатор клиента при установке расширения NPS.

![Поиск идентификатора каталога в разделе свойств для Azure Active Directory](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Требования к сети

Необходимо обеспечить обмен данными между сервером NPS и указанными ниже URL-адресами через порты 80 и 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Additionally, connectivity to the following URLs is required to complete the [setup of the adapter using the provided PowerShell script](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Подготовка среды

Прежде чем устанавливать расширение NPS, следует подготовить среду для обработки трафика аутентификации.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Включение роли NPS на сервере, присоединенном к домену

NPS-сервер подключается к Azure Active Directory и аутентифицирует запросы MFA. Выберите один сервер для этой роли. Рекомендуется выбрать сервер, который не обрабатывает запросы от других служб, так как расширение NPS выдает ошибки для всех запросов, которые не являются запросами RADIUS. NPS-сервер необходимо настроить как сервер основной и дополнительной аутентификации для вашей среды. Он не может передавать запросы RADIUS на другой сервер.

1. Откройте на сервере **мастер добавления ролей и компонентов**, воспользовавшись меню быстрого запуска диспетчера сервера.
2. Щелкните **Установка ролей или компонентов** для используемого типа установки.
3. Выберите роль сервера **Службы политики сети и доступа**. Может появиться окно, сообщающее о необходимых компонентах для выполнения этой роли.
4. Следуйте дальнейшим указаниям мастера, пока не перейдете к странице подтверждения. Щелкните **Установить**.

Теперь назначенный NPS-сервер следует настроить для обработки входящих запросов RADIUS от решения VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Настройка решения VPN для обмена данными с NPS-сервером

В зависимости от того, какое решение VPN используется, действия по настройке политики аутентификации RADIUS могут отличаться. Настройте эту политику, указав NPS-сервер RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Синхронизация пользователей домена с облаком

Возможно, этот шаг уже выполнен в клиенте, но рекомендуется еще раз убедиться, что служба Azure AD Connect недавно синхронизировала базы данных.

1. Войдите на [портал Azure](https://portal.azure.com) с использованием учетной записи администратора.
2. Щелкните **Azure Active Directory** > **Azure AD Connect**.
3. Убедитесь в том, что синхронизация находится в состоянии **Включена** и последняя синхронизация была выполнена менее часа назад.

Если требуется начать новый цикл синхронизации, следуйте инструкциям в разделе [Синхронизация Azure AD Connect: планировщик](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Определение методов проверки подлинности, которые смогут использовать пользователи

Существует два фактора, влияющих на то, какие методы проверки подлинности доступны для развертывания расширения сервера политики сети (NPS):

1. Алгоритм шифрования пароля для обмена данными между клиентом RADIUS (VPN, сервером Netscaler или другим) и серверами NPS.
   - **PAP** supports all the authentication methods of Azure MFA in the cloud: phone call, one-way text message, mobile app notification, OATH hardware tokens, and mobile app verification code.
   - **CHAPV2** и **EAP** поддерживают телефонный звонок или уведомление мобильного приложения.

      > [!NOTE]
      > При развертывании расширения NPS используйте эти факторы, чтобы оценить, какие методы будут доступны для пользователей. Если клиент RADIUS поддерживает протокол PAP, а клиент UX не имеет полей ввода для проверки кода, тогда телефонный звонок или уведомление мобильного приложения являются подходящими вариантами.
      >
      > In addition, if your VPN client UX does support input field and you have configured Network Access Policy - the authentication might succeed, however none of the RADIUS attributes configured in the Network Policy will be applied to neither the Network Access Device, like the RRAS server, nor the VPN client. As a result, the VPN client might have more access than desired or less to no access.
      >

2. Методы ввода, которые может обработать клиентское приложение (VPN, сервер Netscaler или другое). Например, может ли VPN-клиент разрешить пользователю вводить код проверки из мобильного приложения или текста?

Вы можете [отключить неподдерживаемые методы проверки подлинности](howto-mfa-mfasettings.md#verification-methods) в Azure.

### <a name="register-users-for-mfa"></a>Регистрация пользователей для использования MFA

Перед развертыванием и использованием расширения NPS нужно зарегистрировать пользователей, которые должны будут проходить двухфакторную проверку подлинности, в службе MFA. Сейчас же, чтобы проверить расширения после развертывания, необходима по крайней мере одна тестовая учетная запись, полностью зарегистрированная для использования Многофакторной идентификации.

Чтобы приступить к работе с тестовой учетной записью, выполните следующее.

1. Войдите на сайт [https://aka.ms/mfasetup](https://aka.ms/mfasetup) с тестовой учетной записью.
2. Чтобы настроить методы проверки, следуйте инструкциям на экране.
3. [Create a Conditional Access policy](howto-mfa-getstarted.md#create-conditional-access-policy) to require multi-factor authentication for the test account.

## <a name="install-the-nps-extension"></a>Установка расширения NPS

> [!IMPORTANT]
> Установите расширение NPS на другом сервере, который не является точкой доступа VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Скачивание и установка расширения NPS для MFA Azure

1. [Скачайте расширение NPS](https://aka.ms/npsmfa) из Центра загрузки Майкрософт.
2. Скопируйте этот двоичный файл на сервер политики сети, который требуется настроить.
3. Запустите файл *setup.exe* и следуйте инструкциям по установке. При возникновении ошибок проверьте, были ли успешно установлены две библиотеки из раздела необходимых компонентов.

#### <a name="upgrade-the-nps-extension"></a>Upgrade the NPS extension

When upgrading an existing NPS extension install, to avoid a reboot of the underlying server complete the following steps:

1. Uninstall the existing version
1. Run the new installer
1. Restart the Network Policy Server (IAS) service

### <a name="run-the-powershell-script"></a>Запуск сценария PowerShell

Установщик создаст сценарий PowerShell в этом расположении: `C:\Program Files\Microsoft\AzureMfa\Config` (где C:\ — диск установки). При каждом запуске скрипт PowerShell выполняет следующие действия:

- Создать самозаверяющий сертификат.
- Связывает открытый ключ сертификата с субъектом-службой в Azure AD.
- Сохраняет сертификат в хранилище сертификатов на локальном компьютере.
- Предоставляет пользователю в сети доступ к закрытому ключу сертификата.
- Перезапускает расширение NPS.

Если вы не намерены использовать собственные сертификаты (вместо самозаверяющих сертификатов, которые создает скрипт PowerShell), запустите скрипт PowerShell для завершения установки. Если установить расширение на нескольких серверах, каждый из них должен использовать собственный сертификат.

1. Запустите Windows PowerShell от имени администратора.
2. Перейдите в соответствующий каталог.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Запустите сценарий PowerShell, созданный установщиком.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Войдите в Azure AD как администратор.
5. PowerShell запросит ваш идентификатор клиента. Введите GUID каталога, скопированный с портала Azure при выполнении предварительных требований.
6. PowerShell отобразит сообщение об успешном выполнении после завершения сценария.  

Повторите эти действия на всех дополнительных серверах NPS, которые необходимо настроить для балансировки нагрузки.

If your previous computer certificate has expired, and a new certificate has been generated, you should delete any expired certificates. Having expired certificates can cause issues with the NPS Extension starting.

> [!NOTE]
> При использовании собственных сертификатов вместо их создания с помощью сценария PowerShell убедитесь, что они соответствуют соглашению об именовании сервера политики сети. Имя субъекта-службы будет иметь значение **CN \<TenantID\>, а OU — Microsoft NPS Extension**. 

### <a name="certificate-rollover"></a>Смена сертификатов

With release 1.0.1.32 of the NPS extension, reading multiple certificates is now supported. This capability will help facilitate rolling certificate updates prior to their expiration. If your organization is running a previous version of the NPS extension, you should upgrade to version 1.0.1.32 or higher.

Certificates created by the `AzureMfaNpsExtnConfigSetup.ps1` script are valid for 2 years. IT organizations should monitor certificates for expiration. Certificates for the NPS extension are placed in the Local Computer certificate store under Personal and are Issued To the tenant ID provided to the script.

When a certificate is approaching the expiration date, a new certificate should be created to replace it.  This process is accomplished by running the `AzureMfaNpsExtnConfigSetup.ps1` again and keeping the same tenant ID when prompted. This process should be repeated on each NPS server in your environment.

## <a name="configure-your-nps-extension"></a>Настройка расширения NPS

Этот раздел содержит рекомендации по проектированию и успешному развертыванию расширения NPS.

### <a name="configuration-limitations"></a>Ограничения конфигурации

- Расширение NPS для Azure MFA не включает в себя инструменты для переноса пользователей и настроек с сервера MFA в облако. По этой причине мы рекомендуем вам использовать расширение для новых развертываний, а не для существующего развертывания. При использовании расширения в существующем развертывании пользователям придется еще раз подтвердить свою личность, чтобы заполнить сведения для MFA в облаке.  
- The NPS extension uses the UPN from the on-premises Active directory to identify the user on Azure MFA for performing the Secondary Auth. The extension can be configured to use a different identifier like alternate login ID or custom Active Directory field other than UPN. Дополнительные сведения см. в статье [Параметры расширенной конфигурации расширения NPS для Многофакторной идентификации](howto-mfa-nps-extension-advanced.md).
- Не все протоколы шифрования поддерживают все методы проверки.
   - **PAP** поддерживает такие методы: телефонный звонок, одностороннее текстовое сообщение, уведомление мобильного приложения и код проверки мобильного приложения.
   - **CHAPV2** и **EAP** поддерживают телефонный звонок или уведомление мобильного приложения.

### <a name="control-radius-clients-that-require-mfa"></a>Управление клиентами RADIUS, для которых требуется MFA

Когда вы включаете для клиента RADIUS поддержку MFA с помощью расширения NPS, все сеансы проверки подлинности для этого клиента могут выполняться только с использованием MFA. Если вы хотите включить MFA только для отдельных клиентов RADIUS, настройте два сервера NPS и установите расширение только на один из них. Затем настройте для клиентов RADIUS, которые должны использовать MFA, отправку запросов на сервер NPS, на котором установлено это расширение. Всех остальных клиентов RADIUS направьте на другой сервер NPS, для которого расширение не используется.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Подготовка к входу пользователей, не зарегистрированных для MFA

Если у вас есть пользователи, не зарегистрированные для MFA, вы можете выбрать действие, которое будет выполняться при попытке проверки подлинности. Для управления этим поведением используйте параметр реестра *REQUIRE_USER_MATCH*, расположенный в пути *HKLM\Software\Microsoft\AzureMFA*. Здесь есть только один параметр конфигурации:

| Ключ | Value | значение по умолчанию |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | True или false | Не задано (эквивалентно значению True) |

Этот параметр определяет, что нужно делать при проверке подлинности пользователя, не зарегистрированного для MFA. Если этот ключ не существует, не задан или имеет значение True, то для незарегистрированного пользователя проверка MFA считается не пройденной. Если этот ключ имеет значение False, то для незарегистрированного пользователя проверка подлинности выполняется без MFA. Если пользователь зарегистрирован в службе MFA, он должен проходить аутентификацию с помощью MFA, даже если REQUIRE_USER_MATCH имеет значение FALSE.

Вы можете создать этот ключ и присвоить ему значение False, если пользователи подключаются, но еще не зарегистрированы для Azure MFA. Однако такая настройка ключа разрешает вход всем пользователям, которые не зарегистрированы для MFA, поэтому этот ключ следует удалить до начала использования в рабочей среде.

## <a name="troubleshooting"></a>Устранение неисправностей

### <a name="nps-extension-health-check-script"></a>NPS extension health check script

The following script is available on the TechNet Gallery to perform basic health check steps when troubleshooting the NPS extension.

[MFA_NPS_Troubleshooter.ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Как проверить, правильно ли установлен сертификат клиента?

Найдите самозаверяющий сертификат, созданный установщиком в хранилище сертификатов, и убедитесь, что его закрытый ключ имеет разрешения, предоставленные пользователю **NETWORK SERVICE**. У этого сертификата имя субъекта-службы будет иметь значение **CN \<tenantid\>, а OU — Microsoft NPS Extension**.

Self-signed certificates generated by the *AzureMfaNpsExtnConfigSetup.ps1* script also have a validity lifetime of two years. When verifying that the certificate is installed, you should also check that the certificate has not expired.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Как проверить, связан ли сертификат клиента с правильным клиентом в Azure Active Directory?

Откройте командную строку PowerShell и выполните следующие команды:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Эти команды выводят в сеанс PowerShell все сертификаты, благодаря которым ваш клиент связывается с экземпляром расширения NPS. Экспортируйте сертификат клиента в файл в кодировке Base-64 X.509 (CER) без закрытого ключа и сравните его со списком в PowerShell, чтобы найти соответствие.

Следующая команда создаст файл с именем "npscertificate" на диске "C:" в формате CER.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Once you run this command, go to your C drive, locate the file and double-click on it. Перейдите к подробным сведениям и прокрутите вниз до раздела thumbprint. Сравните отпечаток сертификата, установленного на сервере, с этим отпечатком. Отпечатки сертификатов должны совпадать.

Метки времени Valid-From и Valid-Until, которые имеют понятный для человека формат, позволят отфильтровать очевидные несоответствия, если команда возвращает более одного сертификата.

---

### <a name="why-cant-i-sign-in"></a>Почему не удается войти?

Проверьте, не истек ли срок действия пароля. Расширение NPS не поддерживает изменение паролей при входе в систему. Contact your organization's IT Staff for further assistance.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Почему запросы завершаются сбоем с ошибкой маркера ADAL?

Эта ошибка может быть вызвана одной из нескольких причин. Выполните следующие действия для устранения неполадок.

1. Перезагрузите сервер политики сети.
2. Убедитесь, что сертификат клиента установлен правильно.
3. Убедитесь, что сертификат связан с клиентом в Azure AD.
4. Убедитесь, что адрес https://login.microsoftonline.com/ доступен с сервера, на котором выполняется расширение.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Почему проверка подлинности завершается ошибкой, а в журналы HTTP заносится ошибка с сообщением о том, что пользователь не найден?

Убедитесь, что служба AD Connect работает, а нужный пользователь присутствует как в Windows Active Directory, так и в Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Почему в журналах отображаются ошибки подключения HTTP для всех попыток проверки подлинности?

Убедитесь, что адрес https://adnotifications.windowsazure.com доступен с сервера, на котором выполняется расширение NPS.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Why is authentication not working, despite a valid certificate being present?

If your previous computer certificate has expired, and a new certificate has been generated, you should delete any expired certificates. Having expired certificates can cause issues with the NPS Extension starting.

To check if you have a valid certificate, check the local Computer Account's Certificate Store using MMC, and ensure the certificate has not passed its expiry date. To generate a newly valid certificate, rerun the steps under the section "[Run the PowerShell script](#run-the-powershell-script)"

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Управление протоколами TLS и SSL, а также комплектами шифров

Рекомендуем отключить или удалить прежние, менее надежные комплекты шифров (если они не требуются в организации). Соответствующие инструкции см. в статье, посвященной [управлению протоколами SSL и TLS, а также комплектами шифров для AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

### <a name="additional-troubleshooting"></a>Additional troubleshooting

Additional troubleshooting guidance and possible solutions can be found in the article [Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Дальнейшие действия

- Настройте альтернативные идентификаторы для входа или настройте список исключений IP-адресов, которым не нужно выполнять двухфакторную проверку подлинности, в [дополнительных параметрах конфигурации расширения NPS для Многофакторной идентификации](howto-mfa-nps-extension-advanced.md)

- См. дополнительные сведения об интеграции [шлюза удаленных рабочих столов](howto-mfa-nps-extension-rdg.md) и [VPN-серверов](howto-mfa-nps-extension-vpn.md) с помощью расширения NPS.

- [Устранение ошибок, связанных с расширением NPS для Многофакторной идентификации Azure](howto-mfa-nps-extension-errors.md)
