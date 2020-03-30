---
title: Сбои обнаружения VMware vCenter в восстановлении сайта Azure
description: В этой статье описывается, как устраивать сбои обнаружения VMware vCenter в восстановлении сайта Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091251"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Сбои обнаружения сервера обнаружения VCenter Server

Эта статья поможет вам устранить неполадки, возникающие из-за сбоев обнаружения VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Нечисло значений в свойстве maxSnapShots

В версиях до 9.20 vCenter отключается при получении нечисленного `snapshot.maxSnapShots` значения для свойства на VM.

Эта проблема идентифицируется по идентификатору ошибок 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Чтобы устранить проблему:

- Определите VM и установите значение для численного значения (настройки VM Edit в vCenter).

либо

- Обновите сервер конфигурации до версии 9.20 или позже.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Проблемы конфигурации прокси для подключения vCenter

vCenter Discovery выполняет настройки прокси-сервера системы по умолчанию, настроенные пользователем Системы. Служба DRA выполняет настройки прокси, предоставляемые пользователем во время установки сервера конфигурации с помощью единого установщика установки или шаблона OVA. 

Как правило, прокси используется для общения в публичных сетях; например, общение с Azure. Если прокси настроен и vCenter находится в локальной среде, он не сможет общаться с DRA.

При возникновении этой проблемы возникают следующие ситуации:

- Сервер \<vCenter vCenter> недоступен из-за ошибки: Удаленный сервер вернул ошибку: (503) Сервер недоступен
- Сервер \<vCenter vCenter> недоступен из-за ошибки: Удаленный сервер вернул ошибку: Невозможно подключиться к удаленному серверу.
- Невозможно подключиться к серверу vCenter/ESXi.

Чтобы устранить проблему:

Скачать [инструмент PsExec](https://aka.ms/PsExec). 

Используйте инструмент PsExec для доступа к контексту пользователя Системы и определения того, настроен ли прокси-адрес. Затем можно добавить vCenter в список обхода, используя следующие процедуры.

Для конфигурации прокси-сервера Discovery:

1. Откройте IE в контексте системного пользователя с помощью инструмента PsExec.
    
    psexec-s-i "%programfiles% -Internet Explorer-iexplore.exe"

2. Измените настройки прокси в Internet Explorer, чтобы обойти IP-адрес vCenter.
3. Перезапустите сервис tmanssvc.

Для конфигурации прокси DRA:

1. Откройте запрос команды и откройте папку microsoft Azure Site Recovery Provider.
 
    **cd C: »Файлы программы»-Поставщик восстановления azure сайта Microsoft**

3. Из запроса команды запустите следующую команду.
   
   **ДРОНФИГУРАТОР. EXE /настраивается /AddBypassUrls (IP-адрес/ФЗДН vCenter Server, предоставленный на момент добавления vCenter)**

4. Перезапустите услугу поставщика DRA.

## <a name="next-steps"></a>Дальнейшие действия

[Управление сервером конфигурации для аварийного восстановления виртуальных машин VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
