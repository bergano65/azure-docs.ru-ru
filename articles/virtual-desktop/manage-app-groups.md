---
title: Управление группами приложений для предварительной версии Виртуального рабочего стола Windows — Azure
description: В этой статье объясняется, как настраивать арендаторы предварительной версии Виртуального рабочего стола в Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f0cdd28be8c6e7390aa26fdc2dfbf32ec5542c2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233907"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Руководство по управлению группами приложений для предварительной версии Виртуального рабочего стола Windows

Группа приложений по умолчанию, созданная для нового пула узлов предварительной версии Виртуального рабочего стола Windows, также позволяет опубликовать полные классические приложения. Кроме того, вы можете создать одну или несколько групп приложений RemoteApp для пула узлов. Выполните инструкции, приведенные в этом руководстве, чтобы создать группу приложений RemoteApp и опубликовать отдельные приложения в меню "Пуск".

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание группы приложений RemoteApp;
> * предоставление доступа к удаленным приложениям RemoteApp.

Перед началом работы [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще этого не сделали).

## <a name="create-a-remoteapp-group"></a>Создание группы приложений RemoteApp

1. Выполните этот командлет PowerShell, чтобы создать пустую группу приложений RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Необязательно) Чтобы проверить, была ли создана группа приложений, выполните указанный ниже командлет и просмотрите список всех групп приложений для пула узлов.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Выполните указанный ниже командлет, чтобы получить список приложений в меню "Пуск", которые присутствуют в образе виртуальной машины из пула узлов. Запишите значения свойств **FilePath**, **IconPath**, **IconIndex** и другие важные данные для приложения, которое вы хотите опубликовать.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Выполните следующий командлет, чтобы установить приложение на основе свойства appalias. Свойство appalias отображается после обработки выходных данных, полученных на шаге 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Выполните следующий командлет, чтобы опубликовать новое удаленное приложение RemoteApp в группе приложений, созданной на шаге 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Чтобы убедиться в том, что приложение было опубликовано, выполните указанный ниже командлет.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Повторите шаги 1–5 для каждого приложения, которое вы хотите опубликовать в этой группе приложений.
8. Выполните указанный ниже командлет, чтобы предоставить пользователям доступ к удаленным приложениям RemoteApp в этой группе приложений.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы узнали, как создать группы приложений, заполнить группу приложений с помощью RemoteApps и назначить для нее пользователей. Чтобы узнать больше о том, как можно выполнять вход в Виртуальный рабочий стол Windows, перейдите к руководствам по подключению к Виртуальному рабочему столу Windows.

- [Connect to the Remote Desktop client on Windows 7 and Windows 10](connect-windows-7-and-10.md) (Подключение к клиенту удаленного рабочего стола в Windows 7 и Windows 10)
- [Connect to the Windows Virtual Desktop Preview web client](connect-web.md) (Подключение к веб-клиенту предварительной версии Виртуального рабочего стола Windows)
