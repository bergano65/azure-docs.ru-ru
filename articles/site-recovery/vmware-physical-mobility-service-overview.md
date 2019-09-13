---
title: Сведения о службе Mobility Service для аварийного восстановления виртуальных машин и физических серверов VMware с помощью Azure Site Recovery | Документация Майкрософт
description: Узнайте об агенте службы Mobility Service для аварийного восстановления виртуальных машин и физических серверов VMware в Azure с помощью службы Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: aeb00b84ac254232e0d68fd9631fb539a928e67d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931892"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Сведения о службе Mobility Service на виртуальных машинах и физических серверах VMware

При настройке аварийного восстановления для виртуальных машин VMware и физических серверов используйте [Azure Site Recovery](site-recovery-overview.md). Требуется установить службу мобильности Site Recovery на каждую локальную виртуальную машину VMware и физический сервер.  Служба Mobility Service фиксирует операции записи данных на компьютере и перенаправляет их на сервер обработки Site Recovery. Вы можете запустить службу Mobility Service одним из следующих способов:

- [Принудительная установка](#push-installation). Site Recovery устанавливает агент мобильности на сервере при включении защиты с помощью портал Azure.
- Установить вручную: Службу Mobility Service можно установить вручную на каждом компьютере с помощью [пользовательского интерфейса](#install-mobility-agent-through-ui) или [командной строки](#install-mobility-agent-through-command-prompt).
- [Автоматизированное развертывание](vmware-azure-mobility-install-configuration-mgr.md). Установку можно автоматизировать с помощью инструментов развертывания программного обеспечения, таких как System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Антивирусное ПО на реплицированных компьютерах

Если на компьютерах, которые нужно реплицировать, запущено антивирусное ПО, обязательно исключите установочную папку службы Mobility в список исключений из антивирусной проверки (*C:\ProgramData\ASR\agent*). Это обеспечит правильную работу репликации.

## <a name="push-installation"></a>Принудительная установка

Принудительная установка — это неотъемлемая часть задания "[Включение репликации](vmware-azure-enable-replication.md#enable-replication)", активируемого на портале. Выбрав набор виртуальных машин, которые необходимо защитить, и активируйте параметр "включить репликацию", сервер конфигурации отправляет на серверы агент мобильности, устанавливает агент и завершает регистрацию агента с помощью сервера конфигурации. Для успешного выполнения этой операции

- Убедитесь, что выполнены все [необходимые условия](vmware-azure-install-mobility-service.md) для принудительной установки.
- Убедитесь, что все конфигурации серверов находятся в разделе [Поддержка матрицы VMware в сценарии аварийного восстановления Azure](vmware-physical-azure-support-matrix.md).

Сведения о рабочем процессе принудительной установки описаны в следующих разделах.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Начиная с [версии 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)

Во время принудительной установки агента Mobility Agent выполняются следующие действия.

1. Отправляет агент на исходный компьютер. Копирование агента на исходный компьютер может завершиться сбоем из-за нескольких ошибок среды. Ознакомьтесь с [нашим руководством](vmware-azure-troubleshoot-push-install.md) по устранению сбоев при принудительной установке.
2. После успешного копирования агента на на сервер выполняются проверки необходимых компонентов. Установка завершается сбоем, если не выполнены некоторые [Предварительные требования](vmware-physical-azure-support-matrix.md) . Если все предварительные условия выполнены, установка будет активирована.
3. Azure Site Recovery поставщик VSS устанавливается на сервере в ходе установки агента мобильности. Этот поставщик используется для создания точек, совместимых с приложениями. Если при установке поставщика VSS произойдет сбой, этот шаг будет пропущен и установка агента будет продолжена.
4. Если установка агента завершилась успешно, но установка поставщика VSS завершается сбоем, то состояние задания помечается как "предупреждение". Это не влияет на создание точек согласованности.

    1\. Чтобы создать точки, связанные с приложениями, см. [рекомендации](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) по завершению установки Site Recovery поставщика VSS вручную. </br>
    2\.  Если вы не хотите создавать точки с единообразным применением, [измените политику репликации](vmware-azure-set-up-replication.md#create-a-policy) , чтобы отключить точки, учитывающие состояние приложений.

### <a name="before-922-versions"></a>До 9,22 версий

1. Отправляет агент на исходный компьютер. Копирование агента на исходный компьютер может завершиться сбоем из-за нескольких ошибок среды. Ознакомьтесь с [нашим руководством](vmware-azure-troubleshoot-push-install.md) по устранению сбоев при принудительной установке.
2. После успешного копирования агента на на сервер выполняются проверки необходимых компонентов. Установка завершается сбоем, если не выполнены некоторые [Предварительные требования](vmware-physical-azure-support-matrix.md) . Если все предварительные условия выполнены, установка будет активирована.
3. Azure Site Recovery поставщик VSS устанавливается на сервере в ходе установки агента мобильности. Этот поставщик используется для создания точек, совместимых с приложениями. В случае сбоя установки поставщика VSS Установка агента завершится ошибкой. Чтобы избежать сбоя установки агента Mobility Service, используйте [версию 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) или более позднюю, чтобы создать точки с ошибками и установить поставщик VSS вручную.

## <a name="install-mobility-agent-through-ui"></a>Установка агента мобильности с помощью пользовательского интерфейса

### <a name="prerequisite"></a>Предварительное требование

- Убедитесь, что все конфигурации серверов находятся в разделе [Поддержка матрицы VMware в сценарии аварийного восстановления Azure](vmware-physical-azure-support-matrix.md).
- [Нахождение установщика](#locate-installer-files) на основе операционной системы сервера.

>[!IMPORTANT]
> Если вы реплицируете виртуальную машину Azure IaaS из одного региона Azure в другой, не используйте этот метод. Используйте вместо этого метод установки на основе командной строки.

1. Скопируйте файл установки на компьютер и запустите его.
2. В колонке **параметров установки** выберите **Install Mobility Service** (Установить Mobility Service).
3. Выберите расположение установщика > **Установить**.

    ![Страница параметров установки Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Ход состояния установки можно отслеживать в окне **Ход выполнения установки**. После завершения установки нажмите кнопку **Proceed to Configuration** (Перейти к конфигурации), чтобы зарегистрировать службу на сервере конфигурации.

    ![Страница регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. На странице **сведения о сервере конфигурации**укажите НАСТРОЕННЫЙ IP-адрес и парольную фразу.  

    ![Страница регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Выберите **Зарегистрировать**, чтобы завершить регистрацию.

    ![Страница завершения регистрации Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Установка агента мобильности с помощью командной строки

### <a name="prerequisite"></a>Предварительное требование

- Убедитесь, что все конфигурации серверов находятся в разделе [Поддержка матрицы VMware в сценарии аварийного восстановления Azure](vmware-physical-azure-support-matrix.md).
- [Нахождение установщика](#locate-installer-files) на основе операционной системы сервера.

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
**Параметр** | **Сведения**
--- | ---
Использование | UnifiedAgent. exe/Role \<MS/MT >/InstallLocation \<расположение установки >/Platform "VMware"/Silent
журналы установки; | В разделе %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Параметр, обязательный для установки. Указывает, следует ли устанавливать службу Mobility Service или главный целевой сервер.
/InstallLocation| Необязательный параметр. Указывает расположение установки (любая папка).
/Platform | (Обязательный параметр.) Указывает платформу, на которой будет установлена служба Mobility Service. **VMware** используется для физических серверов или виртуальных машин VMware, а **Azure** — для виртуальных машин Azure.<br/><br/> Если виртуальные машины Azure разрабатывается как физические компьютеры, укажите **VMware**.
/Silent| Необязательный элемент. Указывает, разрешен ли запуск установщика в автоматическом режиме.

#### <a name="registration-settings"></a>Параметры регистрации
**Параметр** | **Сведения**
--- | ---
Использование | Унифиедажентконфигуратор. exe/ксендпоинт \<CSIP >/PassphraseFilePath \<пассфрасефилепас >
Журналы конфигурации агента | Журналы находятся в папке %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Обязательный параметр. Указывает IP-адрес сервера конфигурации. Используйте любой допустимый IP-адрес.
/PassphraseFilePath |  (Обязательный параметр.) Расположение файла с парольной фразой. Используйте любой допустимый локальный путь к файлу или UNC.

### <a name="on-a-linux-machine"></a>На компьютере с Linux

1. Скопируйте установщик в локальную папку (например, /tmp) на сервере, который необходимо защитить. Выполните следующие команды в окне терминала.

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
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
**Параметр** | **Сведения**
--- | ---
Использование | ./Install-d \<расположение установки >-r \<MS/MT >-v VMware-q
-r | Параметр, обязательный для установки. Указывает, следует ли устанавливать службу Mobility Service или главный целевой сервер.
-d | Необязательный параметр. Указывает расположение установки службы Mobility Service: /usr/local/ASR.
-v | (Обязательный параметр.) Указывает платформу, на которой будет установлена служба Mobility Service. **VMware** используется для физических серверов или виртуальных машин VMware, а **Azure** — для виртуальных машин Azure.
-q | Необязательный элемент. Указывает, разрешен ли запуск установщика в автоматическом режиме.

#### <a name="registration-settings"></a>Параметры регистрации
**Параметр** | **Сведения**
--- | ---
Использование | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh-i \<CSIP >-P \<пассфрасефилепас >
-i | Обязательный параметр. Указывает IP-адрес сервера конфигурации. Используйте любой допустимый IP-адрес.
-P |  (Обязательный параметр.) Полный путь к файлу, в котором хранится парольная фраза. Используйте любую допустимую папку.

## <a name="azure-virtual-machine-agent"></a>Агент виртуальной машины Azure

- **Для виртуальных машин Windows**: Начиная с версии 9.7.0.0 Mobility Service, [агент виртуальной машины Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) устанавливает установщик Mobility Service. Это гарантирует, что когда выполняется отработка отказа устройства в Azure, виртуальная машина Azure отвечает требованиям к установке агента для использования любого расширения виртуальной машины.
- **Для виртуальных машин Linux**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) следует установить вручную на виртуальной машине Azure после отработки отказа.

## <a name="locate-installer-files"></a>Обнаружение файлов установщика

Перейдите в папку%Програмдата%\аср\хоме\свсистемс\пушинсталлсвк\репоситори на сервере конфигурации. Проверьте, какой установщик требуется в зависимости от операционной системы. В следующей таблице перечислены файлы установщика для каждой виртуальной машины VMware и физической серверной операционной системы. Вы можете просмотреть [поддерживаемые операционные системы](vmware-physical-azure-support-matrix.md#replicated-machines), прежде чем начать.

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

## <a name="next-steps"></a>Следующие шаги

[Установка службы Mobility Service для аварийного восстановления виртуальных машин VMware и физических серверов](vmware-azure-install-mobility-service.md)
