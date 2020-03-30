---
title: Публикация встроенных приложений в виртуальном рабочем столе Windows - Azure
description: Как публиковать встроенные приложения в Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127737"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Публикация встроенных приложений в Windows Virtual Desktop

Эта статья расскажет вам, как публиковать приложения в среде Windows Virtual Desktop.

## <a name="publish-built-in-apps"></a>Публикация встроенных приложений

Чтобы опубликовать встроенное приложение:

1. Подключитесь к одной из виртуальных машин в пуле хоста.
2. Получите **PackageFamilyName** приложения, которое вы хотите опубликовать, следуя инструкциям в [этой статье.](/powershell/module/appx/get-appxpackage?view=win10-ps/)
3. Наконец, запустите следующий `<PackageFamilyName>` cmdlet с заменой **PackageFamilyName** вы нашли в предыдущем шаге:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows Virtual Desktop поддерживает только публикацию `C:\Program Files\Windows Apps`приложений с установкой мест, которые начинаются с .

## <a name="update-app-icons"></a>Обновление значков приложений

После публикации приложения у него появится значок приложения Windows по умолчанию вместо обычного изображения значка. Чтобы изменить значок на свой обычный значок, поместите изображение иконки, которая вы хотите, в сети. Поддерживаемые форматы изображений: PNG, BMP, GIF, JPG, JPEG и ICO.

## <a name="publish-microsoft-edge"></a>Публикация Microsoft Edge

Процесс публикации Microsoft Edge немного отличается от процесса публикации для других приложений. Чтобы опубликовать Microsoft Edge с домашней страницей по умолчанию, запустите этот cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте о том, как настроить каналы для организации отображения приложений для пользователей в [настраиваемом канале для пользователей Windows Virtual Desktop.](customize-feed-for-virtual-desktop-users.md)
- Узнайте о функции присоединения приложения MSIX при [настройке приложения MSIX.](app-attach.md)

