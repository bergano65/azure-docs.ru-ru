---
title: Рекомендации по защите конечных точек в центрах безопасности Azure
description: Как обнаруживаются и идентифицируются решения по защите конечных точек как здоровые.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208548"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Оценка защиты конечных точек и рекомендации в Центре безопасности Azure

Центр безопасности Azure предоставляет оценку работоспособности [поддерживаемых](security-center-services.md#endpoint-supported) версий решений по защите конечных точек. В этой статье разъясняются сценарии, которые приводят Центр безопасности к созданию следующих двух рекомендаций:

* **Установка решений по защите конечных точек на виртуальной машине**
* **Устраните проблемы работоспособности защиты конечных точек на своих компьютерах**

## <a name="windows-defender"></a>Защитник Windows

* Центр безопасности рекомендует **вам "Установить решения по защите конечных точек на виртуальной машине",** когда [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) работает и результат **AMServiceEnabled: False**

* Центр безопасности рекомендует **вам "Решить проблемы защиты конечной точки на ваших компьютерах",** когда [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) работает и любой из следующих происходит:

  * Любые из следующих свойств являются ложными:

    **AMServiceEnabled**

    **Антишпионское посуда**

    **Режим реального времени**

    **ПоведениеMonitorВсте**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * Если один или оба из следующих свойств 7 или более.

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Защита конечных точек Microsoft System Center

* Центр безопасности рекомендует **вам "Установить решения по защите конечных точек на виртуальной машине"** при импорте **SCEPMpModule ("$env:ProgramFiles/Microsoft Security Client-MpProvider.psd1")** и запуск **get-MProtComputerStatus** результаты с **AMServiceEnabled - ложные**

* Центр безопасности рекомендует **вам "Решить проблемы защиты конечной точки на ваших машинах",** когда **Get-MprotComputerStatus** работает и любой из следующих происходит:

    * По крайней мере одно из следующих свойств является ложным:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Если один или оба из следующих обновлений подписи больше или равна 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Центр безопасности рекомендует **вам "Установить решения по защите конечных точек на виртуальной машине",** если ни один из следующих проверок не выполнен:
    * **HKLM: «ПРОГРАММНОе обеспечение»TrendMicro»Глубокое средство безопасности** существует
    * **HKLM: «ПРОГРАММА-TrendMicro»Глубокий агент безопасности-УстановкаFolder** существует
    * Файл **dsa_query.cmd** найден в файле установки
    * Запуск **dsa_query.cmd** результаты с **Component.AM.mode: на - Trend Micro Глубокая безопасность агент обнаружен**

## <a name="symantec-endpoint-protection"></a>Защита конечных точек Symantec
Центр безопасности рекомендует **вам "Установить решения по защите конечных точек на виртуальной машине",** если ни один из следующих проверок не выполнен:

* **HKLM: «Программное обеспечение»Symantec/Symantec Защита конечных точек»/ТекущаяВерсия-PRODUCTNAME - "Защита конечных точек Symantec"**

* **HKLM: «Программное обеспечение»Symantec/Symantec Защита от конечных точек/ТекущаяВерсия,публично-опсивайное государство»ASRunningStatus No 1**

либо

* **HKLM: «Программное обеспечение»Wow6432Node-Symantec-Symantec Защита конечных точек/ТекущаяВерсия-ПРОДУКТНАМЕ »**

* **HKLM: «Программное обеспечение»Wow6432Node-Symantec/Symantec Защита от конечных точек/ТекущаяВерсия,публично-опгосударство»ASRunningStatus No 1**

Центр безопасности рекомендует **вам "решить проблемы защиты конечных точек на ваших машинах",** если ни один из следующих проверок не выполнен:

* Проверка Symantec Версия >No 12: Местоположение реестра: **HKLM: «Программное обеспечение»Symantec/Symantec Защита от конечных точек»-CurrentVersion» -Value "PRODUCTVERSION"**

* Проверка статуса защиты в реальном времени: **HKLM: «Программное обеспечение»Wow6432Node»Symantec-Symantec Защита от конечных точек/AV-Storages/Filesystem**

* Проверить Подпись Обновление статуса: **HKLM-Software-Symantec-Symantec <Защита от конечных точек**

* Проверка полного статуса сканирования: **HKLM: «Программное обеспечение»Symantec/Symantec Защита конечных точек»-ТекущаяВерсия-public-opstate-LastSuccessfulScanDate, <**

* Найти подпись номер версии Путь к подписи версии для Symantec 12: **Путь реестра "ТекущийВерсия "SharedDefs" -Значение "SRTSP"** 

* Путь к подписной версии для Symantec 14: **Пути регистрации " CurrentVersion**

Пути регистрации:

* **"HKLM: (Программное обеспечение)Symantec-Symantec Защита конечных точек" - $Path;**
* **"HKLM:"Программное обеспечение-Wow6432Node -Symantec-Symantec Защита конечных точек" - $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Защита конечных точек McAfee для Windows

Центр безопасности рекомендует **вам "Установить решения по защите конечных точек на виртуальной машине",** если ни один из следующих проверок не выполнен:

* **HKLM: «ПРОГРАММА»МАКАфи/Конечная точка,АВ-ПродуктВерсия** существует

* **HKLM: «ПРОГРАММА»МАКАфи-АВСоф-МСШИЛСИГЛеБАЛЕАН-ГЛОБАЛЕВО**

Центр безопасности рекомендует **вам "решить проблемы защиты конечных точек на ваших машинах",** если ни один из следующих проверок не выполнен:

* McAfee Версия: **HKLM: »SOFTWARE-McAfee/End >point**

* Найти Подпись Версия: **HKLM: "Программное обеспечение"McAfee-AVSolution-DS-DS-Значение "dwContentMajorVersion"**

* Дата нахально: **HKLM: «Программное обеспечение»McAfee-AVSolution»DS-DS-Value "szContentCreationDate" >7 дней**

* Дата сканирования: **HKLM: «Программное обеспечение»McAfee/Endpoint/AV-ODS-Value "LastFullScanOdsRunTime" >7 дней**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Безопасность конечных точек McAfee для предотвращения угроз Linux 

Центр безопасности рекомендует **вам "Установить решения по защите конечных точек на виртуальной машине",** если ни один из следующих проверок не выполнен:

- Файл **/opt/isec/ens/threatprevention/bin/isecav** выходит 

- **"/opt/isec/ens/threatprevention/bin/isecav --версия"** выход: **McAfee имя - McAfee Endpoint Security для предупреждения угроз Linux и версия McAfee >No 10**

Центр безопасности рекомендует **вам "решить проблемы защиты конечных точек на ваших машинах",** если ни один из следующих проверок не выполнен:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** возвращает **быстрое сканирование, полное сканирование** и оба сканирования <7 дней

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** возвращает **DAT и время обновления двигателя,** и оба они <7 дней

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** **возвращается на** статус сканирования доступа

## <a name="sophos-antivirus-for-linux"></a>Антивирус Sophos для Linux 

Центр безопасности рекомендует **вам "Установить решения по защите конечных точек на виртуальной машине",** если ни один из следующих проверок не выполнен:

- Файл **/opt/sophos-av/bin/savdstatus** выходит или ищет индивидуальное местоположение **"readlink $(which savscan)"**

- **"/opt/sophos-av/bin/savdstatus --версия"** возвращает имя Софоса - **Sophos Anti-Virus и Sophos версия >No 9**

Центр безопасности рекомендует **вам "решить проблемы защиты конечных точек на ваших машинах",** если ни один из следующих проверок не выполнен:

- **"/opt/sophos-av/bin/savlog --maxage -7 grep-i "Запланированное сканирование . \* завершено" хвост -1"**, возвращает значение

- **"/opt/sophos-av/bin/savlog --maxage -7 grep "сканирование закончено"** хвост -1", возвращает значение

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** возвращается в прошлом обновлении, которое должно быть <7 дней 

- **"/opt/sophos-av/bin/savdstatus-v"** равно **"Сканирование доступа работает"** 

- **"/opt/sophos-av/bin/savconfig получить LiveProtection"** возвращается включено

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Обновления расширения Microsoft Antimalware доступны по адресу: **%Systemdrive% (WindowsAzure)Logs-Plugins-Microsoft.Azure.Security.IaaSAntimalware(Или PaaSAntimalware)**

### <a name="support"></a>Поддержка

Для получения дополнительной помощи обратитесь к экспертам Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/forums/) Или подайте в файл инцидента с поддержкой Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите Получить поддержку. Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).