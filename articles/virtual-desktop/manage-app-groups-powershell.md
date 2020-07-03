---
title: Управление группами приложений для виртуальных рабочих столов Windows PowerShell — Azure
description: Управление группами приложений виртуальных рабочих столов Windows с помощью PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 531c7a819bf83edff2756fe1e62859bcb8fef459
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615270"
---
# <a name="manage-app-groups-using-powershell"></a>Управление группами приложений с помощью PowerShell

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows. Если вы используете виртуальный рабочий стол Windows в выпуске 2019 без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/manage-app-groups-2019.md).
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Группа приложений по умолчанию, созданная для нового пула узлов Виртуального рабочего стола Windows, также позволяет опубликовать полные классические приложения. Кроме того, вы можете создать одну или несколько групп приложений RemoteApp для пула узлов. Следуйте указаниям этого руководства, чтобы создать группу приложений RemoteApp и опубликовать отдельные приложения меню " **Пуск** ".

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание группы приложений RemoteApp;
> * предоставление доступа к программам RemoteApp.

## <a name="prerequisites"></a>Предварительные условия

В этой статье предполагается, что вы использовали инструкции в разделе [Настройка модуля PowerShell](powershell-module.md) для настройки модуля PowerShell и входа в учетную запись Azure.

## <a name="create-a-remoteapp-group"></a>Создание группы приложений RemoteApp

Чтобы создать группу RemoteApp с помощью PowerShell, выполните следующие действия.

1. Выполните этот командлет PowerShell, чтобы создать пустую группу приложений RemoteApp.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (Необязательно) Чтобы проверить, была ли создана группа приложений, выполните указанный ниже командлет и просмотрите список всех групп приложений для пула узлов.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Выполните следующий командлет, чтобы получить список приложений меню " **Пуск** " в образе виртуальной машины пула узлов. Запишите значения свойств **FilePath**, **IconPath**, **IconIndex** и другие важные данные для приложения, которое вы хотите опубликовать.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more 
   ```

   Выходные данные должны отображать все пункты меню "Пуск" в следующем формате:

   ```powershell
   AppAlias            : access
   CommandLineArgument : 
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE 
   FriendlyName        : 
   IconIndex           : 0 
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe 
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access 
   Name                : 0301RAG/Access 
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   
   AppAlias            : charactermap 
   CommandLineArgument : 
   FilePath            : C:\windows\system32\charmap.exe 
   FriendlyName        : 
   IconIndex           : 0 
   IconPath            : C:\windows\system32\charmap.exe 
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map 
   Name                : 0301RAG/Character Map 
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems 
   ```
   
4. Выполните следующий командлет, чтобы установить приложение на основе свойства `AppAlias`. Свойство `AppAlias` отображается после обработки выходных данных, полученных на шаге 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require> 
   ```

5. Выполните следующий командлет, чтобы опубликовать новую программу RemoteApp в группе приложений, созданной на шаге 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require> 
   ```

6. Чтобы убедиться в том, что приложение было опубликовано, выполните указанный ниже командлет.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Повторите шаги 1–5 для каждого приложения, которое вы хотите опубликовать в этой группе приложений.
8. Выполните указанный ниже командлет, чтобы предоставить пользователям доступ к программам RemoteApp в этой группе приложений.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Дальнейшие действия

Если вы приступили к этому руководству в наших руководствах, ознакомьтесь со [статьей Создание пула узлов для проверки обновлений службы](create-validation-host-pool.md). Вы может использовать пул узлов проверки, чтобы отслеживать обновления служб перед их развертыванием в рабочую среду.