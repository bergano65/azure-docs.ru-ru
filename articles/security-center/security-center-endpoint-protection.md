---
title: ENDPOINT protection решения обнаружение и оценка работоспособности в центре безопасности Azure | Документация Майкрософт
description: Принципы решения для защиты конечной точки обнаружения и определены как работоспособные.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247969"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Оценка защиты конечной точки и рекомендации в центре безопасности Azure

Оценка защиты конечной точки и рекомендации в центре безопасности Azure обнаруживает и предоставляет оценку работоспособности [поддерживается](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) версии решения для защиты конечной точки. В этом разделе объясняется, сценарии, которые создают следующие две рекомендации для решения для защиты конечной точки с помощью центра безопасности Azure.

* **Установка решения для защиты конечной точки на виртуальной машине**
* **Устранения неполадок работоспособности endpoint protection на компьютерах**

## <a name="windows-defender"></a>Защитник Windows

* **«Установка решения для защиты конечной точки на виртуальной машине»** рекомендация создается при [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) запусков и результатом является **AMServiceEnabled: значение false**

* **«Устранение неполадок работоспособности endpoint protection на компьютерах»** рекомендация создается при [Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) происходит запусков и одно или оба указанных ниже:

  * По крайней мере одно из следующих свойств имеет значение false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Если один или оба следующих свойства больше или равно 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* **«Установка решения для защиты конечной точки на виртуальной машине»** рекомендация создается при импорте **SCEPMpModule («$env: Client\MpProvider\MpProvider.psd1 ProgramFiles\Microsoft безопасности»)** и запуск **Get-MProtComputerStatus** результатов для **AMServiceEnabled = false**

* **«Устранение неполадок работоспособности endpoint protection на компьютерах»** рекомендация создается при **Get MprotComputerStatus** происходит запусков и одно или оба указанных ниже:

    * По крайней мере одно из следующих свойств имеет значение false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Если один или оба из следующих обновлений подписи больше или равно 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* **«Установка решения для защиты конечной точки на виртуальной машине»** рекомендация создается в том случае, если один или несколько из следующих проверок не соблюдены:
    * **Агент безопасности HKLM:\SOFTWARE\TrendMicro\Deep** существует
    * **Agent\InstallationFolder безопасности HKLM:\SOFTWARE\TrendMicro\Deep** существует
    * **Dsq_query.cmd** файл находится в папке установки
    * Под управлением **dsa_query.cmd** результатов для **Component.AM.mode: на - тренда Micro Deep Security Agent обнаружил**

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
**«Установка решения для защиты конечной точки на виртуальной машине»** рекомендация создается в том случае, если не выполняется одно из следующих проверок:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

или

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

**«Устранение неполадок работоспособности endpoint protection на компьютерах»** рекомендация создается в том случае, если не выполняется одно из следующих проверок:  

* Проверьте версию Symantec > = 12:  Раздел реестра: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Проверка состояния защиты в реальном времени: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Проверка состояния обновления подписи: **Конечная точка HKLM\Software\Symantec\Symantec Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 дней**

* Проверьте состояние Full Scan. **Конечная точка HKLM:\Software\Symantec\Symantec Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 дней**

* Найти номер версии подпись путь до версии подписи для Symantec 12: **Пути реестра + «CurrentVersion\SharedDefs»-значение «SRTSP»** 

* Путь к версии подписи Symantec 14: **Пути реестра + «CurrentVersion\SharedDefs\SDSDefs»-значение «SRTSP»**

Пути в реестре:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee endpoint protection для Windows

**«Установка решения для защиты конечной точки на виртуальной машине»** рекомендация создается в том случае, если не соблюдены следующие проверки:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** exists

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

**«Устранение неполадок работоспособности endpoint protection на компьютерах»** рекомендация создается в том случае, если не соблюдены следующие проверки:

* McAfee версия: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Найти версию подписи: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Найти Дата подписи: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Найти даты сканирования: **HKLM:\Software\McAfee\Endpoint\AV\ODS-значение «LastFullScanOdsRunTime» > = 7 дней**

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Журналы расширение защиты от вредоносных программ Майкрософт доступны по:  
**%SystemDrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (или PaaSAntimalware)\1.5.5.x (номер версии) \CommandExecution.log**

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Или вы можете отправить обращение за поддержкой Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
