---
title: Устранение неполадок виртуального рабочего стола Windows удаленный рабочий стол Client — Azure
description: Устранение проблем при настройке клиентских подключений в среде клиента виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: bcc1b3a42b1724e0b1b5768d5abb3575a6df26d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486668"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Устранение неполадок клиента удаленный рабочий стол

В этой статье описаны распространенные проблемы с клиентом удаленный рабочий стол и способы их устранения.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>удаленный рабочий стол клиент для Windows 7 или Windows 10 перестает отвечать на запросы или не может быть открыт

Используйте следующие командлеты PowerShell для очистки реестров клиента по внешнему каналу (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Перейдите по адресу **%аппдата%\рдклиентрадк** и удалите все содержимое.

Удалите и переустановите удаленный рабочий стол клиент для Windows 7 и Windows 10.

## <a name="web-client-wont-open"></a>Веб-клиент не открывается

Сначала проверьте подключение к Интернету, открыв другой веб-сайт в браузере. Например, [www.Bing.com](https://www.bing.com).

Используйте **nslookup** , чтобы подтвердить, что DNS может разрешить полное доменное имя:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Попробуйте подключиться с помощью другого клиента, например удаленный рабочий стол клиента для Windows 7 или Windows 10, и проверьте, можно ли открыть веб-клиент.

### <a name="opening-another-site-fails"></a>Не удается открыть другой сайт

Обычно это происходит из-за проблем с сетевым подключением или сбоя сети. Рекомендуется обратиться в службу поддержки сети.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup не удается разрешить имя

Обычно это происходит из-за проблем с сетевым подключением или сбоя сети. Рекомендуется обратиться в службу поддержки сети.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Клиент не может подключиться, но можно подключиться к другим клиентам в сети

Если браузер начинает работу или перестает работать при использовании веб-клиента, следуйте приведенным ниже инструкциям, чтобы устранить неполадки.

1. Перезапустите браузер.
2. Очистите файлы cookie браузера. См. раздел [Удаление файлов cookie в Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Очистите кэш браузера. См. раздел [Очистка кэша браузера для браузера](https://binged.it/2RKyfdU).
4. Откройте браузер в частном режиме.

## <a name="web-client-stops-responding-or-disconnects"></a>Веб-клиент перестает отвечать или отключается

Попробуйте подключиться с помощью другого браузера или клиента.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Другие браузеры и клиенты также неисправен или не могут открыться

Если проблемы продолжают возникать даже после переключения браузеров, проблема может быть не связана с браузером, но с вашей сетью. Рекомендуется обратиться в службу поддержки сети.

## <a name="web-client-keeps-prompting-for-credentials"></a>Веб-клиент сохраняет запросы учетных данных

Если веб-клиент сохраняет запрос на ввод учетных данных, выполните следующие инструкции:

1. Убедитесь, что URL-адрес клиента указан правильно.
2. Убедитесь, что используемые учетные данные предназначены для среды виртуальных рабочих столов Windows, привязанной к URL-адресу.
3. Очистите файлы cookie браузера. Дополнительные сведения см. в разделе [Удаление файлов cookie в Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Очистите кэш браузера. Дополнительные сведения см. [в разделе Очистка кэша браузера для браузера](https://binged.it/2RKyfdU).
5. Откройте браузер в частном режиме.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок с виртуальным рабочим столом Windows и сведениями о эскалации см. в разделе [Обзор устранения неполадок, обратная связь и поддержка](troubleshoot-set-up-overview.md).
- Сведения об устранении неполадок при создании клиента и пула узлов в среде виртуальных рабочих столов Windows см. в статье [Создание пула клиентов и узлов](troubleshoot-set-up-issues.md).
- Сведения об устранении неполадок при настройке виртуальной машины в виртуальном рабочем столе Windows см. в разделе [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration.md).
- Сведения об устранении неполадок при использовании PowerShell с виртуальным рабочим столом Windows см. в статье [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Руководство по устранению неполадок см. в разделе [учебник. Устранение неполадок диспетчер ресурсов развертываний шаблонов](../azure-resource-manager/resource-manager-tutorial-troubleshoot.md).