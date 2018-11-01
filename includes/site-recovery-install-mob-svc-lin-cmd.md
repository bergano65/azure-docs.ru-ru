---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 2b92aba8b9a8d8f46ae2aeac3a7bfe60a4755f9b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164423"
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
|-v|Обязательно|Указывает платформу, на которой будет установлена служба Mobility Service. </br> </br>- **VMware**. Используйте это значение при установке службы Mobility Service на виртуальной машине под управлением *узлов VMware vSphere ESXi*, *узлов Hyper-V* или *физических серверов*. </br> - **Azure**. Используйте это значение при установке агента на виртуальную машину Azure IaaS.| VMware </br> Таблицы Azure|
|-q|Необязательно|Используется для запуска установщика в автоматическом режиме.| Недоступно|


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
