---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: aa2e6d80620f0a4cf5063919a6de53e4de20f706
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58115519"
---
1. Скопируйте установщик в локальную папку (например, /tmp) на сервере, который необходимо защитить. Выполните следующие команды в окне терминала.
   ```
   cd /tmp ;

   tar -xvzf Microsoft-ASR_UA*release.tar.gz
   ```
2. Чтобы установить службу Mobility Service, выполните следующую команду.

   ```
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```
3. После завершения установки службу Mobility Service необходимо зарегистрировать на сервере конфигурации. Выполните следующую команду, чтобы зарегистрировать службу Mobility Service на сервере конфигурации.

   ```
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="mobility-service-installer-command-line"></a>Командная строка установщика Mobility Service

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Параметр|type|ОПИСАНИЕ|Возможные значения|
|-|-|-|-|
|-r |Обязательно|Указывает службу, которую нужно установить: Mobility Service (MS) или Master Target (MT).|MS </br> MT|
|-d |Необязательно|Расположение, в котором установлена служба Mobility Service|/usr/local/ASR|
|-v|Обязательно|Указывает платформу, на которой будет установлена служба Mobility Service. </br> </br>- **VMware**: Используйте это значение при установке службы Mobility Service на виртуальной Машине под управлением *узлов VMware vSphere ESXi*, *узлов Hyper-V*, и *физических серверов*. </br> - **Azure**: Используйте это значение при установке агента на виртуальной Машине IaaS Azure.| VMware </br> Таблицы Azure|
|-q|Необязательно|Используется для запуска установщика в автоматическом режиме.| Н/Д|


#### <a name="mobility-service-configuration-command-line"></a>Командная строка конфигурации службы Mobility Service

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Параметр|type|ОПИСАНИЕ|Возможные значения|
|-|-|-|-|
|-i |Обязательно|IP-адрес сервера конфигурации|Любой допустимый IP-адрес|
|-P |Обязательно|Полный путь к файлу, в котором хранится парольная фраза для подключения|Любая допустимая папка|
