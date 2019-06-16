---
title: Настроить свойства протокола удаленного рабочего СТОЛА с помощью PowerShell, Azure
description: Способы настройки протокола удаленного рабочего СТОЛА свойства для Windows виртуальному рабочему столу с помощью командлетов PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: 21d0b45b974f4bc806b26423b7deaef96e4bf350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082655"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Настроить свойства протокола удаленного рабочего стола для кластера узлов

Настройка свойств протокола удаленного рабочего стола (RDP) пула узла, например несколькими мониторами более удобной и перенаправление звука, предоставляющее нужные оптимальной работы для пользователей под свои нужды. Можно настроить свойства протокола удаленного рабочего СТОЛА в виртуальный рабочий стол Windows с помощью **- CustomRdpProperty** параметр в **RdsHostPool набора** командлета.

См. в разделе [параметры файла удаленного рабочего СТОЛА](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) полный список поддерживаемых свойств и их значения по умолчанию.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще это не сделали).

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Добавление или изменение одного пользовательского свойства протокола удаленного рабочего СТОЛА

Чтобы добавить или изменить одного пользовательского свойства протокола удаленного рабочего СТОЛА, выполните следующий командлет PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Снимок экрана PowerShell командлет Get-RDSRemoteApp с именем и FriendlyName выделены.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Добавить или изменить несколько настраиваемых свойств протокола удаленного рабочего СТОЛА

Чтобы добавить или изменить несколько настраиваемых свойств протокола удаленного рабочего СТОЛА, выполните следующие командлеты PowerShell, предоставляя пользовательские свойства протокола удаленного рабочего СТОЛА, как строка разделенных точкой с запятой:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Снимок экрана PowerShell командлет Get-RDSRemoteApp с именем и FriendlyName выделены.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Сброс всех настраиваемых свойств протокола удаленного рабочего СТОЛА

Вы можете сбросить отдельные пользовательские свойства протокола удаленного рабочего СТОЛА к значениям по умолчанию, следуя инструкциям в [Добавление или изменение одного пользовательского свойства протокола удаленного рабочего СТОЛА](#add-or-edit-a-single-custom-rdp-property), или вы можете сбросить все пользовательские свойства протокола удаленного рабочего СТОЛА для кластера узлов, выполнив следующую Командлет PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Снимок экрана PowerShell командлет Get-RDSRemoteApp с именем и FriendlyName выделены.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили свойства протокола удаленного рабочего СТОЛА для данного узла пула, вы войти в клиент виртуального рабочего стола Windows для проверки их в рамках сеанса пользователя. Чтобы сделать это, перейдите к подключения виртуальный рабочий стол инструкции Windows:

- [Подключение из Windows 10 и Windows 7](connect-windows-7-and-10.md)
- [Подключение из веб-браузера](connect-web.md)
