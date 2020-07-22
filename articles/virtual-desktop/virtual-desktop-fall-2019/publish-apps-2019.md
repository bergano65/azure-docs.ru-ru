---
title: Публикация встроенных приложений в виртуальных рабочих столах Windows в Azure
description: Как опубликовать встроенные приложения в виртуальном рабочем столе Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7a453ef44a1ede86290f4130a6147eaaad09fa97
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214151"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Публикация встроенных приложений в виртуальном рабочем столе Windows

>[!IMPORTANT]
>Это содержимое применимо к выпуску за осень 2019 года, который не поддерживает объекты Azure Resource Manager для Виртуального рабочего стола Windows. Если вы хотите обеспечить управление объектами Azure Resource Manager для Виртуального рабочего стола Windows, представленными в обновлении за весну 2020 г., см. [эту статью](../publish-apps.md).

В этой статье рассказывается, как опубликовать приложения в среде виртуальных рабочих столов Windows.

## <a name="publish-built-in-apps"></a>Публикация встроенных приложений

Чтобы опубликовать встроенное приложение, выполните следующие действия.

1. Подключитесь к одной из виртуальных машин в пуле узлов.
2. Получите **паккажефамилинаме** приложения, которое вы хотите опубликовать, выполнив инструкции в [этой статье](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Наконец, выполните следующий командлет с `<PackageFamilyName>` замещенным **паккажефамилинаме** , найденным на предыдущем шаге:

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Виртуальный рабочий стол Windows поддерживает публикацию приложений только с расположениями установки, начинающимися с `C:\Program Files\Windows Apps` .

## <a name="update-app-icons"></a>Обновить значки приложений

После публикации приложение будет иметь значок приложения Windows по умолчанию вместо обычного изображения значка. Чтобы изменить значок на обычный значок, следует поместить изображение значка в общую сетевую папку. Поддерживаются форматы изображений PNG, BMP, GIF, JPG, JPEG и ICO.

## <a name="publish-microsoft-edge"></a>Публикация Microsoft ребра

Процесс, используемый для публикации Microsoft ребра, немного отличается от процесса публикации для других приложений. Чтобы опубликовать Microsoft ребро с домашней страницей по умолчанию, выполните следующий командлет:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>Дальнейшие шаги

- Узнайте, как настроить веб-каналы, чтобы организовать отображение приложений для пользователей в [настраиваемом веб-канале для пользователей виртуальных рабочих столов Windows](customize-feed-virtual-desktop-users-2019.md).
- Дополнительные сведения о функции присоединения приложения MSIX см. в статье Настройка присоединения приложения [MSIX](../app-attach.md).

