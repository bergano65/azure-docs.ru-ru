---
title: О службе мобильности для аварийного восстановления VMware VMs и физических серверов с Azure Восстановление сайта (ru) Документы Майкрософт
description: Узнайте об агенте службы мобильности для аварийного восстановления VMwи и физических серверах Azure с помощью службы восстановления сайта Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259814"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Сведения о службе Mobility Service на виртуальных машинах и физических серверах VMware

При настройке аварийного восстановления виртуальных машин VMware (VM) и физических серверов с использованием [Azure Site Recovery](site-recovery-overview.md)вы устанавливаете службу восстановления сайта на каждом наместере VMware VM и физическом сервере. Служба Mobility Service фиксирует операции записи данных на компьютере и перенаправляет их на сервер обработки Site Recovery. Служба мобильности устанавливается программным обеспечением агента службы мобильности, которое можно развернуть следующими методами:

- [Нажмите установку:](#push-installation)Когда защита включена через портал Azure, восстановление сайта устанавливает службу мобильности на сервере.
- Ручная установка: Вы можете установить службу мобильности вручную на каждой машине через [пользовательский интерфейс (UI)](#install-the-mobility-service-using-ui) или [запрос команды.](#install-the-mobility-service-using-command-prompt)
- [Автоматизированное развертывание](vmware-azure-mobility-install-configuration-mgr.md): Вы можете автоматизировать установку службы мобильности с помощью средств развертывания программного обеспечения, таких как Configuration Manager.

> [!NOTE]
> Служба мобильности использует примерно 6%-10% памяти на исходных машинах для VMware VMs или физических машинах.

## <a name="antivirus-on-replicated-machines"></a>Антивирус на реплицированных машинах

Если машины, которые вы хотите воспроизвести, работают с антивирусным программным обеспечением, исключите папку установки службы Mobility _C: «ProgramData-ASR-агент»_ из антивирусных операций. Это исключение гарантирует, что репликация будет работать в том виде, в каком ожидалось.

## <a name="push-installation"></a>Нажмите установку

Установка Push является неотъемлемой частью задания, которое работает с портала Azure для [включения репликации.](vmware-azure-enable-replication.md#enable-replication) После выбора набора VMs, который вы хотите защитить и включить репликацию, сервер конфигурации толкает агента службы мобильности на серверы, устанавливает агента и завершает регистрацию агента с сервером конфигурации.

### <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что все [предпосылки установки push выполнены.](vmware-azure-install-mobility-service.md)
- Убедитесь, что все конфигурации серверов соответствуют критериям [в матрице поддержки для аварийного восстановления VMw и физических серверов в Azure.](vmware-physical-azure-support-matrix.md)

Рабочий процесс установки push описан в следующих разделах:

### <a name="mobility-service-agent-version-923-and-higher"></a>Версия агента мобильного обслуживания 9.23 и выше

Для получения дополнительной информации о версии 9.23 [см. Обновление Rollup 35 для восстановления сайта Azure](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Во время установки push-сервиса Mobility выполняются следующие шаги:

1. Агент нажат на исходную машину. Копирование агента на исходную машину может выйти из строя из-за многочисленных ошибок в окружающей среде. Посетите [наше руководство](vmware-azure-troubleshoot-push-install.md) для устранения неполадок push-установки.
1. После успешного копирования агента на сервер на сервере выполняется проверка предпосылки.
   - Если все предпосылки выполнены, начинается установка.
   - Установка завершается неудачей, если одна или несколько [предпосылок не выполнены.](vmware-physical-azure-support-matrix.md)
1. В рамках установки агента установлен поставщик службы копирования теней (VSS) для восстановления сайта Azure. Поставщик VSS используется для генерации согласованных точек восстановления приложений. Если установка поставщика VSS не удается, этот шаг пропускается и установка агента продолжается.
1. Если установка агента удается, но установка поставщика VSS не удается, то статус задания помечается как **Предупреждение.** Это не влияет на генерацию точки восстановления аварийно-последовательного восстановления.

    - Для создания согласованных точек восстановления приложений обратитесь к [нашим рекомендациям](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) для завершения ручной установки поставщика VSS восстановления сайта.
    - Если вы не хотите создавать согласованные точки восстановления приложения, [измените политику репликации,](vmware-azure-set-up-replication.md#create-a-policy) чтобы отключить согласованные точки восстановления приложения.

### <a name="mobility-service-agent-version-922-and-below"></a>Версия агента мобильного обслуживания 9.22 и ниже

1. Агент нажат на исходную машину. Копирование агента на исходную машину может выйти из строя из-за многочисленных ошибок в окружающей среде. Обратитесь к [нашему руководству,](vmware-azure-troubleshoot-push-install.md) чтобы устранить неполадки push установки.
1. После успешного копирования агента на сервер на сервере выполняется проверка предпосылки.
   - Если все предпосылки выполнены, начинается установка.
   - Установка завершается неудачей, если одна или несколько [предпосылок не выполнены.](vmware-physical-azure-support-matrix.md)

1. В рамках установки агента установлен поставщик службы копирования теней (VSS) для восстановления сайта Azure. Поставщик VSS используется для генерации согласованных точек восстановления приложений.
   - Если установка поставщика VSS не удается, установка агента не сработает. Чтобы избежать сбоя установки агента, используйте [версию 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) или выше для генерации аварийных пунктов восстановления и сделайте ручную установку поставщика VSS.

## <a name="install-the-mobility-service-using-ui"></a>Установка службы мобильности с помощью uI

### <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что все конфигурации серверов соответствуют критериям [в матрице поддержки для аварийного восстановления VMw и физических серверов в Azure.](vmware-physical-azure-support-matrix.md)
- [Найдите установщик](#locate-installer-files) для операционной системы сервера.

>[!IMPORTANT]
> Не используйте метод установки uI, если вы воспроизводите инфраструктуру Azure как VM службы (IaaS) из одного региона Azure в другой. Используйте [командную оперативную](#install-the-mobility-service-using-command-prompt) установку.

1. Скопируйте файл установки на компьютер и запустите его.
1. В колонке **параметров установки** выберите **Install Mobility Service** (Установить Mobility Service).
1. Выберите место установки и выберите **Install.**

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Страница опции установки услуг мобильности.":::

1. Ход состояния установки можно отслеживать в окне **Ход выполнения установки**. После завершения установки нажмите кнопку **Proceed to Configuration** (Перейти к конфигурации), чтобы зарегистрировать службу на сервере конфигурации.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Страница регистрации услуг мобильности.":::

1. В **настройке сервера Детали указаны**IP-адрес и пароль, который вы настроены.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Страница регистрации услуг мобильности.":::

1. Выберите **Зарегистрировать**, чтобы завершить регистрацию.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Регистрация мобильной связи на последней странице.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Установка службы мобильности с помощью запроса команды

### <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что все конфигурации серверов соответствуют критериям [в матрице поддержки для аварийного восстановления VMw и физических серверов в Azure.](vmware-physical-azure-support-matrix.md)
- [Найдите установщик](#locate-installer-files) для операционной системы сервера.

### <a name="windows-machine"></a>Машина Windows

- От запроса команд запустите следующие команды для копирования установки в локальную папку, такую как _C:Temp_, на сервере, который вы хотите защитить. Замените имя файла установщика фактическим именем файла.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Запустите эту команду для установки агента.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Запустите эти команды для регистрации агента с сервером конфигурации.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Параметры установки

Параметр | Сведения
--- | ---
Синтаксис | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
журналы установки; | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Параметр, обязательный для установки. Указывает, следует ли устанавливать службу Mobility Service или главный целевой сервер.
`/InstallLocation`| Необязательный параметр. Указывает расположение установки (любая папка).
`/Platform` | Mandatory. Определяет платформу, на которой установлена услуга Mobility: <br/> **VMware** для VMware VMs/физических серверов. <br/> **Azure** для VM Azure.<br/><br/> Если вы рассматриваете VMs Azure как физические машины, укажите **VMware.**
`/Silent`| Необязательный параметр. Указывает, разрешен ли запуск установщика в автоматическом режиме.

#### <a name="registration-settings"></a>Параметры регистрации

Параметр | Сведения
--- | ---
Синтаксис | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Журналы конфигурации агента | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Обязательный параметр. `<CSIP>`определяет IP-адрес сервера конфигурации. Используйте любой допустимый IP-адрес.
`/PassphraseFilePath` |  Mandatory. Расположение файла с парольной фразой. Используйте любой допустимый локальный путь к файлу или UNC.

### <a name="linux-machine"></a>Linux машина

1. От сеанса терминала скопируйте установщика в локальную папку, такую как _/tmp_ на сервере, который вы хотите защитить. Замените имя файла установщика фактическим именем дистрибутива Linux, а затем запустите команды.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Выполните установку следующим образом.

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. После завершения установки служба Mobility должна быть зарегистрирована на сервер конфигурации. Запустите следующую команду для регистрации службы Mobility с сервером конфигурации.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Параметры установки

Параметр | Сведения
--- | ---
Синтаксис | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Параметр, обязательный для установки. Указывает, следует ли устанавливать службу Mobility Service или главный целевой сервер.
`-d` | Необязательный параметр. Определяет местоположение установки службы `/usr/local/ASR`мобильности: .
`-v` | Mandatory. Определяет платформу, на которой установлена услуга Mobility. <br/> **VMware** для VMware VMs/физических серверов. <br/> **Azure** для VM Azure.
`-q` | Необязательный параметр. Указывает, разрешен ли запуск установщика в автоматическом режиме.

#### <a name="registration-settings"></a>Параметры регистрации

Параметр | Сведения
--- | ---
Синтаксис | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Обязательный параметр. `<CSIP>`определяет IP-адрес сервера конфигурации. Используйте любой допустимый IP-адрес.
`-P` |  Mandatory. Полный путь к файлу, в котором хранится парольная фраза. Используйте любую допустимую папку.

## <a name="azure-virtual-machine-agent"></a>Агент виртуальной машины Azure

- **Виртуальные машины Windows**. Начиная с версии 9.7.0.0 Mobility Service, [агент виртуальной машины Azure](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) устанавливает установщик Mobility Service. Это гарантирует, что при сбое в управление машины Azure VM соответствует предпосылке установки агента для использования любого расширения VM.
- **Виртуальные машины Linux**. [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) следует установить вручную на виртуальной машине Azure после отработки отказа.

## <a name="locate-installer-files"></a>Найдите файлы установки

На сервере конфигурации перейдите в папку _%ProgramData% -ASR-home-svsystems-pushinstallsvc-repository_. Проверьте, какой установщик вам нужен на основе операционной системы. В следующей таблице кратко излагаются файлы установки для каждого VMware VM и операционная система физического сервера. Перед тем, как начать, вы можете просмотреть [поддерживаемые операционные системы.](vmware-physical-azure-support-matrix.md#replicated-machines)

> [!NOTE]
> Имена файлов используют синтаксис, показанный в следующей таблице с _версией_ и _датой,_ в качестве заполнителей для реальных значений. Фактические имена файлов будут выглядеть так же, как и в следующих примерах:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Файл установщика | Операционная система (только 64-разрядная версия)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 с пакетом обновления 1 (SP1)
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Включает в себя SP2 и SP3.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Предприятие Linux 6.4 </br> Oracle Предприятие Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Сервер Ubuntu Linux 16.04 LTS
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Дальнейшие действия

[Установка службы Mobility Service для аварийного восстановления виртуальных машин VMware и физических серверов](vmware-azure-install-mobility-service.md)
