---
title: Распространенные проблемы при создании виртуального жесткого диска. Вопросы и ответы
description: Часто задаваемые вопросы о распространенных проблемах при создании виртуального жесткого диска (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: e96118e3c570faaaffb4cc7ef64e6d2d0e075e47
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954182"
---
# <a name="common-issues-during-vhd-creation"></a>Распространенные проблемы при создании виртуального жесткого диска

В данных часто задаваемых вопросах рассматриваются распространенные проблемы, которые могут возникнуть при создании виртуального жесткого диска (VHD) для предложения виртуальной машины Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Как создать виртуальную машину на портале Azure с помощью виртуального жесткого диска в хранилище класса Premium?

Azure Marketplace в настоящее время не поддерживает создание предложений виртуальных машин на основе образов, находящихся в управляемом хранилище или в хранилище Azure класса Premium. Дополнительные сведения см. в статье [Общие сведения об управляемых дисках Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Можно ли использовать виртуальные машины 2-го поколения для предложений?

Нет, поддерживаются только виртуальные жесткие диски 1-го поколения. Однако в настоящее время мы работаем с группой разработчиков платформы Microsoft Azure, чтобы рассмотреть поддержку виртуальных машин 2-го поколения. Подробнее см. в статье о том, [как выбрать поколение для виртуальной машины в Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="how-do-i-change-the-name-of-the-host"></a>Как изменить имя узла?

Его невозможно изменить. После создания виртуальной машины пользователи (включая владельцев) не смогут обновить имя узла.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Как сбросить службу удаленного рабочего стола или ее пароль для входа в систему?

В следующих статьях объясняется, как выполнить сброс RDS для виртуальных машин под управлением Windows и Linux.

* [Сброс службы удаленных рабочих столов или ее пароля для входа в систему на виртуальной машине под управлением Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Использование расширения VMAccess для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска виртуальной машины Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Как создать сертификаты SSH?

Создание сертификатов описано в разделе [Сертификация образа виртуальной машины Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Как настроить виртуальную частную сеть (VPN) для работы с моими виртуальными машинами?

Если вы используете модель развертывания Azure Resource Manager, у вас есть три варианта:

* [Создание VPN-шлюза на основе маршрутов с помощью портала Azure](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Создание VPN-шлюза на основе маршрутов с помощью Azure PowerShell](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Создание VPN-шлюза на основе маршрута с помощью CLI](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Каковы политики поддержки корпорации Майкрософт для запуска серверного программного обеспечения Майкрософт на виртуальных машинах Azure?

Подробнее см. в статье [Поддержка серверного ПО Майкрософт для виртуальных машин Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Есть ли у виртуальных машин связанные с ними уникальные идентификаторы?

Да, если размещены в Azure. Azure присваивает [уникальный идентификатор виртуальной машины Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) каждому новому ресурсу виртуальной машины, который создается. Дополнительные сведения см. в статье, посвященной уникальному идентификатору виртуальной машины Azure. Этот идентификатор также можно получить с помощью [списка API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Как выполняется управление расширением настраиваемых сценариев при запуске виртуальной машины?

Сведения об использовании расширения настраиваемых сценариев с помощью модуля Azure PowerShell, шаблонов Azure Resource Manager, а также сведения о действиях по устранению неполадок в системах Windows см. в статье [Расширение настраиваемого сценария в ОС Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Поддерживаются ли 32-разрядные приложения или службы в Azure Marketplace?

Как правило, нет. Поддерживаемые операционные системы и стандартные службы для виртуальных машин Azure являются 64-разрядными. Несмотря на то что большинство 64-разрядных операционных систем поддерживают 32-разрядные версии приложений для обеспечения обратной совместимости, использование 32-разрядных приложений в рамках решения виртуальной машины не поддерживается и не рекомендуется к использованию. Повторно создайте приложение как 64-разрядный проект.

Дополнительные сведения вы найдете в следующих статьях:

* [Статья о выполнении 32-разрядных приложений](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Статья о поддержке 32-разрядных операционных системах на виртуальных машинах Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Поддержка серверного ПО Майкрософт для виртуальных машин Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Ошибка: Виртуальный жесткий диск уже зарегистрирован в репозитории образов как ресурс

Каждый раз при попытке создать образ виртуальных жестких дисков в Azure PowerShell отображается следующее сообщение об ошибке: "VHD уже зарегистрирован в репозитории образов как ресурс". Я не создавал образы раньше, и в Azure отсутствует образ с таким именем. Как решить эту проблему?

Эта проблема обычно возникает, если вы создали виртуальную машину на основе виртуального жесткого диска, который имеет блокировку. Убедитесь, что виртуальная машина не выделяется из такого виртуального жесткого диска, а затем повторите операцию. Если проблема не устранена, отправьте запрос в службу поддержки. См. статью [Поддержка для Центра партнеров](support.md).
