---
title: Обзор устранения неполадок виртуальных рабочих столов Windows — Azure
description: Общие сведения об устранении неполадок при настройке среды клиента виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.author: helohr
ms.openlocfilehash: 817cd09797ad861521a462da9c5d0850c65bf562
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459570"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Общие сведения об устранении неисправностей, отзывы и поддержка

В этой статье приводятся общие сведения о проблемах, которые могут возникнуть при настройке среды клиента виртуальных рабочих столов Windows, а также способы решения этих проблем.

## <a name="provide-feedback"></a>Отправить отзыв

Посетите [техническое сообщество Виртуального рабочего стола Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), чтобы обсудить службу "Виртуальный рабочий стол Windows" с группой разработчиков и активными членами сообщества.

## <a name="escalation-tracks"></a>Дорожки эскалации

Используйте следующую таблицу для определения и устранения проблем, с которыми вы можете столкнуться при настройке среды клиента с помощью клиента удаленный рабочий стол. После настройки клиента можно использовать нашу новую [службу диагностики](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) для определения проблем в распространенных сценариях.

>[!NOTE]
> У нас есть форум технического сообщества, который можно использовать для обсуждения проблем, связанных с группой разработки продукта и активными членами сообщества. Посетите [сообщество специалистов по виртуальным рабочим столам Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , чтобы 

| **Проблема**                                                            | **Предлагаемое решение**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Создание клиента                                                    | В случае сбоя Azure обратитесь в [службу поддержки Azure](https://azure.microsoft.com/support/options/). в противном случае **откройте запрос в службу поддержки для виртуальных рабочих столов Windows (COMPUTE)** .|
| Доступ к шаблонам Marketplace в портал Azure       | В случае сбоя Azure обратитесь в [службу поддержки Azure](https://azure.microsoft.com/support/options/). <br> <br> Шаблоны виртуальных рабочих столов Windows в Azure Marketplace доступны бесплатно.|
| Доступ к шаблонам Azure Resource Manager из GitHub                                  | См. раздел "Создание виртуальных машин узла сеансов виртуальных рабочих столов Windows" в разделе [Создание пула клиентов и узлов](troubleshoot-set-up-issues.md). Если проблема не устранена, обратитесь в службу [поддержки GitHub](https://github.com/contact). <br> <br> Если эта ошибка возникает после доступа к шаблону в GitHub, обратитесь в [службу поддержки Azure](https://azure.microsoft.com/support/options/).|
| Виртуальная сеть Azure пула узлов сеансов и параметры Express Route               | Обратитесь в **службу поддержки Azure (сеть)** . |
| Создание виртуальной машины пула узлов сеансов, если не используются шаблоны Azure Resource Manager, предоставляемые виртуальным рабочим столом Windows | Обратитесь в **службу поддержки Azure (COMPUTE)** . <br> <br> Сведения о проблемах с шаблонами Azure Resource Manager, предоставляемыми с виртуальным рабочим столом Windows, см. в разделе Создание клиента виртуальных рабочих столов Windows для [создания пула клиентов и узлов](troubleshoot-set-up-issues.md). |
| Управление средой размещения сеансов виртуальных рабочих столов Windows из портал Azure    | Обратитесь в **службу поддержки Azure**. <br> <br> Сведения об ошибках управления при использовании службы удаленных рабочих столов или Windows Virtual Desktop PowerShell см. в статье [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) или **откройте запрос в службу поддержки для виртуальных рабочих столов Windows (COMPUTE)** . |
| Управление конфигурацией виртуальных рабочих столов Windows, привязанной к пулам узлов и группам приложений (группы приложений)      | Ознакомьтесь с разокном [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)или отправьте **запрос в службу поддержки для виртуальных рабочих столов Windows (COMPUTE)** . <br> <br> Если проблемы связаны с примером графического пользовательского интерфейса, обратитесь к сообществу сообщества Yammer.|
| Клиенты удаленного рабочего стола неисправны при запуске                                                 | См. раздел [Устранение неполадок с клиентом удаленный рабочий стол](troubleshoot-client.md) и если проблема не устранена, **откройте запрос в службу поддержки для Windows Virtual Desktop (вычисление)** .  <br> <br> Если это сетевая ошибка, пользователям необходимо обратиться к администратору сети. |
| Подключено, но нет канала                                                                 | Устранение неполадок с помощью команды "пользователь подключается, но ничего не отображается (без канала)" статьи [Устранение неполадок удаленный рабочий стол клиента](troubleshoot-client.md). <br> <br> Если пользователи были назначены группе приложений, отправьте **запрос в службу поддержки для виртуальных рабочих столов Windows (COMPUTE)** . |
| Проблемы обнаружения веб-канала по сети                                            | Пользователям необходимо обратиться к администратору сети. |
| Подключение клиентов                                                                    | См. раздел [подключения к службе виртуальных рабочих столов Windows](troubleshoot-service-connection.md) . Если это не поможет, см. раздел [Конфигурация виртуальной машины узла сеансов](troubleshoot-vm-configuration.md). |
| Скорость реагирования удаленных приложений или настольных компьютеров                                      | Если проблемы связаны с конкретным приложением или продуктом, обратитесь к группе, ответственной за этот продукт. |
| Сообщения о лицензировании или ошибки                                                          | Если проблемы связаны с конкретным приложением или продуктом, обратитесь к группе, ответственной за этот продукт. |
| Проблемы при использовании средств виртуальных рабочих столов Windows на GitHub (шаблоны Azure Resource Manager, средство диагностики, средство управления) | Сведения о проблемах см. в статье [Azure Resource Manager Templates for службы удаленных рабочих столов](https://github.com/Azure/RDS-Templates/blob/master/README.md) . |

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об устранении неполадок при создании клиента и пула узлов в среде виртуальных рабочих столов Windows см. в статье [Создание пула клиентов и узлов](troubleshoot-set-up-issues.md).
- Сведения об устранении неполадок при настройке виртуальной машины в виртуальном рабочем столе Windows см. в разделе [Конфигурация виртуальной машины узла сеанса](troubleshoot-vm-configuration.md).
- Сведения об устранении неполадок подключения клиентов к виртуальным рабочим столам Windows см. в статье [подключения к службе виртуальных рабочих столов Windows](troubleshoot-service-connection.md).
- Сведения об устранении неполадок, связанных с удаленный рабочий стол клиентами, см. [в разделе Устранение неполадок клиента удаленный рабочий стол](troubleshoot-client.md)
- Сведения об устранении неполадок при использовании PowerShell с виртуальным рабочим столом Windows см. в статье [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Дополнительные сведения о службе см. в разделе [Среда виртуальных рабочих столов Windows](environment-setup.md).
- Руководство по устранению неполадок см. в разделе [учебник. Устранение неполадок диспетчер ресурсов развертываний шаблонов](../azure-resource-manager/resource-manager-tutorial-troubleshoot.md).
- Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](../azure-resource-manager/resource-group-audit.md).
- Сведения о действиях по определению ошибок при развертывании см. в разделе [Просмотр операций развертывания](../azure-resource-manager/resource-manager-deployment-operations.md).
