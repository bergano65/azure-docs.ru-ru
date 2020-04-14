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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266270"
---
# <a name="common-issues-during-vhd-creation"></a>Общие проблемы при создании VHD

> [!NOTE]
> Мы перемещаем управление вашими предложениями Azure VM с облачного партнерского портала в партнерский центр. До тех пор, пока ваши предложения не будут перенесены, пожалуйста, продолжайте следовать инструкциям в [общих вопросах во время создания VHD (Частова»,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) в облачном портале партнеров для управления вашими предложениями.

Эти часто задаваемые вопросы (Часто задаваемые вопросы) охватывают общие проблемы, с которыми вы можете столкнуться при создании виртуального жесткого диска (VHD) для предложения Виртуальной машины Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Как создать VM с портала Azure с помощью VHD в премиум-хранилище?

В настоящее время Azure Marketplace не поддерживает создание vM-предложений из изображений в управляемом хранилище или из премиум-хранилища Azure. Для получения подробной информации [см.](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)

## <a name="can-i-use-generation-2-vms-for-offers"></a>Могу ли я использовать вдыхать в поколение 2 для предложений?

Нет, поддерживаются только VHD поколения 1. Тем не менее, в настоящее время мы работаем с группой платформы Microsoft Azure для изучения поддержки ВМ поколения 2. Для получения подробной информации, [см.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Как изменить имя узла?

Это невозможно. После создания VM пользователи (включая владельцев) не могут обновить имя хоста.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Как сбросить службу удаленного рабочего стола или пароль для ввоза?

В этих статьях объясняется, как выполнять сбросы RDS для VMs на базе Windows и Linux:

* [Сброс службы удаленных рабочих столов или ее пароля для входа в систему на виртуальной машине под управлением Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Использование расширения VMAccess для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска виртуальной машины Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Как создать новые сертификаты SSH?

Поколение сертификатов объясняется в [сертификации изображений Azure VM.](https://aks.ms/CertifyVMimage)

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Как настроить виртуальную частную сеть (VPN) для работы с вводом?

Если вы используете модель развертывания ресурсов Azure Manager, у вас есть три варианта:

* [Создание VPN-шлюза на основе маршрутов с помощью портала Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Создание VPN-шлюза на основе маршрутов с помощью Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Создание VPN-шлюза на основе маршрута с помощью CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Каковы политики поддержки корпорации Майкрософт для запуска серверного программного обеспечения Майкрософт на виртуальных машинах Azure?

Вы можете найти подробную информацию в [службе программного обеспечения сервера Майкрософт для виртуальных машин Microsoft Azure.](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Есть ли у виртуальных машин связанные с ними уникальные идентификаторы?

Да, если размещены в Azure. Azure присваивает каждому новому созданому ресурсу VM уникальный идентификатор под названием [Azure Virtual Machine Unique ID.](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) Для получения подробной информации смотрите уникальный идентификатор виртуальной машины Azure. Вы также можете получить этот идентификатор через [API списка.](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Как в VM управлять расширением пользовательского сценария в задаче запуска?

Для получения подробной информации об использовании расширения пользовательского сценария с помощью модуля Azure [Custom Script Extension for Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)PowerShell, шаблонов Azure Resource Manager и шагов по устранению неполадок в системах Windows см.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Поддерживаются ли 32-разрядные приложения или службы в Azure Marketplace?

Как правило, нет. Поддерживаемые операционные системы и стандартные службы для виртуальных машин Azure являются 64-разрядными. Хотя большинство 64-разрядных операционных систем поддерживают 32-битные версии приложений для обратной совместимости, использование 32-битных приложений как части вашего решения VM не поддерживается и крайне не рекомендуется. Воссоздайте приложение как 64-разрядный проект.

Дополнительные сведения вы найдете в следующих статьях:

* [Статья о выполнении 32-разрядных приложений](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Поддержка 32-разрядных операционных систем на виртуальных машинах Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Поддержка серверного ПО Майкрософт для виртуальных машин Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Ошибка: VHD уже зарегистрирован в репозитории изображений как ресурс

Каждый раз, когда я пытаюсь создать изображение из моих VHD, я получаю ошибку "VHD уже зарегистрирован в репозитории изображений в качестве ресурса" в Azure PowerShell. Раньше я не создавал никакого изображения и не нашел в Azure изображения с этим именем. Как решить эту проблему?

Эта проблема обычно возникает, если вы создали VM из VHD, который имеет блокировку на нем. Подтвердите, что из этого VHD нет VM, а затем повторите операцию. Если эта проблема продолжается, откройте билет поддержки. Смотрите [Поддержку для партнерского центра](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
