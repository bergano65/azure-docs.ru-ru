---
title: Устранение неполадок подключения к службе виртуальная система Windows — 2019 — Azure
description: Сведения об устранении проблем при настройке клиентских подключений в среде клиента Виртуального рабочего стола Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad5e740a2874d398f3947fe755024bedc897ec73
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085798"
---
# <a name="troubleshoot-windows-virtual-desktop-service-connections"></a>Устранение неполадок подключения к службе виртуальных рабочих столов Windows

>[!IMPORTANT]
>Это содержимое применимо к выпуску за осень 2019 года, который не поддерживает объекты Azure Resource Manager для Виртуального рабочего стола Windows. Если вы хотите обеспечить управление объектами Azure Resource Manager для Виртуального рабочего стола Windows, представленными в обновлении за весну 2020 г., см. [эту статью](../troubleshoot-service-connection.md).

Эта статья поможет вам в устранении проблем при настройке клиентских подключений к Виртуальному рабочему столу Windows.

## <a name="provide-feedback"></a>Отзывы

Вы можете оставить отзыв и обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества, посетив [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Пользователь подключается, но изображение отсутствует (нет веб-канала)

Пользователь успешно запускает клиент удаленного рабочего стола и выполняет проверку подлинности, но не видит никаких значков в веб-канале обнаружения.

Убедитесь, что пользователю с этой проблемой назначены группы приложений, выполнив следующую командную строку:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Убедитесь, что этот пользователь входит в систему с правильными учетными данными.

Если используется веб-клиент, проверьте возможные проблемы с кэшированием учетных данных.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок с Виртуальным рабочим столом Windows и о путях эскалации см. в статье [Общие сведения об устранении неисправностей, отзывы и поддержка](troubleshoot-set-up-overview-2019.md).
- Инструкции по устранению неполадок при создании клиента и пула узлов в среде Виртуального рабочего стола Windows см. в статье [Создание клиента и пула узлов](troubleshoot-set-up-issues-2019.md).
- Инструкции по устранению неполадок при настройке виртуальной машины через Виртуальный рабочий стол Windows см. в статье [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration-2019.md).
- Инструкции по устранению неполадок при использовании PowerShell через Виртуальный рабочий стол Windows см. в статье [Виртуальный рабочий стол Windows — PowerShell](troubleshoot-powershell-2019.md).
- Сведения об устранении неполадок см. в статье [Tutorial: Troubleshoot Resource Manager template deployments](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md) (Руководство. Устранение неполадок развертывания шаблонов Resource Manager)
