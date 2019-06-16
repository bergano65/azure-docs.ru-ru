---
title: Управление агентом мобильности на серверах для аварийного восстановления виртуальных машин VMware и физических серверов с помощью Azure Site Recovery | Документация Майкрософт
description: Управление агент службы Mobility Service для аварийного восстановления виртуальных машин VMware и физических серверов в Azure с помощью службы Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66117325"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Управление агентами mobility на защищенных компьютерах

Можно настроить агент службы mobility на сервере при использовании Azure Site Recovery для аварийного восстановления виртуальных машин VMware и физических серверов в Azure. Агент Mobility управления обменом данными между защищенной машины, сервер конфигурации/масштабируемый сервер обработки, а также репликацией данных. В этой статье перечислены распространенные задачи по управлению агента mobility после его развертывания.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Обновления службы mobility service на портале Azure

1. Прежде чем приступать к обновлению службы Mobility Service на защищенных компьютерах, убедитесь, что серверы конфигурации, обработки масштабирования и все основные целевые серверы, входящие в состав вашего развертывания, обновлены.
2. На портале откройте хранилище и выберите **Реплицированные элементы**.
3. Если сервер конфигурации последней версии, отобразится уведомление: "Доступно обновление для агента репликации Site Recovery. Щелкните, чтобы установить его."

     ![Окно "Реплицированные элементы"](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Щелкните уведомление и в разделе **Обновление агента** выберите компьютеры, на которых нужно обновить службу Mobility Service. Нажмите кнопку **ОК**.

     ![Список виртуальных машин "Реплицированные элементы"](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Для каждого выбранного компьютера будет запущено задание обновления службы Mobility Service.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Обновления службы Mobility service в сценарии powershell на Windows server

Используйте следующий скрипт, чтобы обновить службу mobility service на сервере с помощью командлета power shell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Обновление учетной записи, используемой для принудительной установки службы Mobility service

При развертывании Site Recovery, чтобы включить принудительную установку Mobility Service, вы указали учетную запись, которую сервер обработки Site Recovery использует для доступа к компьютерам и установки службы (если на компьютере включена репликация). Если вы хотите обновить учетные данные для этой учетной записи, следуйте [этим инструкциям](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Удаление службы Mobility service

### <a name="on-a-windows-machine"></a>На компьютере с Windows

Выполните удаление из пользовательского интерфейса или командной строки.

- **Установка с помощью пользовательского интерфейса**: на панели управления компьютера выберите **Программы**. Выберите **Microsoft Azure Site Recovery Mobility Service/Master Target server (Microsoft Azure Site Recovery Mobility Service/главный целевой сервер)**  > **Удаление**.
- **Установка из командной строки**: откройте окно командной строки с правами администратора на компьютере. Выполните следующую команду: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>На компьютере с Linux
1. На компьютере с Linux войдите в систему как **привилегированный** пользователь.
2. В разделе "Терминал" перейдите к /user/local/ASR.
3. Выполните следующую команду:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
