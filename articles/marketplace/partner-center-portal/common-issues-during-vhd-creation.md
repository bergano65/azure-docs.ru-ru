---
title: Распространенные проблемы при создании виртуального жесткого диска. Вопросы и ответы
description: Часто задаваемые вопросы о распространенных проблемах при создании виртуального жесткого диска (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266270"
---
# <a name="common-issues-during-vhd-creation"></a>Распространенные проблемы при создании виртуального жесткого диска

> [!NOTE]
> Вы перемещаете Управление предложениями виртуальных машин Azure из Портал Cloud Partner в центр партнеров. Пока ваши предложения не будут перенесены, следуйте инструкциям в разделе [Общие проблемы при создании VHD (часто задаваемые вопросы)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) в портал Cloud Partner для управления вашими предложениями.

На часто задаваемые вопросы рассматриваются распространенные проблемы, которые могут возникнуть при создании виртуального жесткого диска (VHD) для предложения виртуальной машины Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Разделы справки создать виртуальную машину из портал Azure с помощью виртуального жесткого диска в хранилище класса Premium?

В настоящее время Azure Marketplace не поддерживает создание предложений виртуальных машин из образов в управляемом хранилище или хранилище Azure класса Premium. Дополнительные сведения см. в [обзоре управляемых дисков Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Можно ли использовать виртуальные машины версии 2 для предложений?

Нет, поддерживаются только виртуальные жесткие диски поколения 1. Однако в настоящее время мы работаем с группой Microsoft Azure Platform, чтобы исследовать поддержку виртуальных машин поколения 2. Дополнительные сведения см [. в статье Создание виртуальной машины поколения 1 или 2 в Hyper-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Как изменить имя узла?

Это невозможно. После создания виртуальной машины пользователи (включая владельцы) не смогут обновить имя узла.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Разделы справки сбросить службу удаленный рабочий стол или пароль для входа?

В этих статьях объясняется, как выполнять сбросы RDS для виртуальных машин под управлением Windows и Linux.

* [Сброс службы удаленных рабочих столов или ее пароля для входа в систему на виртуальной машине под управлением Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Использование расширения VMAccess для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска виртуальной машины Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Разделы справки создать новые сертификаты SSH?

Создание сертификатов объясняется в [сертификации образа виртуальной машины Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Разделы справки настроить виртуальную частную сеть (VPN) для работы с виртуальными машинами?

Если вы используете модель развертывания Azure Resource Manager, у вас есть три варианта:

* [Создание VPN-шлюза на основе маршрутов с помощью портал Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Создание VPN-шлюза на основе маршрутов с помощью Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Создание VPN-шлюза на основе маршрута с помощью CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Каковы политики поддержки корпорации Майкрософт для запуска серверного программного обеспечения Майкрософт на виртуальных машинах Azure?

Сведения о [Microsoft Azure виртуальных машинах можно найти в технической поддержке Microsoft Server Software](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Есть ли у виртуальных машин связанные с ними уникальные идентификаторы?

Да, если размещены в Azure. Azure назначает уникальный идентификатор, называемый [уникальным идентификатором виртуальной машины Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), для каждого созданного ресурса виртуальной машины. Дополнительные сведения см. в статье уникальный идентификатор виртуальной машины Azure. Этот идентификатор также можно получить с помощью [API списка](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Как управлять расширением пользовательских сценариев на виртуальной машине в задаче запуска?

Дополнительные сведения об использовании расширения пользовательских сценариев с помощью модуля Azure PowerShell, Azure Resource Manager шаблонов и действиях по устранению неполадок в системах Windows см. в разделе [расширение пользовательских сценариев для Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Поддерживаются ли в Azure Marketplace 32-разрядные приложения или службы?

Как правило, нет. Поддерживаемые операционные системы и стандартные службы для виртуальных машин Azure являются 64-разрядными. Хотя большинство 64-разрядных операционных систем поддерживают 32-разрядные версии приложений для обеспечения обратной совместимости, использование 32-разрядных приложений в рамках решения виртуальной машины не поддерживается и не рекомендуется к использованию. Повторно создайте приложение как 64-разрядный проект.

Дополнительные сведения вы найдете в следующих статьях:

* [Статья о выполнении 32-разрядных приложений](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Поддержка 32-разрядных операционных систем на виртуальных машинах Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Поддержка серверного ПО Майкрософт для виртуальных машин Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Ошибка: виртуальный жесткий диск уже зарегистрирован в репозитории образов как ресурс

При каждой попытке создать образ из виртуальных жестких дисков я получаю сообщение об ошибке "виртуальный жесткий диск уже зарегистрирован в репозитории образов как ресурс" в Azure PowerShell. Я не создал образ, пока не нашел образ с таким именем в Azure. Как решить эту проблему?

Эта проблема обычно возникает, если вы создали виртуальную машину на основе виртуального жесткого диска с блокировкой. Убедитесь, что с этим виртуальным жестким диском не выделена виртуальная машина, и повторите операцию. Если проблема не устранена, отправьте запрос в службу поддержки. См. раздел [Поддержка центра партнеров](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
