---
title: Создание пула узлов виртуальных рабочих столов Windows PowerShell — Azure
description: Как создать пул узлов в виртуальном рабочем столе Windows с помощью командлетов PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 474eb4f5247aeb77edce0ebfde1611bf2deef493
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930409"
---
# <a name="create-a-host-pool-with-powershell"></a>Создание пула узлов с помощью PowerShell

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows. Если вы используете виртуальный рабочий стол Windows в выпуске 2019 без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента Виртуального рабочего стола Windows. Каждый пул узлов может быть связан с несколькими группами RemoteApp, одной группой приложений рабочей группы и несколькими узлами сеансов.

## <a name="prerequisites"></a>Предварительные условия

В этой статье предполагается, что вы уже использовали инструкции из раздела [Настройка модуля PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Создание пула узлов с помощью клиента PowerShell

Выполните следующий командлет, чтобы войти в среду Виртуального рабочего стола Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

Этот командлет создаст пул узлов, рабочую область и группу приложений рабочей среды. Кроме того, в рабочую область будет зарегистрирована группа приложений рабочей среды. Вы можете создать рабочую область с помощью этого командлета или использовать существующую рабочую область. 

Выполните следующий командлет, чтобы создать маркер регистрации для авторизации узла сеансов для приподключения к пулу узлов и сохранения его в новом файле на локальном компьютере. Можно указать срок действия маркера регистрации с помощью параметра-Експиратионхаурс.

>[!NOTE]
>Дата окончания срока действия маркера не может быть меньше часа и не больше одного месяца. Если вы установили параметр *-ExpirationTime* за пределами этого ограничения, командлет не создаст маркер.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Например, если вы хотите создать маркер, срок действия которого истекает через два часа, выполните следующий командлет: 

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ')) 
```

После этого выполните этот командлет, чтобы добавить Azure Active Directory пользователей в группу классических приложений по умолчанию для пула узлов.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

Выполните следующий командлет, чтобы добавить Azure Active Directory группы пользователей в группу классических приложений по умолчанию для пула узлов:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Выполните следующий командлет, чтобы экспортировать маркер регистрации в переменную, которая будет использоваться позже в [зарегистрируйте виртуальные машины в пуле узлов виртуальных рабочих столов Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Создание виртуальных машин для пула узлов

Теперь можно создать виртуальную машину Azure, которую можно присоединить к пулу узлов виртуальных рабочих столов Windows.

Создать виртуальную машину можно несколькими способами.

- [Создание виртуальной машины из образа из коллекции Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Создание виртуальной машины из управляемого образа](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Создание виртуальной машины из неуправляемого образа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Если вы развертываете виртуальную машину с Windows 7 в качестве ОС узла, процесс создания и развертывания будет немного иным. Дополнительные сведения см. в статье [развертывание виртуальной машины Windows 7 в виртуальном рабочем столе Windows](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

После создания виртуальных машин узла сеансов [Примените лицензию Windows к виртуальной машине узла сеансов](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) , чтобы запустить виртуальные машины Windows или Windows Server, не обращаясь к другой лицензии. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Подготовка виртуальных машин для установки агента виртуальных рабочих столов Windows

Чтобы подготовить виртуальные машины к установке агентов виртуальных рабочих столов Windows и зарегистрировать виртуальные машины в пуле узлов Windows, необходимо выполнить следующие действия.

- Необходимо присоединить компьютер к домену. Благодаря этому входящие пользователи виртуальных рабочих столов Windows будут сопоставлены с учетной записью Azure Active Directory с их учетной записью Active Directory и успешно разрешили доступ к виртуальной машине.
- Если виртуальная машина работает под управлением ОС Windows Server, необходимо установить роль узла удаленный рабочий стол сеансов (узлов сеансов удаленных рабочих столов). Роль «удаленные рабочие столы» позволяет агентам виртуальных рабочих столов Windows правильно устанавливаться.

Чтобы успешно присоединиться к домену, выполните следующие действия на каждой виртуальной машине.

1. [Подключитесь к виртуальной машине](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) с помощью учетных данных, указанных при создании виртуальной машины.
2. На виртуальной машине откройте **Панель управления** и выберите пункт **система**.
3. Выберите **имя компьютера**, щелкните **изменить параметры**, а затем выберите **изменить...**
4. Выберите **домен** , а затем введите домен Active Directory в виртуальной сети.
5. Проверка подлинности с учетной записью домена, имеющей права на присоединение к домену компьютеров.

    >[!NOTE]
    > Если вы присоединяете виртуальные машины к среде доменных служб Active Directory (Azure AD DS), убедитесь, что пользователь, который присоединяется к домену, также является участником [группы администраторов AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Регистрация виртуальных машин в пуле узлов виртуальных рабочих столов Windows

Регистрация виртуальных машин в пуле узлов виртуальных рабочих столов Windows выполняется так же просто, как установка агентов виртуальных рабочих столов Windows.

Чтобы зарегистрировать агенты виртуальных рабочих столов Windows, выполните следующие действия на каждой виртуальной машине:

1. [Подключитесь к виртуальной машине](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) с помощью учетных данных, указанных при создании виртуальной машины.
2. Скачайте и установите агент виртуальных рабочих столов Windows.
   - Скачайте [Агент виртуальных рабочих столов Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Запустите установщик. Когда установщик запросит у вас маркер регистрации, введите значение, полученное в командлете **Export-рдсрегистратионинфо** .
3. Скачайте и установите загрузчик агента виртуальных рабочих столов Windows.
   - Скачайте [загрузчик агента виртуальных рабочих столов Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Запустите установщик.

>[!IMPORTANT]
>Чтобы усилить защиту среды Виртуального рабочего стола Windows в Azure, мы рекомендуем не открывать входящий порт 3389 для виртуальных машин. Виртуальный рабочий стол Windows не требует открытия входящего порта 3389, чтобы пользователи могли получить доступ к виртуальным машинам пула узла. Если вам все же нужно открыть порт 3389 для устранения неполадок, мы рекомендуем использовать[JIT-доступ к виртуальным машинам](../security-center/security-center-just-in-time.md). Мы также рекомендуем не назначать виртуальные машины общедоступному IP-адресу.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда пул узлов создан, его можно заполнить удаленными приложениями. Дополнительные сведения о том, как управлять приложениями в Виртуальном рабочем столе Windows см. в руководстве по управлению группами приложений.

> [!div class="nextstepaction"]
> [Управление группами приложений](./manage-app-groups.md)
