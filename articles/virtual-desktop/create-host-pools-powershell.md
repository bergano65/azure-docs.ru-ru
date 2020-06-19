---
title: Создание пула узлов Виртуального рабочего стола Windows с помощью PowerShell (Azure)
description: Сведения о том, как создать пул узлов в Виртуальном рабочем столе Windows с использованием командлетов PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0a4d0c22318399370b9ec11046c33a4eb5460eb3
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860126"
---
# <a name="create-a-host-pool-with-powershell"></a>Создание пула узлов с помощью PowerShell

>[!IMPORTANT]
>Это содержимое применимо к обновлению за весну 2020 года с объектами Azure Resource Manager для Виртуального рабочего стола Windows. Если вы используете выпуск Виртуального рабочего стола Windows за осень 2019 года без объектов Azure Resource Manager, см. [эту статью](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).
>
> Обновление Виртуального рабочего стола Windows за весну 2020 года пока предоставляется как общедоступная предварительная версия. без соглашений об уровне обслуживания. Мы не рекомендуем использовать ее для выполнения производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента Виртуального рабочего стола Windows. Каждый пул узлов может быть связан с несколькими группами RemoteApp, одной группой классических приложений и несколькими узлами сеансов.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что вы уже выполнили инструкции по [настройке модуля PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Создание пула узлов с помощью клиента PowerShell

Выполните следующий командлет, чтобы войти в среду Виртуального рабочего стола Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

Этот командлет создает пул узлов, рабочую область и группу классических приложений. Также он регистрирует группу классических приложений в рабочей области. Вы можете создать новую рабочую область с помощью того же командлета или использовать имеющуюся. 

Выполните следующий командлет, чтобы создать маркер регистрации, который разрешит узлу сеансов подключиться к пулу узлов, и сохранить его в новый файл на локальном компьютере. Срок действия маркера регистрации можно указать с помощью параметра -ExpirationHours.

>[!NOTE]
>Срок действия маркера не может быть меньше часа или больше одного месяца. Если вы укажете значение *-ExpirationTime* за пределами этих лимитов, командлет не создаст маркер.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Например, если вы хотите создать маркер со сроком действия два часа, выполните следующий командлет: 

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ')) 
```

После этого выполните следующий командлет, чтобы добавить пользователей Azure Active Directory в группу классических приложений по умолчанию для пула узлов.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

Выполните следующий командлет, чтобы добавить группы пользователей Azure Active Directory в группу классических приложений по умолчанию для пула узлов:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Выполните следующий командлет, чтобы экспортировать маркер регистрации в переменную, которая потребуется нам позже при [регистрации виртуальных машин в пуле узлов Виртуального рабочего стола Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Создание виртуальных машин для пула узлов

Теперь можно создать виртуальную машину Azure и присоединить ее к пулу узлов Виртуального рабочего стола Windows.

Создать виртуальную машину можно несколькими способами:

- [Создание виртуальной машины из образа коллекции Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Создание виртуальной машины из управляемого образа](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Создание виртуальной машины из неуправляемого образа](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Если вы развертываете виртуальную машину с Windows 7 в качестве ОС узла, процесс создания и развертывания будет немного отличаться. Подробные сведения см. в статье [Развертывание виртуальной машины Windows 7 в службе "Виртуальный рабочий стол Windows"](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

После создания виртуальных машин для узла сеансов [примените лицензию Windows к виртуальной машине узла сеансов](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm), чтобы не оплачивать дополнительную лицензию для работы виртуальных машин Windows или Windows Server. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Подготовка виртуальных машин к установке агентов Виртуального рабочего стола Windows

Чтобы подготовить виртуальные машины к установке агентов Виртуального рабочего стола Windows и зарегистрировать эти виртуальные машины в пуле узлов Виртуального рабочего стола Windows, выполните следующие действия:

- Присоедините компьютер к домену. Это позволит сопоставлять для пользователей, входящих в Виртуальный рабочий стол Windows, учетные записи Azure Active Directory с учетными записями Active Directory и успешно разрешать доступ к виртуальной машине.
- Если виртуальная машина работает под управлением ОС Windows Server, необходимо установить роль узла сеансов удаленного рабочего стола. Эта роль позволяет правильно установить агенты Виртуального рабочего стола Windows.

Чтобы успешно присоединиться к домену, выполните следующие действия на каждой виртуальной машине:

1. [Войдите на виртуальную машину](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine), указав учетные данные, которые вы использовали при создании этой виртуальной машины.
2. На виртуальной машине откройте **Панель управления** и выберите **Система**.
3. Выберите **Имя компьютера**, щелкните **Изменить параметры** и выберите **Изменить...**
4. Выберите элемент **Домен** и введите имя домена Active Directory в этой виртуальной сети.
5. Выполните аутентификацию с учетной записью домена, которая имеет права на присоединение компьютеров к домену.

    >[!NOTE]
    > Если вы присоединяете виртуальные машины к среде доменных служб Active Directory (Azure AD DS), убедитесь, что пользователь, который присоединяется к домену, также является участником [группы администраторов AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Регистрация виртуальных машин в пуле узлов Виртуального рабочего стола Windows

Этот процесс ничем не сложнее установки агентов Виртуального рабочего стола Windows.

Чтобы зарегистрировать агенты Виртуального рабочего стола Windows, выполните следующие действия на каждой виртуальной машине:

1. [Войдите на виртуальную машину](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine), указав учетные данные, которые вы использовали при создании этой виртуальной машины.
2. Скачайте и установите агент Виртуального рабочего стола Windows.
   - Скачайте [агент Виртуального рабочего стола Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Запустите установщик. Когда установщик запросит маркер регистрации, введите значение, полученное с помощью командлета **Export-AzWVDRegistrationInfo**.
3. Скачайте и установите загрузчик агента Виртуального рабочего стола Windows.
   - Скачайте [загрузчик агента Виртуального рабочего стола Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Запустите установщик.

>[!IMPORTANT]
>Чтобы усилить защиту среды Виртуального рабочего стола Windows в Azure, мы рекомендуем не открывать входящий порт 3389 для виртуальных машин. Виртуальный рабочий стол Windows не требует открытия входящего порта 3389, чтобы пользователи могли получить доступ к виртуальным машинам пула узла. Если вам все же нужно открыть порт 3389 для устранения неполадок, мы рекомендуем использовать[JIT-доступ к виртуальным машинам](../security-center/security-center-just-in-time.md). Мы также рекомендуем не назначать виртуальным машинам общедоступные IP-адреса.

## <a name="next-steps"></a>Дальнейшие действия

Теперь пул узлов готов, и вы можете заполнить его удаленными приложениями RemoteApp. Дополнительные сведения о том, как управлять приложениями в Виртуальном рабочем столе Windows см. в руководстве по управлению группами приложений.

> [!div class="nextstepaction"]
> [Управление группами приложений](./manage-app-groups.md)
