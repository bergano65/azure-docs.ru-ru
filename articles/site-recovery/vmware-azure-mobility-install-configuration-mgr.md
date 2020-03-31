---
title: Автоматизация службы мобильности для аварийного восстановления установки в Azure Восстановление сайта
description: Как автоматически установить службу мобильности для vMware /физического восстановления сервера с восстановлением сайта Azure.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252233"
---
# <a name="automate-mobility-service-installation"></a>Установка службы автоматизации мобильности

В этой статье описывается, как автоматизировать установку и обновления для агента службы мобильности в [Azure Site Recovery.](site-recovery-overview.md)

При развертывании восстановления сайта для аварийного восстановления предприимчивых VM VMware и физических серверов в Azure вы устанавливаете агент службы мобильности на каждой машине, которую вы хотите воспроизвести. Служба мобильности фиксирует записи данных на машине и направляет их на сервер процесса восстановления сайта для репликации. Службу мобильности можно развернуть несколькими способами:

- **Нажмите установку:** Пусть восстановление сайта установит агент службы мобильности при включании репликации для машины на портале Azure.
- **Ручная установка**: Установка службы мобильности вручную на каждой машине. [Узнайте больше](vmware-physical-mobility-service-overview.md) о нажатии и ручной установке.
- **Автоматизированное развертывание**: Автоматизировать установку с помощью средств развертывания программного обеспечения, таких как Microsoft Endpoint Configuration Manager, или сторонних инструментов, таких как JetPatch.

Автоматизированная установка и обновление обеспечивает решение, если:

- Организация не позволяет нажать установку на защищенных серверах.
- Политика вашей компании требует периодический обмен паролей. Необходимо указать пароль для установки push.
- Ваша политика безопасности не позволяет добавлять исключения брандмауэра для определенных машин.
- Вы выступаете в качестве хостинг-провайдера и не хотите предоставлять учетные данные машины клиента, которые необходимы для установки push-установки с помощью восстановления сайта.
- Необходимо одновременно масштабировать установки агентов на множество серверов.
- Вы хотите запланировать установки и обновления во время запланированных окон технического обслуживания.

## <a name="prerequisites"></a>Предварительные требования

Чтобы автоматизировать установку, вам нужны следующие элементы:

