---
title: Управление группами приложений для Виртуального рабочего стола Windows в Azure
description: В этой статье объясняется, как настраивать клиенты Виртуального рабочего стола в Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 55c2ed5f9066d85643588f77806bbd41e92dd16b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613950"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Руководство по Управление группами приложений для Виртуального рабочего стола Windows

>[!IMPORTANT]
>Это содержимое применимо к выпуску за осень 2019 года, который не поддерживает объекты Azure Resource Manager для Виртуального рабочего стола Windows. Если вы хотите обеспечить управление объектами Azure Resource Manager для Виртуального рабочего стола Windows, представленными в обновлении за весну 2020 г., см. [эту статью](../manage-app-groups.md).

Группа приложений по умолчанию, созданная для нового пула узлов Виртуального рабочего стола Windows, также позволяет опубликовать полные классические приложения. Кроме того, вы можете создать одну или несколько групп приложений RemoteApp для пула узлов. Выполните инструкции, приведенные в этом учебнике, чтобы создать группу приложений RemoteApp и опубликовать отдельные приложения в меню **Пуск**.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание группы приложений RemoteApp;
> * предоставление доступа к программам RemoteApp.

Перед началом работы при необходимости [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell. После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Создание группы приложений RemoteApp

1. Выполните этот командлет PowerShell, чтобы создать пустую группу приложений RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Необязательно) Чтобы проверить, была ли создана группа приложений, выполните указанный ниже командлет и просмотрите список всех групп приложений для пула узлов.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Выполните указанный ниже командлет, чтобы получить список приложений в меню **Пуск**, которые присутствуют в образе виртуальной машины из пула узлов. Запишите значения свойств **FilePath**, **IconPath**, **IconIndex** и другие важные данные для приложения, которое вы хотите опубликовать.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Выполните следующий командлет, чтобы установить приложение на основе свойства `AppAlias`. Свойство `AppAlias` отображается после обработки выходных данных, полученных на шаге 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Выполните следующий командлет, чтобы опубликовать новую программу RemoteApp в группе приложений, созданной на шаге 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Чтобы убедиться в том, что приложение было опубликовано, выполните указанный ниже командлет.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Повторите шаги 1–5 для каждого приложения, которое вы хотите опубликовать в этой группе приложений.
8. Выполните указанный ниже командлет, чтобы предоставить пользователям доступ к программам RemoteApp в этой группе приложений.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы узнали, как создать группу приложений, заполнить группу приложений с помощью программ RemoteApp и назначить для нее пользователей. Дополнительные сведения о том, как создать пул узлов проверки, см. в следующем учебнике. Вы может использовать пул узлов проверки, чтобы отслеживать обновления служб перед их развертыванием в рабочую среду.

> [!div class="nextstepaction"]
> [Руководство по созданию пула узлов для проверки обновлений службы](create-validation-host-pool-2019.md)
