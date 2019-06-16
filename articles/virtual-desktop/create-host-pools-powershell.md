---
title: Создание пула узла Апробация виртуального рабочего стола Windows с помощью PowerShell, Azure
description: Как создать кластер узла в Апробация виртуального рабочего стола Windows с помощью командлетов PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: helohr
ms.openlocfilehash: a58e059e800b13d01ba8e50880bd75077d4418ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833970"
---
# <a name="create-a-host-pool-with-powershell"></a>Создание пула узлов с помощью PowerShell

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента предварительной версии Виртуального рабочего стола Windows. Каждый пул узлов может содержать группы приложений, с которыми пользователи могут взаимодействовать, как с физическим рабочим столом.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Используйте клиент PowerShell для создания пула узла

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще это не сделали).

Выполните следующий командлет для входа в среде виртуального рабочего стола Windows

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Затем выполните этот командлет, чтобы создать новый пул узлов в вашем клиенте виртуального рабочего стола Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Выполните следующий командлет для создания маркера регистрации для авторизации узла сеансов для присоединения к пулу узла и сохраните его в новый файл на локальном компьютере. Можно указать, как долго маркер регистрации является допустимым, используя параметр - ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

После этого запустите этот командлет, чтобы добавить пользователей Azure Active Directory в группу по умолчанию классического приложения для пула узла.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**RdsAppGroupUser добавить** командлет не поддерживает добавление групп безопасности и добавит только сам одного пользователя одновременно в группу приложений. Если вы хотите добавить нескольких пользователей в группу приложений, повторно запустите командлет с именами участника-пользователя.

Выполните следующий командлет, чтобы экспортировать маркер регистрации в переменной, которая будет использоваться позже при работе [регистрация виртуальных машин в пул узлов виртуального рабочего стола Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Создание виртуальных машин для узлов кластера

Теперь можно создать виртуальную машину Azure, можно объединить в пул узлов виртуального рабочего стола Windows.

Виртуальную машину можно создать несколькими способами:

- [Создание виртуальной машины из образа коллекции Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Создание виртуальной машины из управляемого образа](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Создание виртуальной машины из неуправляемого образа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Подготовка виртуальных машин для установки агентов Апробация виртуального рабочего стола Windows

Вам потребуется выполнить следующие действия для подготовки виртуальных машин, прежде чем можно установить агенты виртуального рабочего стола Windows и зарегистрировать виртуальные машины в пул узлов виртуального рабочего стола Windows.

- Необходимо выполнить присоединение к домену компьютера. Это позволяет входящим пользователям виртуального рабочего стола Windows для сопоставления из своей учетной записи Azure Active Directory для своей учетной записи Active Directory и успешно разрешен доступ к виртуальной машине.
- Необходимо установить роль узла сеансов удаленных рабочих столов (RDSH), если виртуальная машина работает под управлением ОС Windows Server. Роль RDSH позволяет агентам виртуальный рабочий стол Windows правильной установки.

Для успешного присоединения к домену выполните указанные ниже действия на каждой виртуальной машине.

1. [Подключение к виртуальной машине](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) с учетными данными, указанным при создании виртуальной машины.
2. На виртуальной машине, запустите **панели управления** и выберите **системы**.
3. Выберите **имя компьютера**выберите **изменить параметры**, а затем выберите **изменений...**
4. Выберите **домена** и затем введите имя домена Active Directory в виртуальной сети.
5. Проверка подлинности с учетной записью домена, имеющей права на присоединение к домену компьютеры.

    >[!NOTE]
    > Если вы присоединяете виртуальные машины к среде доменных служб Azure AD, убедитесь, что пользователь для присоединения к домену также является участником [группы администраторов контроллера домена AAD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Регистрация виртуальных машин в пул узлов Апробация виртуального рабочего стола Windows

Регистрация виртуальных машин в кластер узлов виртуального рабочего стола Windows является простым — установите агенты виртуального рабочего стола Windows.

Чтобы зарегистрировать agents виртуального рабочего стола Windows, выполните следующие действия на каждой виртуальной машине.

1. [Подключение к виртуальной машине](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) с учетными данными, указанным при создании виртуальной машины.
2. Скачайте и установите агент виртуального рабочего стола Windows.
   - Скачайте [агента виртуального рабочего стола Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Щелкните правой кнопкой мыши Скачанный установщик, выберите **свойства**выберите **Unblock**, а затем выберите **ОК**. Это позволит системе доверять установщик.
   - Запустите установщик. Когда программа установки запрашивает маркер регистрации, введите значение, полученное из **RdsRegistrationInfo экспорта** командлета.
3. Скачайте и установите агент загрузчик виртуального рабочего стола Windows.
   - Скачайте [загрузчика агента виртуального рабочего стола Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Щелкните правой кнопкой мыши Скачанный установщик, выберите **свойства**выберите **Unblock**, а затем выберите **ОК**. Это позволит системе доверять установщик.
   - Запустите установщик.

>[!IMPORTANT]
>Чтобы усилить защиту среды Виртуального рабочего стола Windows в Azure, мы рекомендуем не открывать входящий порт 3389 для виртуальных машин. Виртуальный рабочий стол Windows не требует открытия входящего порта 3389, чтобы пользователи могли получить доступ к виртуальным машинам пула узла. Если вам все же нужно открыть порт 3389 для устранения неполадок, мы рекомендуем использовать[JIT-доступ к виртуальным машинам](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы внесли пула узла, его можно заполнить RemoteApps. Дополнительные сведения о том, как управлять приложениями в Виртуальном рабочем столе Windows см. в руководстве по управлению группами приложений.

> [!div class="nextstepaction"]
> [Управление группами приложений](./manage-app-groups.md)
