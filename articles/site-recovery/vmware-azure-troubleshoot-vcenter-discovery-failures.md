---
title: Устранение неполадок при восстановлении размещения в локальной среде во время аварийного восстановления виртуальной машины VMware в Azure с помощью Azure Site Recovery
description: В этой статье описываются способы устранения неполадок восстановления размещения и повторного включения защиты в процессе аварийного восстановления виртуальных машин VMware в Azure с помощью Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: e9213637f45a4761af60de9dfac7add6324f6b96
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053853"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Устранять неполадок обнаружения vCenter

Эта статья поможет вам устранить неполадки, возникающие из-за сбоев при обнаружении VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Нечисловые значения в свойстве Максснапшотс

В версиях до 9,20 vCenter отключается при извлечении нечислового значения свойства `snapshot.maxSnapShots` на виртуальной машине.

Эта проблема определяется по ИДЕНТИФИКАТОРу ошибки 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Чтобы устранить проблему:

- Укажите виртуальную машину и присвойте ей числовое значение (параметры изменения виртуальной машины в vCenter).

или

- Обновите сервер конфигурации до версии 9,20 или более поздней.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Проблемы конфигурации прокси-сервера для подключения vCenter

Обнаружение vCenter учитывает системные параметры прокси-сервера по умолчанию, настроенные системным пользователем. Служба DRA учитывает параметры прокси-сервера, предоставленные пользователем во время установки сервера конфигурации с помощью единой программы установки или шаблона OVA. 

Как правило, прокси-сервер используется для связи с общедоступными сетями. Например, взаимодействие с Azure. Если прокси-сервер настроен и vCenter находится в локальной среде, он не сможет взаимодействовать с DRA.

При возникновении этой проблемы возникают следующие ситуации.

- Сервер vCenter \<vCenter > недоступен из-за ошибки: удаленный сервер вернул ошибку: (503) сервер недоступен.
- Сервер vCenter \<vCenter > недоступен из-за ошибки: удаленный сервер вернул ошибку: не удалось подключиться к удаленному серверу.
- Не удалось подключиться к серверу vCenter/ESXi.

Чтобы устранить проблему:

Загрузите [Средство PsExec](https://aka.ms/PsExec). 

Используйте средство PsExec для доступа к контексту пользователя системы и определите, настроен ли адрес прокси-сервера. Затем можно добавить vCenter в список обхода, выполнив следующие процедуры.

Для конфигурации прокси-сервера обнаружения:

1. Откройте IE в контексте пользователя системы с помощью средства PsExec.
    
    PsExec-s-i "%Програмфилес%\интернет Explorer\iexplore.exe"

2. Измените параметры прокси в Internet Explorer, чтобы обойти IP-адрес vCenter.
3. Перезапустите службу тманссвк.

Для конфигурации прокси-сервера DRA:

1. Откройте командную строку и откройте папку поставщика Microsoft Azure Site Recovery.
 
    **Поставщик Azure Site Recovery CD C:\Program Files\Microsoft**

3. В командной строке выполните следующую команду.
   
   **DRCONFIGURATOR. EXE/configure/Аддбипассурлс [IP-адрес/полное доменное имя vCenter Server предоставлено во время добавления vCenter]**

4. Перезапустите службу поставщика DRA.

## <a name="next-steps"></a>Дальнейшие действия

[Управление сервером конфигурации для аварийного восстановления виртуальной машины VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
