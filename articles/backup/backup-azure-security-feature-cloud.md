---
title: Функции безопасности, помогающие защитить облачные рабочие нагрузки
description: Узнайте, как использовать функции безопасности в Azure Backup, чтобы сделать резервные копии более безопасными.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 9a3c13856d3c130f2396488fed09313578dda79c
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496923"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Функции безопасности, помогающие защитить облачные рабочие нагрузки, использующие Azure Backup

Среди пользователей растет обеспокоенность вопросами безопасности, связанными с вредоносными программами, программами-шантажистами и вторжениями в систему. Эти угрозы могут привести к значительным расходам и потере данных. Для защиты от таких атак Azure Backup теперь предоставляет функции безопасности, помогающие защитить данные резервных копий даже после удаления. Одной из таких функций является обратимое удаление. При обратимом удалении, даже если вредоносный субъект удаляет резервную копию виртуальной машины (или данные резервной копии случайно удаляются), данные резервного копирования сосохранены в течение 14 дополнительных дней, что позволяет восстановить этот элемент без потери данных. Эти дополнительные 14 дней при хранении данных резервных копий в состоянии "обратимого" удаления не несут никаких затрат на клиент. Кроме того, Azure шифрует все резервные копии неактивных данных, используя [Шифрование службы хранилища](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) для дальнейшей защиты данных.

> [!NOTE]
> Обратимое удаление защищает только удаленные данные резервных копий. Если виртуальная машина удаляется без резервной копии, функция обратимого удаления не сохранит данные. Для обеспечения полной устойчивости все ресурсы должны быть защищены с помощью Azure Backup.
>

## <a name="soft-delete"></a>Обратимое удаление

### <a name="supported-regions"></a>Поддерживаемые регионы

Обратимое удаление в настоящее время поддерживается в западной центральной части США, Восточная Азия, Канаде (Канада), Восточной Азии, центральном Франции, Юго-французском, Южной (Корея), Юго-Восточной, Южной, южная часть Соединенного Королевства, западная часть Соединенного Королевства, Восточная Австралия, Юго-Восточная Австралия, Северная Европа, Западная часть США, Запад США 2, Центрально-ru, Юг Восточная Азия, северо-центральная часть США, Юго-Центральный регион США, Восточная Япония, Западная Япония, Южная Индия, Центральная Индия, Западная Индия, Восточная часть США 2, Северная Швейцария, Западная Швейцария и все национальные регионы.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Обратимое удаление для виртуальных машин с помощью портал Azure

