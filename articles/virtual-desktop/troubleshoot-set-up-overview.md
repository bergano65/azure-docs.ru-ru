---
title: Обзор устранения неполадок в виртуальном рабочем столе Windows - Azure
description: Обзор проблем устранения неполадок при настройке среды для арендаторов Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127400"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Общие сведения об устранении неисправностей, отзывы и поддержка

В этой статье содержится обзор проблем, с которыми вы можете столкнуться при настройке среды для арендаторов Windows Virtual Desktop, и предоставляются способы решения проблем.

## <a name="provide-feedback"></a>Отзывы

Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="escalation-tracks"></a>Эскалация треков

Используйте следующую таблицу для выявления и устранения проблем, с которыми вы можете столкнуться при настройке среды клиента с помощью удаленного клиента Desktop. После настройки арендатора вы можете использовать нашу новую [службу диагностики](diagnostics-role-service.md) для выявления проблем для общих сценариев.

>[!NOTE]
> У нас есть форум Tech Community, который вы можете посетить, чтобы обсудить свои проблемы с командой продукта и активными членами сообщества. Посетите [Windows Virtual Desktop Tech Community,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) чтобы начать обсуждение.

| **Проблема**                                                            | **Предлагаемое решение**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Создание наемного устройства виртуального рабочего стола Windows                                                    | Если происходит отключение Azure, [откройте запрос поддержки Azure;](https://azure.microsoft.com/support/create-ticket/) в противном случае [откройте запрос поддержки Azure,](https://azure.microsoft.com/support/create-ticket/)выберите **Windows Virtual Desktop** для службы, выберите **Развертывание** для типа проблемы, а затем выберите **Проблемы, создавая наниматель Windows Virtual Desktop** для проблемного подтипа.|
| Доступ к шаблонам Marketplace на портале Azure       | Если происходит отключение Azure, [откройте запрос поддержки Azure.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Шаблоны виртуального рабочего стола Azure Marketplace Windows находятся в свободном доступе.|
| Доступ к шаблонам менеджера ресурсов Azure от GitHub                                  | Смотрите [раздел Создания Windows Virtual Desktop- сессии VMs-раздела](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) [Tenant и host pool creation.](troubleshoot-set-up-issues.md) Если проблема еще не решена, обратитесь в [службу поддержки GitHub.](https://github.com/contact) <br> <br> Если ошибка произошла после доступа к шаблону в GitHub, обратитесь в [службу поддержки Azure.](https://azure.microsoft.com/support/create-ticket/)|
| Настройки пула сеансов Azure Virtual Network (VNET) и настройки экспресс-маршрута               | [Откройте запрос на поддержку Azure,](https://azure.microsoft.com/support/create-ticket/)затем выберите соответствующую службу (в категории Сеть). |
| Создание пула виртуальной машины (VM) сеанса, когда шаблоны Azure Resource Manager, снабженные Windows Virtual Desktop, не используются | [Откройте запрос на поддержку Azure,](https://azure.microsoft.com/support/create-ticket/)а затем выберите **виртуальную машину** под управлением Windows для службы. <br> <br> Для проблем с шаблонами Azure Resource Manager, которые предоставляются с Windows [Tenant and host pool creation](troubleshoot-set-up-issues.md)Virtual Desktop, см. |
| Управление средой размещения сеансов виртуального рабочего стола Windows с портала Azure    | [Откройте запрос на поддержку Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Для проблем управления при использовании remote Desktop Services/Windows Virtual Desktop PowerShell см. [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) или [откройте запрос поддержки Azure,](https://azure.microsoft.com/support/create-ticket/)выберите **Windows Virtual Desktop** для службы, выберите **конфигурацию и управление** для типа проблемы, а затем выберите **Проблемы настройки клиента с помощью PowerShell** для проблемного подтипа. |
| Управление конфигурацией Windows Virtual Desktop, привязанной к пулам хостов и группам приложений (группы приложений)      | Смотрите [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md), или открыть запрос [поддержки Azure](https://azure.microsoft.com/support/create-ticket/), выберите **Windows Virtual Desktop** для службы, а затем выберите соответствующий тип проблемы.|
| Развертывание и управление контейнерами профиля FSLogix | Смотрите [руководство по устранению проблем для продуктов FSLogix,](/fslogix/fslogix-trouble-shooting-ht/) и если это не решит проблему, [откройте запрос поддержки Azure,](https://azure.microsoft.com/support/create-ticket/)выберите **Windows Virtual Desktop** для службы, выберите **FSLogix** для типа проблемы, а затем выберите соответствующий подтип проблемы. |
| Сбой удаленных настольных компьютеров при запуске                                                 | Просмотрите [проблему Сугения удаленного рабочего стола,](troubleshoot-client.md) и если это не решит проблему, [откройте запрос на поддержку Azure,](https://azure.microsoft.com/support/create-ticket/)выберите **Windows Virtual Desktop** для службы, а затем выберите для типа проблемы **клиенты Remote Desktop.**  <br> <br> Если это сетевая проблема, пользователям необходимо связаться со своим сетевым администратором. |
| Подключен, но не канал                                                                 | Устранение неполадок с помощью [пользователя подключается, но ничего не отображается (без канала)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) раздел [Windows Virtual Desktop соединения службы.](troubleshoot-service-connection.md) <br> <br> Если пользователи были назначены группе приложений, [откройте запрос поддержки Azure,](https://azure.microsoft.com/support/create-ticket/)выберите **виртуальный рабочий стол Windows** для службы, а затем выберите **удаленных настольных клиентов** для типа проблемы. |
| Проблемы обнаружения кормов из-за сети                                            | Пользователям необходимо связаться со своим сетевым администратором. |
| Подключение клиентов                                                                    | Просмотрите [соединения службы Windows Virtual Desktop,](troubleshoot-service-connection.md) и если это не решит вашу проблему, [см.](troubleshoot-vm-configuration.md) |
| Ответственность удаленных приложений или настольных компьютеров                                      | Если проблемы связаны с конкретным приложением или продуктом, обратитесь к группе, ответственной за этот продукт. |
| Лицензирование сообщений или ошибок                                                          | Если проблемы связаны с конкретным приложением или продуктом, обратитесь к группе, ответственной за этот продукт. |
| Проблемы при использовании инструментов Windows Virtual Desktop на GitHub (шаблоны управления ресурсами Azure, инструмент диагностики, инструмент управления) | Для сообщения о проблемах можно ознакомиться с [шаблонами диспетчера ресурсов Azure для удаленных настольных служб.](https://github.com/Azure/RDS-Templates/blob/master/README.md) |

## <a name="next-steps"></a>Дальнейшие действия

- Для устранения неполадок при создании пула арендатора и хоста в среде Windows Virtual Desktop [см.](troubleshoot-set-up-issues.md)
- Для устранения неполадок при настройке виртуальной машины (VM) в Windows Virtual Desktop [см.](troubleshoot-vm-configuration.md)
- Чтобы устранить неполадки с подключением клиентов Windows Virtual Desktop, [см.](troubleshoot-service-connection.md)
- Для устранения неполадок с клиентами Remote Desktop просмейте [о устранении проблем с клиентом Remote Desktop](troubleshoot-client.md)
- Для устранения неполадок при использовании [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)PowerShell с Windows Virtual Desktop см.
- Чтобы узнать больше об услуге, [см.](environment-setup.md)
- Чтобы пройти через учебник по устранению неполадок, [см.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Чтобы узнать о действиях для [View deployment operations](../azure-resource-manager/templates/deployment-history.md)определения ошибок во время развертывания, см.
