---
title: Устранение проблем Windows Виртуальный рабочий стол сессии хост - Azure
description: Как решить проблемы при настройке windows Virtual Desktop сессии хост виртуальных машин.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127584"
---
# <a name="session-host-virtual-machine-configuration"></a>Конфигурация виртуальной машины узла сеанса

Используйте эту статью для устранения неполадок при настройке виртуальных компьютеров (VM) с настройкой виртуальных компьютеров для настольных компьютеров Windows Virtual Desktop.

## <a name="provide-feedback"></a>Отзывы

Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="vms-are-not-joined-to-the-domain"></a>VMs не соединены с доменом

Следуйте этим инструкциям, если у вас возникли проблемы с присоединением VMs к домену.

- Присоединяйтесь к VM вручную, используя процесс в [Join a Windows Server виртуальной машины для управляемого домена](../active-directory-domain-services/join-windows-vm.md) или с помощью [шаблона соединения домена.](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
- Попробуйте пропоить доменное имя из командной строки на VM.
- Просмотрите список сообщений об ошибках domain join в [Сообщениях об ошибках Troubleshooting Join.](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)

### <a name="error-incorrect-credentials"></a>Ошибка: Неправильные учетные данные

**Причина:** При входе учетных данных в исправления шаблона шаблона Azure Resource Manager была сделана опечатка.

**Исправление:** Примите одно из следующих действий для того чтобы разрешить.

- Вручную добавляйте в домене визы.
- Передислоцировать шаблон после подтверждения учетных данных. Смотрите [Создать пул хоста с PowerShell](create-host-pools-powershell.md).
- Присоединяйтесь к VMs-миностам с помощью шаблона с [соединениями существующего Windows VM с aD domain.](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

### <a name="error-timeout-waiting-for-user-input"></a>Ошибка: Тайм-аут ожидания ввода пользователя

**Причина:** Учетная запись, используемая для заполнения соединения домена, может иметь многофакторную аутентификацию (MFA).

**Исправление:** Примите одно из следующих действий для того чтобы разрешить.

- Временно удалить МИД для учетной записи.
- Используйте учетную запись службы.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Ошибка: учетная запись, используемая во время подготовки, не имеет разрешений на завершение операции

**Причина:** Используемая учетная запись не имеет разрешений на присоединение ВМ к домену из-за соответствия требованиям и нормативным требованиям.

**Исправление:** Примите одно из следующих действий для того чтобы разрешить.

- Используйте учетную запись, которая является членом группы Администратора.
- Предоставь необходимые разрешения на использование учетной записи.

### <a name="error-domain-name-doesnt-resolve"></a>Ошибка: доменное имя не разрешится

**Причина 1:** Виртуальные виртуальные технологии находятся в виртуальной сети, не связанной с виртуальной сетью (VNET), где находится домен.

**Исправление 1:** Создайте вонючий VNET между VNET, где были подготовлены VMs, и VNET, где работает контроллер домена (DC). Смотрите [Создать виртуальную сеть пиринга - менеджер ресурсов, различные подписки](../virtual-network/create-peering-different-subscriptions.md).

**Причина 2:** При использовании служб ы идоена Azure Active Directory Domain Services (Azure AD DS) виртуальная сеть не имеет обновленных настроек DNS-сервера для указать на управляемые контроллеры домена.

**Исправление 2:** Чтобы обновить настройки DNS для виртуальной сети, содержащей DD Azure AD, [см.](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)

**Причина 3:** Настройки DNS-сервера сетевого интерфейса не указывают на соответствующий DNS-сервер в виртуальной сети.

**Исправление 3:** Выполняйте одно из следующих действий для решения, следуя за шагами в «Изменении DNS-серверов».
- Измените настройки DNS-сервера сетевого интерфейса на **пользовательские** шаги с [серверов Change DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers) и укажите частные IP-адреса DNS-серверов в виртуальной сети.
- Измените настройки DNS-сервера сетевого интерфейса на **унаследованные от виртуальной сети** с помощью шагов от [серверов Change DNS,](../virtual-network/virtual-network-network-interface.md#change-dns-servers)а затем измените настройки DNS-сервера виртуальной сети с шагами от [серверов Change DNS.](../virtual-network/manage-virtual-network.md#change-dns-servers)

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows Virtual Desktop Agent и Windows Virtual Desktop Boot Loader не установлены

Рекомендуемый способ предоставления виртуальных изображений — это использование шаблона **пула пула ресурсов** Azure Resource Manager Create and provision Windows Virtual Desktop. Шаблон автоматически устанавливает Windows Virtual Desktop Agent и Windows Virtual Desktop Agent Boot Loader.

Следуйте этим инструкциям, чтобы подтвердить, что компоненты установлены и проверить на наличие сообщений об ошибках.

1. Подтвердите, что эти два компонента установлены путем проверки программ**Programs** > **и функций**программ и функций **панели управления.** >  Если **Windows Virtual Desktop Agent** и Windows Virtual Desktop Agent Boot **Loader** не видны, они не установлены на VM.
2. Откройте **исследователь файлов** и перейдите на **C: »Окна»Темп-ScriptLog.log**. Если файл отсутствует, это указывает на то, что PowerShell DSC, установившее два компонента, не смогло работать в предоставленном контексте безопасности.
3. Если файл **C: «Окна»Temp-ScriptLog.log** присутствует, откройте его и проверьте сообщения об ошибках.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Ошибка: Windows Виртуальный настольный агент и Windows Виртуальный настольный агент Загрузка погрузчик отсутствуют. C: «Окна»Темп-ScriptLog.log также отсутствует

**Причина 1:** Учетные данные, предоставленные при вводах для шаблона управления ресурсами Azure, были неверными или разрешения были недостаточными.

**Исправление 1:** Вручную добавьте недостающие компоненты в ВМ, используя [Создать пул хоста с PowerShell.](create-host-pools-powershell.md)

**Причина 2:** PowerShell DSC удалось запустить и выполнить, но не удалось завершить, как он не может войти в Windows Virtual Desktop и получить необходимую информацию.

**Исправление 2:** Подтвердите элементы в следующем списке.

- Убедитесь, что учетная запись не имеет МИД.
- Подтвердите, что имя клиента точно еде и арендатор существует в Windows Virtual Desktop.
- Подтвердите, что учетная запись имеет по крайней мере разрешения вкладчика RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Ошибка: Проверка подлинности не удалось, ошибка в C: »Окна »Темп»ScriptLog.log

**Причина:** PowerShell DSC был в состоянии выполнить, но не может подключиться к Windows Virtual Desktop.

**Исправление:** Подтвердите элементы в следующем списке.

- Вручную регистрируйте виртуальные компьютеры с помощью службы Windows Virtual Desktop.
- Подтверждение учетной записи, используемой для подключения к Windows Virtual Desktop, имеет разрешения на создание пулов хоста.
- Подтверждать учетную запись не имеет МИД.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows Virtual Desktop Agent не регистрируется в службе Windows Virtual Desktop

Когда windows Virtual Desktop Agent впервые устанавливается на всхожие ми -М (вручную или через шаблон Azure Resource Manager и PowerShell DSC), он предоставляет регистрационный токен. В следующем разделе рассматриваются проблемы устранения неполадок, применимые к агенту Windows Virtual Desktop Agent и маркеру.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Ошибка: Статус, поданный в Get-RdsSessionHost cmdlet показывает статус недоступного

![Get-RdsSessionHost cmdlet показывает статус недоступного.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Причина:** Агент не может обновиться до новой версии.

**Исправление:** Следуйте этим инструкциям, чтобы вручную обновить агента.

1. Загрузите новую версию агента на хост-сессии VM.
2. Запуск диспетчера задач и, в вкладке службы, остановить службу RDAgentBootLoader.
3. Запустите установщик для новой версии Windows Virtual Desktop Agent.
4. При запросе на регистрацию токена удалите запись INVALID_TOKEN и нажмите далее (новый маркер не требуется).
5. Завершите установку Мастера.
6. Откройте диспетчер задач и запустите сервис RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Ошибка: Windows Virtual Desktop Agent запись реестра Зарегистрировано показывает значение 0

**Причина:** Регистрация токена истекла или была создана со сроком действия 999999.

**Исправление:** Следуйте этим инструкциям, чтобы исправить ошибку реестра агента.

1. Если маркер регистрации уже есть, удалите его с помощью Remove-RDSRegistrationInfo.
2. Создание нового токена с помощью Rds-NewRegistrationInfo.
3. Подтвердите, что параметр -ExpriationHours установлен на 72 (максимальное значение 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Ошибка: Windows Virtual Desktop агент не сообщает сердцебиение при запуске Get-RdsSessionHost

**Причина 1:** Сервис RDAgentBootLoader остановлен.

**Исправление 1:** Запуск диспетчера задач и, если вкладка службы обслуживания сообщает о прекращении состояния службы RDAgentBootLoader, запустите службу.

**Причина 2:** Порт 443 может быть закрыт.

**Исправление 2:** Следуйте этим инструкциям, чтобы открыть порт 443.

1. Подтвердите, что порт 443 открыт, загрузив инструмент PSPing из [инструментов Sysinternal.](/sysinternals/downloads/psping/)
2. Установите PSPing на VM-хозяйку сеанса, где работает агент.
3. Откройте запрос команды в качестве администратора и оформите ниже:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Подтвердите, что PSPing получил информацию обратно от RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Проблемы устранения неполадок с стеками Windows Virtual Desktop

Стек Windows Virtual Desktop автоматически устанавливается с Windows Server 2019. Используйте Microsoft Installer (MSI) для установки стека бок о бок на Microsoft Windows Server 2016 или Windows Server 2012 R2. Для Microsoft Windows 10, Windows Virtual Desktop бок о бок стек включен с **enablesxstackrs.ps1**.

Существует три основных способа установки или включения в пул пула сессий:

- С помощью менеджера ресурсов Azure Создание и предоставление нового шаблона **пула хостов Windows Virtual Desktop**
- Будучи включенным и включенным на основное изображение
- Устанавливается или включена вручную на каждом VM (или с расширениями/PowerShell)

Если у вас возникли проблемы со стеком Windows Virtual Desktop, введите команду **qwinsta** из запроса команды, чтобы подтвердить, что рядом стек установлен или включен.

Выход **qwinsta** будет перечислять **rdp-sxs** в выходе, если рядом с текетом установлен и включен.

![Рядом с стеком установлен или включен qwinsta, указанный как rdp-sxs в выходе.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Изучите записи реестра, перечисленные ниже, и подтвердите, что их значения совпадают. Если ключи реестра отсутствуют или значения несовместимы, следуйте инструкциям в [«Создать пул хоста» с PowerShell](create-host-pools-powershell.md) о том, как переустановить стек бок о бок.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Ошибка: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE код ошибки.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Причина:** Стек бок о бок не устанавливается на VM-хозяйку сеанса.

**Исправление:** Следуйте этим инструкциям, чтобы установить стек бок о бок на VM-хозяйских сеансах.

1. Используйте протокол удаленного рабочего стола (RDP), чтобы попасть непосредственно в VM-сессию в качестве локального администратора.
2. Скачать и импортировать [модуль Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell, если вы еще не сделали этого, а затем запустите этот cmdlet, чтобы войти в свой аккаунт:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Установите стек бок о бок с помощью [создания пула с PowerShell.](create-host-pools-powershell.md)

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Как исправить Windows Virtual Desktop бок о бок стек, который неисправности

Известны обстоятельства, которые могут привести к неисправности стека бок о бок:

- Не соблюдение правильного порядка шагов для обеспечения стека
- Автоматическое обновление до Windows 10 Улучшенный универсальный диск (EVD)
- Отсутствует роль удаленного рабочего стола (RDSH)
- Запуск enablessstackrcrc.ps1 несколько раз
- Запуск enablessstackrcrc.ps1 в учетной записи, которая не имеет локальных привилегий админ

Инструкции в этом разделе могут помочь вам удалить Windows Virtual Desktop бок о бок стек. После того, как вы удалите рядом стек, перейдите на "Регистрация VM с Windows Virtual Desktop хост бассейн" в [Создать пул хоста с PowerShell](create-host-pools-powershell.md) для переустановки бок о бок стек.

VM, используемый для выполнения исправления, должен находиться в той же подсети и домене, что и VM с неисправным стеком.

Следуйте этим инструкциям для запуска исправления из той же подсети и домена:

1. Подключитесь к стандартному протоколу удаленного рабочего стола (RDP) к VM, откуда будет применено исправление.
2. Скачать PsExec от https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Распаковать загруженный файл.
4. Запустите запрос команды в качестве локального администратора.
5. Перейдите к папке, где PsExec был расстегнут.
6. Из запроса команды используйте следующую команду:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname — это название машины VM с неисправным стеком.

7. Примите лицензионное соглашение PsExec, нажав На кнопку «Согласен».

    ![Скриншот лицензионного соглашения с программным обеспечением.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Этот диалог будет отображаться только при первом запуске PsExec.

8. После того, как сеанс оперативного реагирования команды открывается на VM с неисправным стеком бок о бок, запустите qwinsta и подтвердите, что запись с именем rdp-sxs доступна. Если нет, то в VM не присутствует стек, поэтому проблема не привязана к стеку.

    ![Запрос команды администратора](media/AdministratorCommandPrompt.png)

9. Выполнить следующую команду, которая будет перечислять компоненты Майкрософт, установленные на VM с неисправным стеком бок о бок.

    ```cmd
        wmic product get name
    ```

10. Выполнить команду ниже с именами продуктов из шага выше.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Удалите все продукты, которые начинаются с "Удаленный рабочий стол".

12. После того, как все компоненты Windows Virtual Desktop были неустановлены, следуйте инструкциям для вашей операционной системы:

13. Если ваша операционная система windows Server, перезапустите VM, который имел неисправный стек бок о бок (либо с порталом Azure, либо с помощью инструмента PsExec).

Если ваша операционная система Microsoft Windows 10, продолжайте с инструкциями ниже:

14. От VM под управлением PsExec, откройте File Explorer и скопировать disablesstackrcrc.ps1 до системного привода VM с неисправным стеком.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname — это название машины VM с неисправным стеком.

15. Рекомендуемый процесс: от инструмента PsExec, запустите PowerShell и перейдите в папку с предыдущего шага и запустите disablessstackrc.ps1. Кроме того, вы можете запустить следующие cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Когда cmdlets сделаны идущими, перезапустите VM с неисправным бок о бок стеком.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Режим удаленного лицензирования рабочего стола не настроен

Если вы войдите в многосессию Windows 10 Enterprise с помощью административной учетной записи, вы можете получить уведомление, в которое говорится: «Удаленный режим лицензирования рабочего стола не настроен, службы удаленного рабочего стола перестанут работать в X дней. На сервере Connection Broker используйте серверный менеджер для указания режима лицензирования удаленного рабочего стола".

Если срок истекает, появится сообщение об ошибке, в которое говорится: "Удаленная сессия была отключена из-за того, что для этого компьютера нет лицензий доступа к клиенту Remote Desktop".

Если вы видите любое из этих сообщений, это означает, что изображение не имеет последних обновлений Windows установлено или что вы устанавливаете режим лицензирования удаленного рабочего стола через групповую политику. Выполните следующие разделы, чтобы проверить настройки групповой политики, определить версию многосессионной windows 10 Enterprise и установить соответствующее обновление.  

>[!NOTE]
>Windows Virtual Desktop требует сярприза для доступа к клиенту RDS (CAL), когда пул хоста содержит хосты сеансов Windows Server. Чтобы узнать, как настроить RDS CAL, [см. Лицензию развертывания RDS с лицензиями на доступ к клиентам.](/windows-server/remote/remote-desktop-services/rds-client-access-license/)

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Отпустите настройки политики группы лицензирования удаленного рабочего стола

Проверьте настройки групповой политики, открыв редактор групповой политики в VM и перенаправившись в **административные шаблоны** > **Windows Components** > Remote Desktop Services Remote Desktop**Services** > **Remote Desktop Session Licensing** > **Set** > **the Remote Desktop licensing mode.** Если настройка групповой политики **включена,** измените его на **отключение.** Если он уже отключен, то оставьте его как есть.

>[!NOTE]
>Если вы установите групповую политику в домене, отпустите эту настройку в политиках, ориентированных на эти многосессионные вммиговые компьютеры Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Определите, какую версию многосессионной версии Windows 10 Enterprise вы используете

Чтобы проверить, какая версия многосессионной windows 10 Enterprise у вас есть:

1. Вопийте с вашей учетной записью админ.
2. Введите "О" в панель поиска рядом с меню "Пуск".
3. Выберите **о вашем компьютере**.
4. Проверьте номер рядом с "Версией". Номер должен быть либо "1809" или "1903", как показано на следующем изображении.

    ![Скриншот окна спецификаций Windows. Номер версии выделен синим цветом.](media/windows-specifications.png)

Теперь, когда вы знаете свой номер версии, перейдите вперед к соответствующему разделу.

### <a name="version-1809"></a>Версия 1809

Если номер вашей версии говорит "1809", [установите обновление KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Версия 1903

Передислоцировать операционную систему с последней версией Windows 10, версия 1903 изображение из галереи Azure.

## <a name="next-steps"></a>Дальнейшие действия

- Для обзора устранения неполадок Windows Virtual Desktop и треков эскалации [см.](troubleshoot-set-up-overview.md)
- Для устранения неполадок при создании пула арендатора и хоста в среде Windows Virtual Desktop [см.](troubleshoot-set-up-issues.md)
- Для устранения неполадок при настройке виртуальной машины (VM) в Windows Virtual Desktop [см.](troubleshoot-vm-configuration.md)
- Чтобы устранить неполадки с подключением клиентов Windows Virtual Desktop, [см.](troubleshoot-service-connection.md)
- Для устранения неполадок с клиентами Remote Desktop просмейте [о устранении проблем с клиентом Remote Desktop](troubleshoot-client.md)
- Для устранения неполадок при использовании [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)PowerShell с Windows Virtual Desktop см.
- Чтобы узнать больше об услуге, [см.](environment-setup.md)
- Чтобы пройти через учебник по устранению неполадок, [см.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](../azure-resource-manager/templates/deployment-history.md).
