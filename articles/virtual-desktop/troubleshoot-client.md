---
title: Устранение проблем удаленного рабочего стола Windows Виртуальный рабочий стол - Azure
description: Как решить проблемы при настройке клиентских подключений в среде для арендаторов Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127505"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Устранение проблем с клиентом удаленного рабочего стола

В этой статье описаны общие проблемы с клиентом Remote Desktop и способы их устранения.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Удаленный настольный клиент для Windows 7 или Windows 10 перестает отвечать или не может быть открыт

Используйте следующие смдлеты PowerShell для очистки внеполосных (OOB) клиентских реестров.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Перейдите на **:AppData%-RdClientRadc** и удалите весь контент.

Удалить и переустановить клиент Remote Desktop для Windows 7 и Windows 10.

## <a name="web-client-wont-open"></a>Веб-клиент не откроется

Во-первых, проверить подключение к Интернету, открыв еще один веб-сайт в вашем браузере; например, [www.bing.com](https://www.bing.com).

Используйте **nslookup** для подтверждения DNS может решить ФЗДН:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Попробуйте подключиться к другому клиенту, например к клиенту Remote Desktop для Windows 7 или Windows 10, и проверьте, можете ли вы открыть веб-клиента.

### <a name="opening-another-site-fails"></a>Открытие другого сайта не удается

Обычно это вызвано проблемами сетевого подключения или сбой в сети. Рекомендуем обратиться в службу поддержки сети.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup не может решить имя

Обычно это вызвано проблемами сетевого подключения или сбой в сети. Рекомендуем обратиться в службу поддержки сети.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Ваш клиент не может подключиться, но другие клиенты в вашей сети могут подключиться

Если ваш браузер начинает действовать или перестает работать во время использования веб-клиента, следуйте этим инструкциям, чтобы устранить его неполадки:

1. Перезапустите браузер.
2. Очистить файлы cookie браузера. [Узнайте, как удалить файлы cookie в Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Очистите кэш браузера. Смотрите [четкий кэш браузера для вашего браузера.](https://binged.it/2RKyfdU)
4. Открыть браузер в режиме Private.

## <a name="web-client-stops-responding-or-disconnects"></a>Веб-клиент перестает реагировать или отключается

Попробуйте подключиться с помощью другого браузера или клиента.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Другие браузеры и клиенты также не смогут открыться или не открыть

Если проблемы продолжаются даже после того, как вы переключили браузеры, проблема может быть не с вашим браузером, а с вашей сетью. Рекомендуем обратиться в службу поддержки сети.

## <a name="web-client-keeps-prompting-for-credentials"></a>Веб-клиент сохраняет запрос на учетные данные

Если веб-клиент продолжает побуждать учетные данные, следуйте следующим инструкциям:

1. Подтвердите, что URL-адрес веб-клиента является правильным.
2. Подтвердите, что учетные данные, которые вы используете, для среды Windows Virtual Desktop привязаны к URL-.
3. Очистить файлы cookie браузера. Для получения более подробной информации см. [Как удалить файлы cookie в Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Очистите кэш браузера. Для получения более подробной информации [см.](https://binged.it/2RKyfdU)
5. Откройте браузер в режиме Private.

## <a name="next-steps"></a>Дальнейшие действия

- Для обзора устранения неполадок Windows Virtual Desktop и треков эскалации [см.](troubleshoot-set-up-overview.md)
- Для устранения неполадок при создании пула арендатора и хоста в среде Windows Virtual Desktop [см.](troubleshoot-set-up-issues.md)
- Для устранения неполадок при настройке виртуальной машины (VM) в Windows Virtual Desktop [см.](troubleshoot-vm-configuration.md)
- Для устранения неполадок при использовании [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)PowerShell с Windows Virtual Desktop см.
- Чтобы пройти через учебник по устранению неполадок, [см.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)