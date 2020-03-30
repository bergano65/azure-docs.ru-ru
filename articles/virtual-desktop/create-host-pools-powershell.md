---
title: Создание пула хостов Windows Virtual Desktop PowerShell - Azure
description: Как создать пул хоста в Windows Virtual Desktop с помощью cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246965"
---
# <a name="create-a-host-pool-with-powershell"></a>Создание пула узлов с помощью PowerShell

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента Виртуального рабочего стола Windows. Каждый пул узлов может содержать группы приложений, с которыми пользователи могут взаимодействовать, как с физическим рабочим столом.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Используйте свой клиент PowerShell для создания пула хостов

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell (если вы еще это не сделали).

Запустите следующий cmdlet, чтобы войти в среду виртуального рабочего стола Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Затем запустите этот cmdlet, чтобы создать новый пул хоста в вашем наемщике Windows Virtual Desktop:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Запустите следующий cmdlet, чтобы создать регистрационный маркер, чтобы разрешить хосту сеанса присоединиться к пулу хоста и сохранить его в новом файле на локальном компьютере. Вы можете указать, как долго токен регистрации действителен, используя параметр -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

После этого запустите этот cmdlet, чтобы добавить пользователей Azure Active Directory в группу настольных приложений по умолчанию для пула хоста.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Cmdlet Add-RdsAppGroupUser** не поддерживает добавление групп безопасности и добавляет в группу приложений только одного пользователя. Если вы хотите добавить несколько пользователей в группу приложений, перезапустите cmdlet с соответствующими именами основных пользователей.

Запустите следующий cmdlet для экспорта регистрационного токена в переменную, которую вы будете использовать позже в [Регистрации виртуальных машин в пул хоста Windows Virtual Desktop.](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Создание виртуальных машин для пула хоста

Теперь вы можете создать виртуальную машину Azure, которая может быть соединена с вашим пулом хостов Windows Virtual Desktop.

Вы можете создать виртуальную машину несколькими способами:

- [Создание виртуальной машины из изображения галереи Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Создание виртуальной машины из управляемого изображения](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Создание виртуальной машины из неуправляемого изображения](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Если вы развертываете виртуальную машину с использованием Windows 7 в качестве хост-ОС, процесс создания и развертывания будет немного другим. Для получения более подробной информации [см. Развертывание виртуальной машины Windows 7 на виртуальном рабочем столе Windows.](deploy-windows-7-virtual-machine.md)

После создания виртуальных компьютеров, хост сеанса, [примените лицензию Windows к VM-сессии](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) для запуска виртуальных компьютеров Windows или Windows Server без оплаты другой лицензии. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Подготовка виртуальных машин для установок агента windows Virtual Desktop

Вам нужно сделать следующие вещи, чтобы подготовить ваши виртуальные машины, прежде чем вы можете установить Windows Virtual Desktop агентов и зарегистрировать виртуальные машины для вашего Windows Virtual Desktop хост пула:

- Вы должны домен-присоединиться к машине. Это позволяет пользователям входящих Windows Virtual Desktop быть отображены из своей учетной записи Active Directory в учетную запись Active Directory и успешно получить доступ к виртуальной машине.
- Вы должны установить функцию удаленного рабочего стола (RDSH), если виртуальная машина работает под управлением ОС Windows Server. Роль RDSH позволяет агентам Windows Virtual Desktop правильно устанавливаться.

Чтобы успешно объединить доменовый станок, сделайте следующие вещи на каждой виртуальной машине:

1. [Подключайтесь к виртуальной машине](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) с предоставленными учетными данными при создании виртуальной машины.
2. На виртуальной машине запустите **панель управления** и выберите **систему.**
3. Выберите **имя компьютера,** выберите **настройки изменения,** а затем выберите **Изменение...**
4. Выберите **домен,** а затем введите домен Active Directory в виртуальной сети.
5. Authenticate с учетной записью домена, которая имеет привилегии для доменных машин.

    >[!NOTE]
    > Если вы присоединяете виртуальные машины к среде доменных служб Active Directory (Azure AD DS), убедитесь, что пользователь, который присоединяется к домену, также является участником [группы администраторов AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Регистрация виртуальных машин в пуле хостов Windows Virtual Desktop

Регистрация виртуальных машин в пуле хостов Windows Virtual Desktop так же проста, как установка агентов Windows Virtual Desktop.

Чтобы зарегистрировать агенты Windows Virtual Desktop, сделайте следующее на каждой виртуальной машине:

1. [Подключайтесь к виртуальной машине](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) с предоставленными учетными данными при создании виртуальной машины.
2. Скачать и установить Windows Виртуальный настольный агент.
   - Скачать [Windows Виртуальный настольный агент](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Право нажмите на загруженный установщик, выберите **Свойства,** выберите **Unblock,** затем выберите **OK.** Это позволит вашей системе доверять установщику.
   - Запустите установщик. Когда установщик запросит у вас регистрационный токен, введите значение, полученное от **Export-RdsRegistrationInfo** cmdlet.
3. Скачать и установить Windows Виртуальный настольный агент Bootloader.
   - Скачать [Windows Виртуальный настольный агент Bootloader](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Право нажмите на загруженный установщик, выберите **Свойства,** выберите **Unblock,** затем выберите **OK.** Это позволит вашей системе доверять установщику.
   - Запустите установщик.

>[!IMPORTANT]
>Чтобы усилить защиту среды Виртуального рабочего стола Windows в Azure, мы рекомендуем не открывать входящий порт 3389 для виртуальных машин. Виртуальный рабочий стол Windows не требует открытия входящего порта 3389, чтобы пользователи могли получить доступ к виртуальным машинам пула узла. Если вам все же нужно открыть порт 3389 для устранения неполадок, мы рекомендуем использовать[JIT-доступ к виртуальным машинам](../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы сделали пул хоста, вы можете заполнить его с помощью RemoteApps. Дополнительные сведения о том, как управлять приложениями в Виртуальном рабочем столе Windows см. в руководстве по управлению группами приложений.

> [!div class="nextstepaction"]
> [Управление группами приложений](./manage-app-groups.md)
