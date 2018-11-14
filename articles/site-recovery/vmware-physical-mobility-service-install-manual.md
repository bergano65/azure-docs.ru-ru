---
title: Ручная установка службы Mobility Service для аварийного восстановления виртуальных машин VMware и физических серверов с помощью Azure Site Recovery | Документация Майкрософт
description: Узнайте, как установить агент службы Mobility Service для аварийного восстановления виртуальных машин VMware и физических серверов в Azure с помощью службы Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: dfed0209131379843b97ff8050c2f2ba7294537d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019998"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>Ручная установка службы Mobility Service на виртуальных машинах VMware и физических серверах

При настройке аварийного восстановления для виртуальных машин VMware и физических серверов используйте [Azure Site Recovery](site-recovery-overview.md). Требуется установить [службу мобильности Site Recovery](vmware-physical-mobility-service-overview.md) на каждую локальную виртуальную машину VMware и физический сервер.  Служба Mobility Service фиксирует операции записи данных на компьютере и перенаправляет их на сервер обработки Site Recovery.

Статья содержит сведения о ручной установке службы Mobility Service на каждый компьютер, который требуется защитить.

## <a name="create-a-passphrase"></a>Создание парольной фразы

Перед началом установки необходимо создать парольную фразу, которая будет использоваться во время процесса установки.

1. Войдите на сервер конфигурации.
2. Запустите командную строку от имени администратора.
3. Откройте каталог bin и создайте файл с парольной фразой.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Сохраните файл парольной фразы в безопасном месте. 


## <a name="install-the-service-from-the-ui"></a>Установка службы с помощью пользовательского интерфейса

>[!IMPORTANT]
> Данный метод запрещается использовать при репликации виртуальных машин Azure IaaS из одного региона Azure в другой. Вместо него используйте метод установки, в котором задействуется командная строка.

1. Определите версию установщика, которая соответствует ОС компьютера. Установщики находятся в папке %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository. [Проверьте](vmware-physical-mobility-service-overview.md#installer-files), какой из установщиков вам требуется.
2. Скопируйте файл установки на компьютер и запустите его.
3. В колонке **параметров установки** выберите **Install Mobility Service** (Установить Mobility Service).
4. Выберите расположение установщика > **Установить**.

    ![Страница параметров установки Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. Ход состояния установки можно отслеживать в окне **Ход выполнения установки**. После завершения установки нажмите кнопку **Proceed to Configuration** (Перейти к конфигурации), чтобы зарегистрировать службу на сервере конфигурации.

    ![Страница регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  На странице **Сведения о сервере конфигурации** укажите IP-адрес сервера конфигурации и созданную парольную фразу.  

    ![Страница регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Выберите **Зарегистрировать**, чтобы завершить регистрацию.

    ![Страница завершения регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>Установка службы из командной строки

### <a name="on-a-windows-machine"></a>На компьютере с ОС Windows

1. Скопируйте установщик в локальную папку (например, C:\Temp) на сервере, который необходимо защитить. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Выполните установку следующим образом.

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. Зарегистрируйте агент на сервере конфигурации.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Параметры установки
**Параметр** | **Дополнительные сведения**
--- | ---
Использование | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform "VmWare" /Silent
журналы установки; | В разделе %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Параметр, обязательный для установки. Указывает, следует ли устанавливать службу Mobility Service или главный целевой сервер.
/InstallLocation| Необязательный параметр. Указывает расположение установки (любая папка).
/Platform | (Обязательный параметр.) Указывает платформу, на которой будет установлена служба Mobility Service. **VMware** используется для физических серверов или виртуальных машины VMware, а **Azure** используется для виртуальных машин Azure. 
/Silent| Необязательный элемент. Указывает, разрешен ли запуск установщика в автоматическом режиме.

#### <a name="registration-settings"></a>Параметры регистрации
**Параметр** | **Дополнительные сведения**
--- | ---
Использование | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Журналы конфигурации агента | Журналы находятся в папке %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Обязательный параметр. Указывает IP-адрес сервера конфигурации. Используйте любой допустимый IP-адрес.
/PassphraseFilePath |  (Обязательный параметр.) Расположение файла с парольной фразой. Используйте любой допустимый локальный путь к файлу или UNC.


### <a name="on-a-linux-machine"></a>На компьютере с ОС Linux

1. Скопируйте установщик в локальную папку (например, /tmp) на сервере, который необходимо защитить. Выполните следующие команды в окне терминала.
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Выполните установку следующим образом.

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. После завершения установки службу Mobility Service необходимо зарегистрировать на сервере конфигурации. Выполните следующую команду, чтобы зарегистрировать службу Mobility Service на сервере конфигурации.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```


#### <a name="installation-settings"></a>Параметры установки
**Параметр** | **Дополнительные сведения**
--- | ---
Использование | ./install -d <Install Location> -r <MS|MT> -v VmWare -q
-r | Параметр, обязательный для установки. Указывает, следует ли устанавливать службу Mobility Service или главный целевой сервер.
-d | Необязательный параметр. Указывает расположение установки службы Mobility Service: /usr/local/ASR.
-v | (Обязательный параметр.) Указывает платформу, на которой будет установлена служба Mobility Service. **VMware** используется для физических серверов или виртуальных машины VMware, а **Azure** используется для виртуальных машин Azure. 
-q | Необязательный элемент. Указывает, разрешен ли запуск установщика в автоматическом режиме.

#### <a name="registration-settings"></a>Параметры регистрации
**Параметр** | **Дополнительные сведения**
--- | ---
Использование | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Обязательный параметр. Указывает IP-адрес сервера конфигурации. Используйте любой допустимый IP-адрес.
-P |  (Обязательный параметр.) Полный путь к файлу, в котором хранится парольная фраза. Используйте любую допустимую папку

## <a name="next-steps"></a>Дополнительная информация
- [Настройка аварийного восстановления в Azure для локальных виртуальных машин VMware](vmware-azure-tutorial.md)
- [Настройка аварийного восстановления в Azure для локальных физических серверов](physical-azure-disaster-recovery.md)
