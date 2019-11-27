---
title: Публикация встроенных приложений в виртуальных рабочих столах Windows в Azure
description: Как опубликовать современные приложения в виртуальном рабочем столе Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
ms.openlocfilehash: 26ebcc7f2196267719a5f49aeeb8de00d48b9c3e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483742"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Публикация встроенных приложений в виртуальном рабочем столе Windows

В этой статье рассказывается, как опубликовать приложения в среде виртуальных рабочих столов Windows.

## <a name="publish-built-in-apps"></a>Публикация встроенных приложений

Чтобы опубликовать встроенное приложение, выполните следующие действия.

1. Подключитесь к одной из виртуальных машин в пуле узлов.
2. Получите **паккажефамилинаме** приложения, которое вы хотите опубликовать, выполнив инструкции в [этой статье](https://docs.microsoft.com/powershell/module/appx/get-appxpackage?view=win10-ps).
3. Наконец, выполните следующий командлет с `<PackageFamilyName>` замените на **паккажефамилинаме** , найденный на предыдущем шаге:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Виртуальный рабочий стол Windows поддерживает публикацию приложений только с расположениями установки, которые начинаются с `C:\Program Files\Windows Apps`.

## <a name="update-app-icons"></a>Обновить значки приложений

После публикации приложение будет иметь значок приложения Windows по умолчанию вместо обычного изображения значка. Чтобы изменить значок на обычный значок, следует поместить изображение значка в общую сетевую папку. Поддерживаются форматы изображений PNG, BMP, GIF, JPG, JPEG и ICO.

## <a name="publish-microsoft-edge"></a>Публикация Microsoft ребра

Процесс, используемый для публикации Microsoft ребра, немного отличается от процесса публикации для других приложений. Чтобы опубликовать Microsoft ребро с домашней страницей по умолчанию, выполните следующий командлет:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```



