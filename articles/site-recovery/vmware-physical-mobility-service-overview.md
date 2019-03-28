---
title: Сведения о службе Mobility Service для аварийного восстановления виртуальных машин и физических серверов VMware с помощью Azure Site Recovery | Документация Майкрософт
description: Узнайте об агенте службы Mobility Service для аварийного восстановления виртуальных машин и физических серверов VMware в Azure с помощью службы Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 523567a0db79e54bea1ed6ff23557c7fa29c74f6
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541117"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Сведения о службе Mobility Service на виртуальных машинах и физических серверах VMware

При настройке аварийного восстановления для виртуальных машин VMware и физических серверов используйте [Azure Site Recovery](site-recovery-overview.md). Требуется установить службу мобильности Site Recovery на каждую локальную виртуальную машину VMware и физический сервер.  Служба Mobility Service фиксирует операции записи данных на компьютере и перенаправляет их на сервер обработки Site Recovery. Вы можете запустить службу Mobility Service одним из следующих способов:

- [Принудительная установка](#push-installation). Site Recovery устанавливает агент мобильности на сервере, при включении защиты с помощью портала Azure.
- Установите вручную. Можно установить службу Mobility service вручную на каждом компьютере с помощью [пользовательского интерфейса](#install-mobility-agent-through-ui) или [командной](#install-mobility-agent-through-command-prompt).
- [Автоматизированное развертывание](vmware-azure-mobility-install-configuration-mgr.md). Установку можно автоматизировать с помощью инструментов развертывания программного обеспечения, таких как System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Антивирусное ПО на реплицированных компьютерах

Если на компьютерах, которые нужно реплицировать, запущено антивирусное ПО, обязательно исключите установочную папку службы Mobility в список исключений из антивирусной проверки (*C:\ProgramData\ASR\agent*). Это обеспечит правильную работу репликации.

## <a name="push-installation"></a>Принудительная установка

Принудительная установка является неотъемлемой частью "[включения репликации](vmware-azure-enable-replication.md#enable-replication)" Задание активации на портале. После выбора набор виртуальных машин, которые вы хотите защитить и активировать «Включить репликацию», сервер конфигурации Push-уведомлений агента mobility на серверы, устанавливает агент и завершить регистрацию агента с сервером конфигурации. Для успешного завершения этой операции

- Убедитесь, что все Принудительная установка [предварительные требования](vmware-azure-install-mobility-service.md) соблюдены.
- Убедитесь, что все конфигурации серверов не попадает под [Матрица поддержки VMware к сценарию аварийного восстановления Azure](vmware-physical-azure-support-matrix.md).

Сведения о принудительной установки рабочего процесса описан в следующих разделах.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Из [9.23 версии](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) и более поздних версий

Во время принудительной установки агента мобильности выполняются следующие шаги

1. Агент Push-уведомлений на исходном компьютере. Копирование агента на исходном компьютере может завершиться ошибкой из-за нескольких ошибок окружающей среды. Посетите [наше руководство](vmware-azure-troubleshoot-push-install.md) принудительной Устранение неполадок при установке.
2. После успешного копирования агента на для проверки готовности сервера выполняются на сервере. Установка завершается сбоем, если один или несколько из [предварительные требования](vmware-physical-azure-support-matrix.md) не выполняются. Если все условия соблюдены, установка активируется.
3. Поставщик Azure Site Recovery VSS устанавливается на сервере как часть установки агента мобильности. Этот поставщик используется для создания согласованного точки приложения. Если не удастся установить поставщик VSS, этот шаг будет пропущен и продолжит установку агента.
4. Если установка агента выполнена успешно, но не удается установить поставщик VSS, состояние задания помечается как «Warning». Это не влияет на создание точки согласованности аварийного завершения.

    a. Создавать согласованные точки приложения, см. в статье [наше руководство](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) для завершения установки поставщика Site Recovery VSS вручную. </br>
    2.  Если вы не желаете согласованные точки приложения будет создан, [изменить политику репликации](vmware-azure-set-up-replication.md#create-a-policy) отключение согласованные точки приложения.

### <a name="before-922-versions"></a>Прежде чем 9.22 версий

1. Агент Push-уведомлений на исходном компьютере. Копирование агента на исходном компьютере может завершиться ошибкой из-за нескольких ошибок окружающей среды. Посетите [наше руководство](vmware-azure-troubleshoot-push-install.md) принудительной Устранение неполадок при установке.
2. После успешного копирования агента на для проверки готовности сервера выполняются на сервере. Установка завершается сбоем, если один или несколько из [предварительные требования](vmware-physical-azure-support-matrix.md) не выполняются. Если все условия соблюдены, установка активируется.
3. Поставщик Azure Site Recovery VSS устанавливается на сервере как часть установки агента мобильности. Этот поставщик используется для создания согласованного точки приложения. Если не удастся установить поставщик VSS, установка агента завершится ошибкой. Чтобы избежать сбоя установки агента мобильности, используйте [9.23 версии](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) или более поздней версии для создания отказоустойчивой точек и установить поставщик VSS вручную.

## <a name="install-mobility-agent-through-ui"></a>Установка агента mobility через пользовательский Интерфейс

### <a name="prerequisite"></a>Предварительные требования

- Убедитесь, что все конфигурации серверов не попадает под [Матрица поддержки VMware к сценарию аварийного восстановления Azure](vmware-physical-azure-support-matrix.md).
- [Найдите установщик](#locate-installer-files) зависимости от операционной системы сервера.

>[!IMPORTANT]
> Если при репликации виртуальных Машин IaaS Azure из одного региона Azure в другой, не используйте этот метод. Вместо этого используйте метод установки с помощью командной строки.

1. Скопируйте файл установки на компьютер и запустите его.
2. В колонке **параметров установки** выберите **Install Mobility Service** (Установить Mobility Service).
3. Выберите расположение установщика > **Установить**.

    ![Страница параметров установки Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Ход состояния установки можно отслеживать в окне **Ход выполнения установки**. После завершения установки нажмите кнопку **Proceed to Configuration** (Перейти к конфигурации), чтобы зарегистрировать службу на сервере конфигурации.

    ![Страница регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. в **сведения о сервере конфигурации**, укажите IP-адрес и парольную фразу, вы настроили.  

    ![Страница регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Выберите **Зарегистрировать**, чтобы завершить регистрацию.

    ![Страница завершения регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Установка агента mobility с помощью командной строки

### <a name="prerequisite"></a>Предварительные требования

- Убедитесь, что все конфигурации серверов не попадает под [Матрица поддержки VMware к сценарию аварийного восстановления Azure](vmware-physical-azure-support-matrix.md).
- [Найдите установщик](#locate-installer-files) зависимости от операционной системы сервера.

### <a name="on-a-windows-machine"></a>На компьютере с Windows

- Скопируйте установщик в локальную папку (например, C:\Temp) на сервере, который необходимо защитить.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Выполните установку следующим образом.

    ``` 
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Зарегистрируйте агент на сервере конфигурации.

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
/Platform | (Обязательный параметр.) Указывает платформу, на которой будет установлена служба Mobility Service. **VMware** используется для физических серверов или виртуальных машин VMware, а **Azure** — для виртуальных машин Azure. 
/Silent| Необязательный элемент. Указывает, разрешен ли запуск установщика в автоматическом режиме.

#### <a name="registration-settings"></a>Параметры регистрации
**Параметр** | **Дополнительные сведения**
--- | ---
Использование | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP > /PassphraseFilePath \<PassphraseFilePath >
Журналы конфигурации агента | Журналы находятся в папке %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Обязательный параметр. Указывает IP-адрес сервера конфигурации. Используйте любой допустимый IP-адрес.
/PassphraseFilePath |  (Обязательный параметр.) Расположение файла с парольной фразой. Используйте любой допустимый локальный путь к файлу или UNC.

### <a name="on-a-linux-machine"></a>На компьютере с Linux

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
Использование | . / install -d \<Расположение_установки > - r < MS|MT> -v VmWare -q
-r | Параметр, обязательный для установки. Указывает, следует ли устанавливать службу Mobility Service или главный целевой сервер.
-d | Необязательный параметр. Указывает расположение установки службы Mobility Service: /usr/local/ASR.
-v | (Обязательный параметр.) Указывает платформу, на которой будет установлена служба Mobility Service. **VMware** используется для физических серверов или виртуальных машин VMware, а **Azure** — для виртуальных машин Azure. 
-q | Необязательный элемент. Указывает, разрешен ли запуск установщика в автоматическом режиме.

#### <a name="registration-settings"></a>Параметры регистрации
**Параметр** | **Дополнительные сведения**
--- | ---
Использование | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | Обязательный параметр. Указывает IP-адрес сервера конфигурации. Используйте любой допустимый IP-адрес.
-P |  (Обязательный параметр.) Полный путь к файлу, в котором хранится парольная фраза. Используйте любую допустимую папку.

## <a name="azure-virtual-machine-agent"></a>Агент виртуальной машины Azure

- **Для виртуальных машин Windows**: Начиная с версии 9.7.0.0 Mobility Service, [агент виртуальной машины Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) устанавливает установщик Mobility Service. Это гарантирует, что когда выполняется отработка отказа устройства в Azure, виртуальная машина Azure отвечает требованиям к установке агента для использования любого расширения виртуальной машины.
- **Для виртуальных машин Linux**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) следует установить вручную на виртуальной машине Azure после отработки отказа.

## <a name="locate-installer-files"></a>Найдите файлы установщика

Перейдите в папку %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository на сервере конфигурации. Проверьте, какие установщика, вам потребуется, в зависимости от операционной системы. В следующей таблице перечислены файлы установщика для каждой виртуальной Машины VMware и физических серверов операционной системы. Вы можете просмотреть [поддерживаемые операционные системы](vmware-physical-azure-support-matrix.md#replicated-machines), прежде чем начать.

**Файл установщика** | **Операционная система (только 64-разрядная версия)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 с пакетом обновления 1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 (с пакетом обновления 1, 2 и 3) 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Сервер Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8;

## <a name="next-steps"></a>Дальнейшие действия

[Установка службы Mobility Service для аварийного восстановления виртуальных машин VMware и физических серверов](vmware-azure-install-mobility-service.md)
