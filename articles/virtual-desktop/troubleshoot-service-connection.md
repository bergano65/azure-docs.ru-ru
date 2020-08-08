---
title: Устранение неполадок с подключением к службе Виртуального рабочего стола Windows (Azure)
description: Сведения об устранении проблем при настройке клиентских подключений в среде клиента Виртуального рабочего стола Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6d0360a4fe957f43e38fd892cef6b4ab0a2325a
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009381"
---
# <a name="windows-virtual-desktop-service-connections"></a>Подключения к службе Виртуального рабочего стола Windows

>[!IMPORTANT]
>Это содержимое применимо к Виртуальному рабочему столу Windows с объектами Azure Resource Manager для Виртуального рабочего стола Windows. Если вы используете Виртуальный рабочий стол Windows (классический) без объектов Azure Resource Manager, ознакомьтесь с [этой статьей](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Эта статья поможет вам в устранении проблем при настройке клиентских подключений к Виртуальному рабочему столу Windows.

## <a name="provide-feedback"></a>Отзывы

Вы можете оставить отзыв и обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества, посетив [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Пользователь подключается, но изображение отсутствует (нет веб-канала)

Пользователь успешно запускает клиент удаленного рабочего стола и выполняет проверку подлинности, но не видит никаких значков в веб-канале обнаружения.

1. Убедитесь, что пользователю, у которого возникла проблема, назначены группы приложений, выполнив следующую командную строку:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Убедитесь, что этот пользователь входит в систему с правильными учетными данными.

3. Если используется веб-клиент, проверьте возможные проблемы с кэшированием учетных данных.

4. Если пользователь входит в группу пользователей Azure Active Directory (AD), убедитесь, что группа пользователей является группой безопасности, а не группой рассылки. Виртуальный рабочий стол Windows не поддерживает группы рассылки Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об устранении неполадок с Виртуальным рабочим столом Windows и о путях эскалации см. в статье [Общие сведения об устранении неисправностей, отзывы и поддержка](troubleshoot-set-up-overview.md).
- Инструкции по устранению неполадок при создании среды Виртуального рабочего стола Windows и пула узлов в среде Виртуального рабочего стола Windows см. в [этой статье](troubleshoot-set-up-issues.md).
- Инструкции по устранению неполадок при настройке виртуальной машины через Виртуальный рабочий стол Windows см. в статье [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration.md).
- Инструкции по устранению неполадок при использовании PowerShell через Виртуальный рабочий стол Windows см. в статье [Виртуальный рабочий стол Windows — PowerShell](troubleshoot-powershell.md).
- Сведения об устранении неполадок см. в статье [Tutorial: Troubleshoot Resource Manager template deployments](../azure-resource-manager/templates/template-tutorial-troubleshoot.md) (Руководство. Устранение неполадок развертывания шаблонов Resource Manager)