1. Чтобы удалить данные резервной копии виртуальной машины, необходимо остановить резервное копирование. В портал Azure перейдите к хранилищу служб восстановления, щелкните правой кнопкой мыши элемент резервного копирования и выберите пункт **прерывать резервное копирование**.

   ![Снимок экрана: портал Azure элементы резервного копирования](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. В следующем окне вы получите возможность удалить или хранить данные резервной копии. Если выбрать вариант **удалить данные резервной копии** , а затем **отменить резервное копирование**, то резервная копия виртуальной машины не будет окончательно удалена. Вместо этого данные резервного копирования будут храниться в течение 14 дней в состоянии обратимого удаления. Если выбран параметр **удалить данные резервной копии** , в настроенный идентификатор электронной почты отправляется оповещение об удалении по электронной почте, информирующее пользователя о том, что срок хранения данных резервной копии остается за 14 дней. Кроме того, в 12-й день отправляется оповещение по электронной почте о том, что для восстановлении удаленных данных осталось еще два дня. Удаление откладывается до 15-го дня, когда произойдет постоянное удаление и будет отправлено итоговое оповещение по электронной почте, информирующее об окончательном удалении данных.

   ![Снимок экрана портал Azure, окно завершения резервного копирования](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. В течение 14 дней в хранилище служб восстановления обратимо Удаленная виртуальная машина появится рядом с красным значком "обратимое удаление".

   ![Снимок экрана портал Azure, виртуальная машина в состоянии обратимого удаления](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Если в хранилище имеются обратимо удаленные элементы резервного копирования, хранилище нельзя будет удалить в это время. Попробуйте удалить хранилище после окончательного удаления архивных элементов, и в хранилище не останется ни одного элемента в состоянии обратимого удаления.

4. Чтобы восстановить обратимо удаленную виртуальную машину, необходимо сначала отменить ее удаление. Чтобы отменить удаление, выберите обратимо удаленную виртуальную машину, а затем выберите пункт **Удалить**.

   ![Снимок экрана портал Azure, отменить удаление виртуальной машины](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Появится окно с предупреждением о том, что при выборе отменить удаление все точки восстановления для виртуальной машины будут удалены и доступны для выполнения операции восстановления. Виртуальная машина будет храниться в состоянии "остановить защиту с сохранением данных" с приостановленными резервными копиями и хранением данных резервного копирования без учета политики резервного копирования.

   ![Снимок экрана портал Azure, подтверждение отмены удаления виртуальной машины](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   На этом этапе можно также восстановить виртуальную машину, выбрав пункт **восстановить виртуальную машину** из выбранной точки восстановления.  

   ![Снимок экрана: параметр портал Azure, восстановление виртуальной машины](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Сборщик мусора будет работать и очищать точки восстановления с истекшим сроком только после того, как пользователь выполнит операцию **возобновления резервного копирования** .

5. После завершения процесса отмены удаления будет возвращено состояние "прерывать резервное копирование с помощью удержания данных", после чего можно выбрать **возобновить резервное копирование**. Операция **возобновления** резервного копирования возвращает резервную копию в активном состоянии, связанную с политикой резервного копирования, выбранной пользователем, определяющей расписания резервного копирования и хранения.

   ![Снимок экрана: параметр портал Azure, возобновить резервное копирование](./media/backup-azure-security-feature-cloud/resume-backup.png)

На этой блок-схеме показаны различные шаги и состояния элемента резервного копирования, когда включено обратимое удаление.

![Жизненный цикл элемента резервного копирования с обратимым удалением](./media/backup-azure-security-feature-cloud/lifecycle.png)

Дополнительные сведения см. в разделе " [часто задаваемые вопросы](backup-azure-security-feature-cloud.md#frequently-asked-questions) " ниже.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Обратимое удаление для виртуальных машин с помощью Azure PowerShell

> [!IMPORTANT]
> Версия AZ. RecoveryServices, необходимая для использования обратимого удаления с помощью Azure PS, — это min 2.2.0. Чтобы получить последнюю версию, используйте ```Install-Module -Name Az.RecoveryServices -Force```.

Как описано выше для портал Azure, последовательность шагов при использовании Azure PowerShell также одинакова.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Удаление элемента архивации с помощью Azure PowerShell

Удалите элемент резервного копирования с помощью командлета [Disable-азрековерисервицесбаккуппротектион](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

Значение "Делетестате" элемента резервного копирования изменится с "Нотделетед" на "Тобеделетед". Данные резервной копии будут храниться в течение 14 дней. Если вы хотите отменить операцию удаления, необходимо выполнить отмену-удаление.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Отмена операции удаления с помощью Azure PowerShell

Сначала извлеките соответствующий элемент резервного копирования, который находится в состоянии обратимого удаления, т. е. сведения об удалении

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Затем выполните операцию отмены удаления с помощью командлета [Undo-азрековерисервицесбаккупитемделетион](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Значение "Делетестате" элемента резервного копирования будет возвращено к "Нотделетед". Но защита по-прежнему останавливается. Чтобы повторно включить защиту, необходимо [возобновить резервное копирование](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) .

### <a name="soft-delete-for-vms-using-rest-api"></a>Обратимое удаление для виртуальных машин с помощью REST API

- Удалите резервные копии с помощью REST API, как описано [здесь](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Если пользователь хочет отменить эти операции удаления, см. действия, описанные [здесь](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Отключение обратимого удаления

Обратимое удаление по умолчанию включено для вновь созданных хранилищ, чтобы защитить данные резервных копий от случайных или вредоносных удалений.  Отключение этой функции не рекомендуется. Единственное обстоятельство, где следует отключать обратимое удаление, — это планирование перемещения защищенных элементов в новое хранилище и ожидание в течение 14 дней, необходимых перед удалением и повторной защитой (например, в тестовой среде). Только администратор резервного копирования может отключить эту функцию. Если отключить эту функцию, все удаления защищенных элементов приведут к немедленному удалению без возможности восстановления. Резервное копирование данных в обратимо удаленном состоянии перед отключением этой функции будет оставаться в обратимо удаленном состоянии. Если вы хотите безвозвратно удалить эти данные немедленно, необходимо отменить удаление и удалить их снова, чтобы окончательно удалить их.

### <a name="disabling-soft-delete-using-azure-portal"></a>Отключение обратимого удаления с помощью портал Azure

Чтобы отключить обратимое удаление, выполните следующие действия.

1. В портал Azure перейдите в хранилище, а затем выберите **параметры** -> **свойства**.
2. На панели Свойства выберите **Параметры безопасности** -> **Обновить**.  
3. В области Параметры безопасности в разделе **обратимое удаление**выберите **Отключить**.

![Отключить обратимое удаление](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Отключение обратимого удаления с помощью Azure PowerShell

> [!IMPORTANT]
> Версия AZ. RecoveryServices, необходимая для использования обратимого удаления с помощью Azure PS, — это min 2.2.0. Чтобы получить последнюю версию, используйте ```Install-Module -Name Az.RecoveryServices -Force```.

Чтобы отключить, используйте командлет [Set-азрековерисервицесваултбаккуппроперти](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Отключение обратимого удаления с помощью REST API

Чтобы отключить функцию обратимого удаления с помощью REST API, см. действия, описанные [здесь](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Окончательное удаление обратимо удаленных архивных элементов

Резервное копирование данных в обратимо удаленном состоянии перед отключением этой функции будет оставаться в обратимо удаленном состоянии. Если вы хотите безвозвратно удалить эти данные немедленно, удалите их и удалите снова, чтобы окончательно удалить их.

### <a name="using-azure-portal"></a>Использование портала Azure

Выполните следующие действия.

1. Выполните действия по [отключению обратимого удаления](#disabling-soft-delete).
2. В портал Azure перейдите в хранилище, перейдите к **элементу Архивация** и выберите обратимо удаленную виртуальную машину.

![Выберите обратимо удаленную виртуальную машину](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Выберите параметр отменить **Удаление**.

![Выберите отменить удаление](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. Появится окно. Выберите отменить **Удаление**.

![Выберите отменить удаление](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Выберите **удалить резервные копии данных** , чтобы окончательно удалить данные резервной копии.

![Выберите Удалить данные архивации](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Введите имя элемента резервного копирования, чтобы подтвердить, что необходимо удалить точки восстановления.

![Введите имя элемента архивации](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Чтобы удалить данные архивации для элемента, выберите **Удалить**. Сообщение уведомления позволяет убедиться, что данные резервного копирования удалены.

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

Если элементы были удалены до отключения обратимого удаления, они будут находиться в состоянии обратимого удаления. Чтобы немедленно удалить их, операция удаления должна быть отменена, а затем снова выполнена.

Определяет элементы, которые находятся в состоянии обратимого удаления.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Затем отмените операцию удаления, которая была выполнена, когда обратимое удаление было включено.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Так как обратимое удаление теперь отключено, операция удаления приведет к немедленному удалению данных резервного копирования.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Использование REST API

Если элементы были удалены до отключения обратимого удаления, они будут находиться в состоянии обратимого удаления. Чтобы немедленно удалить их, операция удаления должна быть отменена, а затем снова выполнена.

1. Сначала отмените операции удаления, выполнив описанные [здесь](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)действия.
2. Затем отключите функцию обратимого удаления с помощью REST API, выполнив описанные [здесь](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)действия.
3. Затем удалите резервные копии с помощью REST API, как описано [здесь](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="other-security-features"></a>Другие функции безопасности

### <a name="storage-side-encryption"></a>Шифрование на стороне хранилища

Служба хранилища Azure автоматически шифрует данные при сохранении их в облаке. Шифрование защищает ваши данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации. Данные в службе хранилища Azure шифруются и расшифровываются прозрачно с использованием 256-разрядного шифрования AES, одним из наиболее подданных блоков блочных шифров и совместимым с FIPS 140-2. Шифрование службы хранилища Azure аналогично шифрованию BitLocker в Windows. Azure Backup автоматически шифрует данные перед сохранением. Служба хранилища Azure расшифровывает данные перед извлечением.  

В Azure данные, передаваемые между службой хранилища Azure и хранилищем, защищаются по протоколу HTTPS. Эти данные остаются в магистральной сети Azure.

Дополнительные сведения см. в статье [Шифрование службы хранилища Azure для неактивных данных](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).  Чтобы ответить на вопросы о шифровании, обратитесь к разделу [часто ЗАдаваемых вопросов Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) .

### <a name="vm-encryption"></a>Шифрование виртуальной машины

Вы можете выполнять резервное копирование и восстановление виртуальных машин Azure (ВМ) Windows или Linux с помощью зашифрованных дисков, используя службу Azure Backup. Инструкции см. в статье [резервное копирование и восстановление зашифрованных виртуальных машин с помощью Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

### <a name="protection-of-azure-backup-recovery-points"></a>Защита точек восстановления Azure Backup

Учетные записи хранения, используемые хранилищами служб восстановления, изолированы и недоступны пользователям для вредоносных целей. Доступ разрешен только с помощью операций управления Azure Backup, таких как восстановление. Управление этими операциями осуществляется с помощью управления доступом на основе ролей (RBAC).

Дополнительные сведения см. в статье [Использование управления доступом на основе ролей для управления Azure Backup точек восстановления](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="for-soft-delete"></a>Для обратимого удаления

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Нужно ли включать функцию обратимого удаления в каждом хранилище?

Нет, он создается и включается по умолчанию для всех хранилищ служб восстановления.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Можно ли настроить число дней, в течение которых мои данные будут храниться в обратимо удаленном состоянии после завершения операции удаления?

Нет, после операции удаления в течение 14 дней потребуется дополнительное хранение.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Нужно ли платить за это дополнительное 14-дневное хранение?

Нет, эта 14-дневная дополнительная экономия предоставляется бесплатно в рамках функции обратимого удаления.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Можно ли выполнить операцию восстановления, когда данные находятся в состоянии обратимого удаления?

Нет, для восстановления необходимо отменить удаление ресурса с обратимым удалением. Операция отмены удаления переместит ресурс обратно в **состояние защита с сохранением данных** , где можно выполнить восстановление в любой момент времени. Сборщик мусора остается приостановленным в этом состоянии.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Будут ли мои моментальные снимки следовать тому же жизненному циклу, что и мои точки восстановления в хранилище?

Да.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Как снова запустить запланированное резервное копирование для ресурса с обратимым удалением?

Операция отмены удаления с последующей операцией возобновления обеспечит защиту ресурса. Операция возобновления связывает политику резервного копирования для активации запланированных резервных копий с выбранным сроком хранения. Кроме того, сборщик мусора запускается сразу после завершения операции возобновления. Если вы хотите выполнить восстановление из точки восстановления, которая находится за предельным сроком действия, рекомендуется выполнить ее перед запуском операции возобновления.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Можно ли удалить хранилище, если в хранилище есть обратимо удаленные элементы?

Хранилище служб восстановления невозможно удалить, если в хранилище есть элементы резервного копирования в обратимо удаленном состоянии. Обратимо удаленные элементы окончательно удаляются через 14 дней после операции удаления. Если вы не можете подождать 14 дней, [Отключите обратимое удаление, отмените](#disabling-soft-delete)удаление обратимо удаленных элементов и удалите их, чтобы окончательно удалить их. Убедившись в отсутствии защищенных элементов и отсутствии обратимо удаленных элементов, хранилище можно удалить.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Можно ли удалить данные, предшествующие 14-дневному периоду обратимого удаления после удаления?

Нет. Нельзя принудительно удалять обратимо удаленные элементы, они автоматически удаляются через 14 дней. Эта функция безопасности включена для защиты резервных копий данных от случайных или вредоносных удалений.  Необходимо подождать 14 дней, прежде чем выполнять любые другие действия с виртуальной машиной.  Обратимо удаленные элементы не будут начисляться.  Если требуется повторно защитить виртуальные машины, помеченные для обратимого удаления в течение 14 дней, в новое хранилище, обратитесь в службу поддержки Майкрософт.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Могут ли операции обратимого удаления выполняться в PowerShell или CLI?

Операции обратимого удаления можно выполнить с помощью [PowerShell](#soft-delete-for-vms-using-azure-powershell). В настоящее время CLI не поддерживается.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Поддерживается ли обратимое удаление для других облачных рабочих нагрузок, таких как SQL Server на виртуальных машинах Azure и SAP HANA на виртуальных машинах Azure?

Нет. Сейчас обратимое удаление поддерживается только для виртуальных машин Azure.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [элементами управления безопасностью для Azure Backup](backup-security-controls.md).
