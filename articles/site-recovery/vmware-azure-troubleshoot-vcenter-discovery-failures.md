---
title: Устранение неполадок при восстановлении размещения в локальную инфраструктуру во время аварийного восстановления виртуальных машин VMware в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описываются способы устранения неполадок восстановления размещения и повторного включения защиты в процессе аварийного восстановления виртуальных машин VMware в Azure с помощью Azure Site Recovery.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565197"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Устранять неполадок обнаружения vCenter

Эта статья поможет вам устранить проблемы, возникающие из-за ошибки обнаружения vCenter VMware.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Нечисловые значения в свойстве maxSnapShots

В версиях, предшествующих 9.20, vCenter отключается при извлечении нечисловое значение для свойства `snapshot.maxSnapShots` свойство на виртуальной Машине.

Идентификатор ошибки 95126 определяет эту проблему.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Чтобы устранить проблему:

- Найдите виртуальную Машину и задайте значение в числовое значение (виртуальной Машины, изменение параметров на сервере vCenter).

или

- Обновите сервер конфигурации до версии 9.20 или более поздней версии.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Проблемы конфигурации прокси-сервера для подключения к vCenter

vCenter обнаружении учитывается системные настройки прокси-сервера по умолчанию, заданный пользователем системы. Служба агента DRA учитывает параметры прокси-сервера, указанное пользователем во время установки сервера конфигурации с помощью программы единой установки установщика или ova-файла шаблона. 

Как правило прокси-сервер используется для связи к общедоступным сетям; Например общение с Azure. Если прокси-сервер настроен и vCenter находится в локальной среде, оно может взаимодействовать с DRA.

При возникновении этой проблемы возникновения следующих ситуаций:

- Сервер vCenter \<vCenter > недоступен из-за ошибки: The remote server returned an error: (503) сервер недоступен
- Сервер vCenter \<vCenter > недоступен из-за ошибки: The remote server returned an error: Не удалось подключиться к удаленному серверу.
- Не удалось подключиться к серверу vCenter или ESXi.

Чтобы устранить проблему:

Скачайте [средством PsExec](https://aka.ms/PsExec). 

Средство PsExec для доступа к контексту пользователя системы и определить, настроен ли адрес прокси-сервера. Затем можно добавить сервер vCenter, список обхода, с помощью следующих процедур.

Для обнаружения конфигурации прокси-сервера:

1. Откройте IE в контексте пользователя системы, используя средство PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Измените параметры прокси-сервера в Internet Explorer для обхода IP-адрес vCenter.
3. Перезапустите службу tmanssvc.

Для конфигурации прокси-сервера агента DRA:

1. Откройте командную строку и откройте папку поставщика Microsoft Azure Site Recovery.
 
    **Поставщик CD C:\Program Files\Microsoft Azure Site Recovery**

3. В командной строке выполните следующую команду.
   
   **DRCONFIGURATOR. EXE / configure /AddBypassUrls [IP адрес или полное ДОМЕННОЕ имя vCenter Server предоставил во время добавления vCenter]**

4. Перезапустите службу агента DRA поставщика.

## <a name="next-steps"></a>Дальнейшие действия

[Управление сервером конфигурации для аварийного восстановления виртуальных Машин VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