- Развернутое решение для установки программного обеспечения, такое как [Configuration Manager](/configmgr/) или [JetPatch.](https://jetpatch.com/microsoft-azure-site-recovery/)
- Предпосылки развертывания в [Azure](tutorial-prepare-azure.md) и [на местах](vmware-azure-tutorial-prepare-on-premises.md) для аварийного восстановления VMware или для [физического](physical-azure-disaster-recovery.md) аварийного восстановления сервера. Просмотрите [требования к поддержке](vmware-physical-azure-support-matrix.md) аварийного восстановления.

## <a name="prepare-for-automated-deployment"></a>Подготовка к автоматическому развертыванию

В следующей таблице кратко излагаются инструменты и процессы автоматизации развертывания службы мобильности.

**Инструмент** | **Подробно** | **Инструкции**
--- | --- | ---
**Диспетчер конфигураций** | 1. Убедитесь, что у вас есть [предпосылки, перечисленные](#prerequisites) выше на месте. <br/><br/> 2. Развертывание аварийного восстановления путем настройки исходной среды, включая загрузку файла OVA для развертывания сервера конфигурации сайта в качестве VMware VM с помощью шаблона OVF.<br/><br/> 3. Вы регистрируете сервер конфигурации в службе восстановления сайта, настраиваете целевую среду Azure и настраиваете политику репликации.<br/><br/> 4. Для автоматического развертывания службы мобильности вы создаете сетевой общий пакет, содержащий пароль сервера конфигурации и файлы установки Службы мобильности.<br/><br/> 5. Вы создаете пакет Configuration Manager, содержащий установку или обновления, и готовитесь к развертыванию службы мобильности.<br/><br/> 6. Затем можно включить репликацию в Azure для машин с установленной службой мобильности. | [Автоматизация с помощью менеджера конфигурации](#automate-with-configuration-manager)
**JetPatch** | 1. Убедитесь, что у вас есть [предпосылки, перечисленные](#prerequisites) выше на месте. <br/><br/> 2. Развертывание аварийного восстановления путем настройки исходной среды, включая загрузку и развертывание менеджера агента JetPatch для восстановления azure в среде восстановления сайта, используя шаблон OVF.<br/><br/> 3. Вы регистрируете сервер конфигурации с помощью восстановления сайта, настраиваете целевую среду Azure и настраиваете политику репликации.<br/><br/> 4. Для автоматического развертывания инициализация и завершение конфигурации JetPatch Agent Manager.<br/><br/> 5. В JetPatch можно создать политику восстановления сайта для автоматизации развертывания и обновления агента службы мобильности. <br/><br/> 6. Затем можно включить репликацию в Azure для машин с установленной службой мобильности. | [Автоматизация с JetPatch Агент менеджер](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Установка агента по устранению неполадок в JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Автоматизация с помощью менеджера конфигурации

### <a name="prepare-the-installation-files"></a>Подготовка файлов установки

1. Убедитесь, что у вас есть предпосылки на месте.
1. Создайте безопасную долю сетевого файла (SMB share), к которым можно получить доступ к машине, работая на сервере конфигурации.
1. В Configuration Manager [классифицировать серверы,](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) на которых требуется установить или обновить службу мобильности. Одна коллекция должна содержать все серверы Windows, другая - все серверы Linux.
1. В сети разделите папку:

   - Для установки на компьютерах Windows создайте папку под названием _MobSvcWindows._
   - Для установки на машинах Linux создайте папку под названием _MobSvcLinux._

1. Войдите на компьютер сервера конфигурации.
1. На серверной машине конфигурации откройте запрос административной команды.
1. Чтобы создать файл фразы, запустите эту команду:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Копируйте файл _MobSvc.passphrase_ в папку Windows и папку Linux.
1. Чтобы просмотреть папку, содержащую файлы установки, запустите эту команду:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Копирование этих файлов установки в общую сеть:

   - Для Windows скопируйте _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ на _MobSvcWindows._
   - Для Linux скопируйте следующие файлы _в MobSvcLinux:_
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Как описано в следующих процедурах, скопируйте код на папки Windows или Linux. Мы предполагаем, что:

   - IP-адрес сервера конфигурации `192.168.3.121`.
   - Защищенная доля сетевого файла `\\ContosoSecureFS\MobilityServiceInstallers`.

### <a name="copy-code-to-the-windows-folder"></a>Копирование кода в папку Windows

Копируйте следующий код:

- Сохранить код в папке _MobSvcWindows_ как _install.bat_.
- Замените `[CSIP]` заполнителей в этом скрипте фактическими значениями IP-адреса сервера конфигурации.
- Скрипт поддерживает новые установки агента службы мобильности и обновляет уже установленные агенты.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Копирование кода в папку Linux

Копируйте следующий код:

- Сохранить код в папке _MobSvcLinux_ _под install_linux.sh_.
- Замените `[CSIP]` заполнителей в этом скрипте фактическими значениями IP-адреса сервера конфигурации.
- Скрипт поддерживает новые установки агента службы мобильности и обновляет уже установленные агенты.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Создание пакета

1. Войдите на консоль Configuration Manager и перейдите к пакетам > **управления** > **приложениями библиотеки** **программного обеспечения.**
1. Пакеты для > **создания** **пакетов**с правой кнопкой мыши .
1. Предоставьте сведения о пакете, включая имя, описание, производителя, язык и версию.
1. Выберите **Этот пакет содержит исходные файлы.**
1. Нажмите **Просмотр**, и выберите общую часть сети и папку, которая содержит соответствующий установщик (_MobSvcWindows_ или _MobSvcLinux_). Затем выберите **Следующий**.

   ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. В **выберите тип программы, который вы хотите создать** страницу, выберите **Стандартная программа** > **Далее**.

   ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. В **указано информацию об этой стандартной** странице программы, укажите следующие значения:

    **Параметр** | **Значение Windows** | **Значение Linux**
    --- | --- | ---
    **Название** | Установка Microsoft Azure Mobility Service (Windows) | Установка службы мобильности Microsoft Azure (Linux).
    **Командная строка** | install.bat | ./install_linux.sh
    **Программа может запускаться** | Независимо от входа пользователя в систему | Независимо от входа пользователя в систему
    **Другие параметры** | Используйте настройки по умолчанию | Используйте настройки по умолчанию

   ![Снимок экрана с мастером создания пакета и программы](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. При **уточнении требований к этой стандартной программе**выполняйте следующие задачи:

   - Для компьютеров Windows выберите **эту программу можно запускать только на определенных платформах.** Затем выберите [поддерживаемые операционные системы Windows](vmware-physical-azure-support-matrix.md#replicated-machines) и выберите **Следующий.**
   - Для машин Linux выберите **Эту программу можно запустить на любой платформе.** Затем выберите **Следующий**.

1. Завершите работу мастера.

### <a name="deploy-the-package"></a>Развертывание пакета

1. В консоли Configuration Manager щелкните пакет и выберите **Distribute Content.**

   ![Снимок экрана с консолью Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Выберите Точки распространения, на которые следует скопировать эти пакеты. Ознакомьтесь с [дополнительными сведениями](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Завершите работу мастера. Теперь пакет будет реплицироваться на выбранные точки распространения.
1. После завершения дистрибуции пакета нажмите на пакет > **Deploy.**

   ![Снимок экрана с консолью Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Выберите созданную ранее коллекцию устройств Windows или Linux.
1. На странице **«Указать направление содержимого»** выберите **Баллы распределения.**
1. В настройках указания для управления тем, **как это программное обеспечение развертывается** на странице, установите **Цель** **требуемого**.

   ![Снимок экрана с мастером развертывания программного обеспечения](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. При **уточнении расписания развертывания**назначайте расписание. Ознакомьтесь с [дополнительными сведениями](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Служба мобильности устанавливается в соответствии с графиком, который вы указываете.
   - Чтобы избежать лишних перезагрузок, запланируйте установку пакета на период ежемесячного обслуживания или обновления программного обеспечения.

1. На странице **пунктов распределения** настройте настройки и закончите мастер.
1. Мониторинг хода развертывания в консоли Configuration Manager. Перейдите к **мониторингу** > **развертывания** > _\<вашего\>названия пакета._

### <a name="uninstall-the-mobility-service"></a>Удаление службы мобильности

Можно создать пакеты Configuration Manager для удаления службы мобильности. Например, следующий скрипт удаляет службу мобильности:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Дальнейшие действия

[Включить репликацию](vmware-azure-enable-replication.md) для VMs.
