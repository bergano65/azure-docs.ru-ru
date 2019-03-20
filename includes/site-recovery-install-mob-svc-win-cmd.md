---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9fe3b66de83ebc2cd0bf3a56a45456668c069191
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116251"
---
1. Скопируйте установщик в локальную папку (например, C:\Temp) на сервере, который необходимо защитить. В командной строке выполните следующие команды от имени администратора.

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. Чтобы установить службу Mobility Service, выполните следующую команду.

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```
3. Теперь необходимо зарегистрировать агент на сервере конфигурации.

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Команда установщика службы Mobility Service: аргументы строки

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Параметр|type|ОПИСАНИЕ|Возможные значения|
|-|-|-|-|
|/Role|Обязательно|Указывает службу, которую нужно установить: Mobility Service (MS) или Master Target (MT).|MS </br> MT|
|/InstallLocation|Необязательно|Расположение, в котором установлена служба Mobility Service|Любая папка на компьютере.|
|/Platform|Обязательно|Указывает платформу, на которой будет установлена служба Mobility Service. </br> </br>- **VMware**: Используйте это значение при установке службы Mobility Service на виртуальной Машине под управлением *узлов VMware vSphere ESXi*, *узлов Hyper-V*, и *физических серверов*. </br> - **Azure**: Используйте это значение при установке агента на виртуальной Машине IaaS Azure. | VMware </br> Таблицы Azure|
|/Silent|Необязательно|Используется для запуска установщика в автоматическом режиме.| Н/Д|

>[!TIP]
> Журналы установки находятся в папке %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Аргументы командной строки регистрации службы Mobility Service

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Параметр|type|ОПИСАНИЕ|Возможные значения|
  |-|-|-|-|
  |/CSEndPoint |Обязательно|IP-адрес сервера конфигурации| Любой допустимый IP-адрес|
  |/PassphraseFilePath|Обязательно|Расположение файла c парольной фразой |Любой допустимый локальный путь к файлу или UNC|


>[!TIP]
> Журналы конфигурации агента находятся в папке %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
