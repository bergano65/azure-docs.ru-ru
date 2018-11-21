---
title: Сведения о службе Mobility Service для аварийного восстановления виртуальных машин и физических серверов VMware с помощью Azure Site Recovery | Документация Майкрософт
description: Узнайте об агенте службы Mobility Service для аварийного восстановления виртуальных машин и физических серверов VMware в Azure с помощью службы Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: a3e695e8c238dd4bc333d5cd477e70b93231399b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51565026"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Сведения о службе Mobility Service на виртуальных машинах и физических серверах VMware

При настройке аварийного восстановления для виртуальных машин VMware и физических серверов используйте [Azure Site Recovery](site-recovery-overview.md). Требуется установить службу мобильности Site Recovery на каждую локальную виртуальную машину VMware и физический сервер.  Служба Mobility Service фиксирует операции записи данных на компьютере и перенаправляет их на сервер обработки Site Recovery. Вы можете запустить службу Mobility Service одним из следующих способов:

[Принудительная установка](vmware-azure-install-mobility-service.md). Настройте в Site Recovery выполнение принудительной установки Mobility Service. Для этого при настройке аварийного восстановления также настройте учетную запись, которую сервер обработки Site Recovery может использовать для доступа к виртуальной машине или физическому серверу для целей установки службы.
[Установка вручную](vmware-physical-mobility-service-install-manual.md). Mobility Service можно установить вручную на каждом компьютере с помощью пользовательского интерфейса или окна командной строки.
[Автоматизированное развертывание](vmware-azure-mobility-install-configuration-mgr.md). Установку можно автоматизировать с помощью инструментов развертывания программного обеспечения, таких как System Center Configuration Manager.

## <a name="azure-virtual-machine-agent"></a>Агент виртуальной машины Azure

- **Виртуальные машины Windows**. Начиная с версии 9.7.0.0 Mobility Service, [агент виртуальной машины Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) устанавливает установщик Mobility Service. Это гарантирует, что когда выполняется отработка отказа устройства в Azure, виртуальная машина Azure отвечает требованиям к установке агента для использования любого расширения виртуальной машины.
- **Виртуальные машины Linux**. [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) следует установить вручную на виртуальной машине Azure после отработки отказа.

## <a name="installer-files"></a>Файлы установщика

В таблице перечислены файлы установщика для каждой операционной системы виртуальной машины и физического сервера VMware. Вы можете просмотреть [поддерживаемые операционные системы](vmware-physical-azure-support-matrix.md#replicated-machines), прежде чем начать.


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

## <a name="anti-virus-on-replicated-machines"></a>Антивирусное ПО на реплицированных компьютерах

Если на компьютерах, которые нужно реплицировать, запущено антивирусное ПО, обязательно исключите установочную папку службы Mobility в список исключений из антивирусной проверки (*C:\ProgramData\ASR\agent*). Это обеспечит правильную работу репликации.

## <a name="update-the-mobility-service"></a>Обновление службы Mobility Service

1. Прежде чем приступать к обновлению службы Mobility Service на защищенных компьютерах, убедитесь, что серверы конфигурации, обработки масштабирования и все основные целевые серверы, входящие в состав вашего развертывания, обновлены.
2. На портале откройте хранилище и выберите **Реплицированные элементы**.
3. Если сервер конфигурации последней версии, отобразится уведомление: "Доступно обновление для агента репликации Site Recovery. Щелкните, чтобы установить его."

     ![Окно "Реплицированные элементы"](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)

4. Щелкните уведомление и в разделе **Обновление агента** выберите компьютеры, на которых нужно обновить службу Mobility Service. Нажмите кнопку **ОК**.

     ![Список виртуальных машин "Реплицированные элементы"](.\media\vmware-azure-install-mobility-service\update-okpng.png)

5. Для каждого выбранного компьютера будет запущено задание обновления службы Mobility Service.

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>Обновление учетной записи, используемой для принудительной установки Mobility Service

При развертывании Site Recovery, чтобы включить принудительную установку Mobility Service, вы указали учетную запись, которую сервер обработки Site Recovery использует для доступа к компьютерам и установки службы (если на компьютере включена репликация). Если вы хотите обновить учетные данные для этой учетной записи, следуйте [этим инструкциям](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-the-mobility-service"></a>Удаление службы Mobility Service

### <a name="on-a-windows-machine"></a>На компьютере с Windows

Выполните удаление из пользовательского интерфейса или командной строки.

- **Из пользовательского интерфейса**: на панели управления компьютера выберите **Программы**. Выберите **Microsoft Azure Site Recovery Mobility Service/Master Target server (Microsoft Azure Site Recovery Mobility Service/главный целевой сервер)** > **Удаление**.
- **Из командной строки**: откройте окно командной строки с правами администратора компьютера. Выполните следующую команду: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>На компьютере с Linux
1. На компьютере с Linux войдите в систему как **привилегированный** пользователь.
2. В разделе "Терминал" перейдите к /user/local/ASR.
3. Выполните следующую команду:

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>Дополнительная информация

[Установка службы Mobility Service для аварийного восстановления виртуальных машин VMware и физических серверов](vmware-azure-install-mobility-service.md)
