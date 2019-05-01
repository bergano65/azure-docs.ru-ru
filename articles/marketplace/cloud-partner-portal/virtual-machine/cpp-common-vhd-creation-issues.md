---
title: Распространенные проблемы во время создания виртуального жесткого диска (FAQ) для Azure Marketplace
description: Часто задаваемые вопросы о создании виртуального жесткого диска и связанные с ним проблемы.
services: Azure Marketplace
author: HannibalSII
ms.service: marketplace
ms.topic: article
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: d8b532793282db18d6182237bb921bc118ea717b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938460"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Распространенные проблемы при создании виртуального жесткого диска. Вопросы и ответы

Следующие часто задаваемые вопросы охватывают общие проблемы, возникающие во время создания виртуальных жестких дисков (VHD) и виртуальных машин для предложений виртуальных машин. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Как создать виртуальную машину на портале Azure с помощью виртуального жесткого диска, переданного в хранилище класса Premium?

Azure Marketplace в настоящее время не поддерживает создание предложений виртуальных машин на основе образов, находящихся в управляемом хранилище или в хранилище Azure класса Premium.  Дополнительные сведения о возможностях хранения данных доступны в [Обзоре компонента "Управляемые диски" Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Можно ли использовать виртуальные машины 2-го поколения для предложений?

Нет, поддерживаются только виртуальные жесткие диски 1-го поколения.  Однако в настоящее время мы работаем с группой разработчиков платформы Microsoft Azure, чтобы рассмотреть поддержку виртуальных машин 2-го поколения.  Дополнительные сведения см. в статье о том, [как выбрать поколение для виртуальной машины в Hyper-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Как изменить имя узла?

Вы не можете этого сделать.  После создания виртуальной машины пользователи (даже владельцы) не могут изменить имя узла.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Как сбросить службу удаленного рабочего стола или ее пароль для входа в систему?

В следующих статьях объясняется, как выполнить сброс RDS для виртуальных машин под управлением Windows и Linux:   

- [Сброс службы удаленных рабочих столов или ее пароля для входа в систему на виртуальной машине под управлением Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Использование расширения VMAccess для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска виртуальной машины Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Как создать сертификаты SSH?

Дополнительные сведения о создании сертификатов см. в статьях [Get shared access signature URI for your VM image](./cpp-get-sas-uri.md) (Получение URI подписанного URL-адреса для образа виртуальной машины) и [Create technical assets for a virtual machine offer](./cpp-create-technical-assets.md) (Создание технических ресурсов для предложения виртуальной машины).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Как настроить виртуальную частную сеть (VPN) для работы с моими виртуальными машинами?

Если вы используете модель развертывания Azure Resource Manager, то у вас есть три общих варианта настройки VPN:
- [Создание VPN-шлюза на основе маршрутов с помощью портала Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Создание VPN-шлюза на основе маршрутов с помощью PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Создание VPN-шлюза на основе маршрута с помощью CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Каковы политики поддержки корпорации Майкрософт для запуска серверного программного обеспечения Майкрософт на виртуальных машинах Azure?

Описание политик поддержки см. в статье [Поддержка серверного ПО Майкрософт для виртуальных машин Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Есть ли у виртуальных машин связанные с ними уникальные идентификаторы?

Да, если размещены в Azure.  Azure присваивает уникальный идентификатор виртуальной машины Azure каждому новому ресурсу виртуальной машины, который создается.  Дополнительные сведения см. в записи блога об [уникальных идентификаторах виртуальных машин Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Вы также можете получить этот идентификатор программным путем с помощью [API вывода списка](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Как управлять расширением пользовательских скриптов в задаче запуска на виртуальной машине?

В этой статье объясняется, как использовать расширение пользовательских сценариев с помощью модуля Azure PowerShell и шаблонов Azure Resource Manager, а также подробно описываются действия по устранению неполадок в системах Windows. [Расширение Custom Script в ОС Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Поддерживаются ли 32-разрядные приложения или службы в Azure Marketplace?

Как правило, нет.  Поддерживаемые операционные системы и стандартные службы для виртуальных машин Azure являются 64-разрядными.  Однако с технической точки зрения большинство 64-разрядных операционных систем поддерживают выполнение 32-разрядных версий приложений для обеспечения обратной совместимости.  Но использование 32-разрядных приложений как части вашего решения виртуальной машины не поддерживается и, следовательно, *не рекомендуется*.  Вместо этого нужно перекомпилировать приложение как 64-разрядный проект.

Дополнительные сведения см. в следующих статьях:
- [Статья о выполнении 32-разрядных приложений](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Статья о поддержке 32-разрядных операционных системах на виртуальных машинах Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Поддержка серверного ПО Майкрософт для виртуальных машин Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Каждый раз при попытке создать образ с помощью виртуального жесткого диска возникает ошибка `.VHD is already registered with image repository as the resource` в PowerShell. Я не создавал образы раньше, и в Azure отсутствует образ с таким именем. Как решить эту проблему?

Эта проблема обычно возникает, если пользователь подготовил виртуальную машину из виртуального жесткого диска, который имеет блокировку.  Убедитесь, что виртуальная машина не выделяется из такого виртуального жесткого диска, а затем повторите операцию.  Если проблема не устраняется, откройте запрос в службу поддержки, как описано в статье [Получение поддержки на Портале Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

