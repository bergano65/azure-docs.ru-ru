---
title: Управление группами приложений для предварительной версии Виртуального рабочего стола Windows — Azure
description: В этой статье объясняется, как настраивать арендаторы предварительной версии Виртуального рабочего стола в Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 2bec7e490443727fa294e7be9412bb20ae66e691
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163254"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Руководство по управлению группами приложений для предварительной версии Виртуального рабочего стола Windows

Группа приложений по умолчанию, созданная для нового пула узлов предварительной версии Виртуального рабочего стола Windows, также позволяет опубликовать полные классические приложения. Кроме того, вы можете создать одну или несколько групп приложений RemoteApp для пула узлов. Выполните инструкции, приведенные в этом учебнике, чтобы создать группу приложений RemoteApp и опубликовать отдельные приложения в меню **Пуск**.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание группы приложений RemoteApp;
> * предоставление доступа к программам RemoteApp.

Перед началом работы при необходимости [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell. После этого выполните следующий командлет, чтобы войти в учетную запись:

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

## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы узнали, как создать группу приложений, заполнить группу приложений с помощью программ RemoteApp и назначить для нее пользователей. Дополнительные сведения о том, как создать пул узлов проверки, см. в следующем учебнике. Вы может использовать пул узлов проверки, чтобы отслеживать обновления служб перед их развертыванием в рабочую среду.

> [!div class="nextstepaction"]
> [Руководство по созданию пула узлов для проверки обновлений службы](./create-validation-host-pool.md)
