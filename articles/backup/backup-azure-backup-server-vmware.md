---
title: Резервное копирование виртуальных машин VMware с помощью Azure Backup Server
description: Для резервного копирования виртуальных машин VMware, запущенных на серверах VMWare vCenter и ESXi, используйте Azure Backup Server.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: adb8cb6a5740b7d09848c792093ea62f69f521a7
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466799"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Резервное копирование виртуальных машин VMware с помощью Azure Backup Server

В этой статье объясняется, как в Azure с помощью Azure Backup Server создать резервные копии виртуальных машин VMware на узлах VMware ESXi или vCenter Server.

В этой статье описано, как:

- настроить безопасный канал так, чтобы Azure Backup Server мог обмениваться данными с серверами VMware по протоколу HTTPS;
- настроить учетную запись VMware, которую использует Azure Backup Server для доступа к серверу VMware;
- добавить учетные данные учетной записи в Azure Backup;
- добавить сервер vCenter или ESXi в Azure Backup Server;
- настроить группу защиты, которая содержит необходимые для резервного копирования виртуальные машины VMware, указать параметры резервного копирования и планировать его выполнение.

## <a name="before-you-start"></a>Перед началом
- Убедитесь, что версия vCenter или ESXi поддерживает резервное копирование (версии 6.5, 6.0 и 5.5).
- Убедитесь, что Azure Backup Server настроено. Если это еще не сделано, перед началом изучите [это](backup-azure-microsoft-azure-backup.md). Azure Backup Server следует запускать с последними обновлениями.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Создание безопасного подключения к серверу vCenter

По умолчанию Azure Backup Server взаимодействует с серверами VMware по протоколу HTTPS. Чтобы настроить соединение HTTPS, скачайте сертификат Центра сертификации (ЦС) VMware и импортируйте его в Azure Backup Server.


### <a name="before-you-start"></a>Перед началом

- Если вы не хотите использовать протокол HTTPS, можно [отключить проверку сертификата HTTPS для всех серверов VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Обычно к серверу vCenter или ESXi можно подключиться из браузера на компьютере Azure Backup Server через веб-клиент vSphere. Соединение не защищено, когда делаете это в первый раз, и отобразится следующее.
- Важно понимать, как Azure Backup Server обрабатывает резервные копии.
    - Первым шагом будет создание Azure Backup Server резервной копии данных в локальном хранилище дисков. Azure Backup Server использует пул хранилища, набор дисков и томов, на которых Azure Backup Server хранит точки восстановления диска для защищенных данных. Пул хранения может быть хранилищем, подключенным напрямую (DAS), оптоволоконным каналом сети SAN или устройством или сетью хранения данных iSCSI. Важно убедиться, что хранилище имеет достаточный объем для создания локальной резервной копии данных виртуальной машины VMware.
    - Затем Azure Backup Server создает резервные копии в Azure из локального хранилища дисков.
    - Чтобы выяснить, какой объем хранилища необходим, см. [этот раздел](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need). Информация предназначена для DPM, но ее также можно использовать для сервера Azure Backup Server.

### <a name="set-up-the-certificate"></a>Настройка сертификата

Настройте безопасный канал следующим образом.

1. В браузере на сервере Azure Backup Server введите URL-адрес веб-клиента vSphere. Если страница входа не отображается, проверьте параметры подключения и прокси-сервера из браузера.

    ![Веб-клиент vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. На странице входа веб-клиента vSphere Щелкните **Download trusted root CA certificates** (Скачать сертификаты доверенного корневого ЦС).

    ![Скачивание сертификата доверенного корневого ЦС.](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Загружается файл с именем **download**. В зависимости от браузера появится сообщение с предложением открыть или сохранить файл.

    ![Скачивание сертификата ЦС](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Сохраните файл на компьютере Azure Backup Server с расширением ZIP.

5. Правой кнопкой мыши щелкните **download.zip** > **Extract All** (Извлечь все). Содержимое сжатого ZIP-файла будет извлечено в папку **certs**, которая вмещает следующие элементы.
   - Файл корневого сертификата с расширением, которое начинается с нумерованной последовательности, например, .0 и .1.
   - Файл списка отзыва сертификатов имеет расширение, которое начинается с такой последовательности, как .r0 или .r1. Файл списка отзыва сертификатов связан с сертификатом.

     ![Загруженные сертификаты](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. В папке **certs** щелкните правой кнопкой мыши файл корневого сертификата и выберите **Переименовать**.

    ![Переименование корневого сертификата](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Измените расширение файла корневого сертификата на CRT и подтвердите. Значок файла изменится на значок корневого сертификата.

7. Щелкните правой кнопкой мыши этот корневой сертификат и во всплывающем меню выберите **Установить сертификат**.

8. В **Certificate Import Wizard** (Мастере импорта сертификатов) выберите **Local Machine** (Локальный компьютер) в качестве места назначения для сертификата, а затем щелкните **Далее**. Если отобразится запрос разрешения на внесение изменений на компьютер, разрешите внести изменения.

    ![Страница приветствия мастера](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. На странице **Хранилище сертификатов** выберите **Поместить все сертификаты в следующее хранилище**, а затем щелкните **Обзор**, чтобы выбрать хранилище сертификатов.

     ![Хранилище сертификатов](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. В окне **Select Certificate Store** (Выбор хранилища сертификатов) в качестве папки назначения для сертификатов выберите **Trusted Root Certification Authorities** (Доверенные корневые центры сертификации и нажмите кнопку) и щелкните **ОК**.

    ![Папка назначения для сертификата](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. В окне **Completing the Certificate Import Wizard** (Завершение работы мастера импорта сертификатов) проверьте папку и нажмите кнопку **Готово**.

    ![Проверка наличия сертификата в нужной папке](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. После подтверждения импорта сертификата войдите на vCenter Server, чтобы подтвердить, что подключение защищено.




### <a name="disable-https-certificate-validation"></a>Отключить проверку сертификата HTTPS

Если в вашей организации есть границы безопасности, или вы не хотите использовать протокол HTTPS между серверами VMware и компьютером Azure Backup Server, отключите HTTPS следующим образом.
1. Скопируйте приведенный ниже текст и вставьте его в TXT-файл.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. На компьютере Azure Backup Server сохраните файл с именем **DisableSecureAuthentication.reg**.

3. Дважды щелкните этот файл, чтобы активировать запись реестра.


## <a name="create-a-vmware-role"></a>Создание роли VMware

Для Azure Backup Server необходима учетная запись пользователя с разрешениями для доступа к серверу v-Center Server или узлу ESXi. Создайте роль VMware с определенными привилегиями, а затем свяжите учетную запись пользователя с ролью.

1. Войдите на сервер vCenter Server (или, если вы не используете vCenter Server, на узел ESXi).
2. На панели **Навигатор** щелкните **Администрирование**.

    ![Управление](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. В **Администрирование** > **Роли** щелкните значок добавления роли (символ "+").

    ![Добавить роль](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. В разделе**Создание роли** > **Имя роли** введите *BackupAdminRole*. Это может быть любое имя, описывающее назначение этой роли.

5. Выберите привилегии, как описано в следующей таблице, а затем нажмите кнопку **ОК**.  В списке на панели **Роли** появится новая роль.
   - Щелкните значок рядом с родительской меткой, чтобы раскрыть родительский элемент и просмотреть дочерние привилегии.
   - Чтобы выбрать привилегии виртуальной машины, необходимо использовать несколько уровней иерархии родителей-потомков.
   - Вам не нужно выбирать все дочерние привилегии, входящие в родительскую.

     ![Иерархия привилегий родителей-потомков](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Разрешения роли
**6.5 или 6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>Создание учетной записи VMware

1. На панели vCenter Server **Навигатор** щелкните **Пользователи и группы**. Создайте учетную запись на соответствующем узле ESXi в том случае, если вы не используете vCenter Server.

    ![Пользователи и группы](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Появится панель **Пользователи и группы vCenter** .


2. На панели **vCenter Users and Groups** (Пользователи и группы vCenter) выберите вкладку **Users** (Пользователи), а затем щелкните значок добавления пользователей (знак "+").

     ![Панель vCenter Users and Groups (Пользователи и группы vCenter)](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. В диалоговом окне **Новый пользователь** добавьте сведения о пользователе, а затем щелкните **ОК**. В этой процедуре имя пользователя — BackupAdmin.

    ![Диалоговое окно New User (Новый пользователь)](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. Чтобы связать учетную запись пользователя с ролью, на панели **Navigator** (Навигатор) щелкните **Global Permissions** (Глобальные разрешения). На панели **Global Permissions** (Глобальные разрешения) выберите вкладку **Manage** (Управление), а затем щелкните значок добавления (знак "+").

    ![Панель Global Permissions (Глобальные разрешения)](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. В окне**Global Permission Root – Add Permission** (Корень глобального разрешения – добавление разрешения) щелкните **Добавить**, чтобы выбрать пользователя или группу.

    ![Выбор пользователя или группы](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. В окне **Select Users/Groups Выбрать пользователей или группы** (Выбрать пользователей или группы) выберите **BackupAdmin** > **Добавить**. В разделе **Users** (Пользователи) для учетной записи пользователя используется формат *домен\имя пользователя*. Если вы хотите использовать другой домен, выберите его из списка **доменов**. Нажмите кнопку **ОК**, чтобы добавить выбранных пользователей в диалоговое окно **Add Permission** (Добавление разрешения).

    ![Добавление пользователя BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  В разделе **Назначенная роль** в раскрывающемся списке выберите **BackupAdminRole** > **ОК**.

    ![Назначение пользователя роли](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


На вкладке **Manage** (Управление) на панели **Global Permissions** (Глобальные разрешения) в списке появятся новая учетная запись пользователя и связанная роль.


## <a name="add-the-account-on-azure-backup-server"></a>Добавление учетной записи в Azure Backup Server


1. Откройте Azure Backup Server. Если не удается найти значок на рабочем столе, откройте Microsoft Azure Backup из списка приложений.

    ![Значок Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. На консоли Azure Backup Server щелкните **Управление** >  **Рабочие серверы** > **Manage VMware**  (Управление VMware).

    ![Консоль сервера Azure Backup Server](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. В диалоговом окне **Управление учетными данными** щелкните **Добавить**.

    ![Диалоговое окно управления учетными данными на сервере Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. В окне **Добавить учетные данные** введите имя и описание новых учетных данных и укажите имя пользователя и пароль, определенные на сервере VMware. Имя *Contoso Vcenter credential* используется для идентификации учетных данных в этой процедуре. Если сервер VMware и Azure Backup Server размещены в разных доменах, укажите домен в имени пользователя.

    ![Диалоговое окно добавления учетных данных на сервере Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Нажмите кнопку **Добавить**, чтобы добавить новые учетные данные.

    ![Диалоговое окно управления учетными данными на сервере Azure Backup Server](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>Добавление vCenter Server

Добавление vCenter Server в Azure Backup Server


1. На консоли Azure Backup Server щелкните **Управление** > **Рабочие серверы** > **Добавить**.

    ![Открытие мастера добавления рабочего сервера](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. На странице **Production Server Addition Wizard**(Мастер добавления рабочего сервера) > **Select Production Server type**  (Выбор типа рабочего сервера) выберите **VMware Servers** (Серверы VMware), а затем щелкните **Далее**.

     ![Мастер добавления рабочего сервера](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. На шаге **Выбрать компьютеры** в строке **Server Name/IP Address Имя или IP-адрес сервера** (Имя или IP-адрес сервера) укажите полное доменное имя или IP-адрес сервера VMware. Если один и тот же сервер vCenter управляет всеми серверами ESXi, то укажите имя vCenter. В противном случае добавьте узел ESXi.

    ![Указывание сервера VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. В поле **SSL-порт** введите порт, который использовался для взаимодействия с сервером VMware. 443 – порт по умолчанию, но если ваш сервер VMware прослушивает другой порт, его можно изменить.

5. В поле **указания учетных данных** выберите созданные учетные данные.

    ![Указание учетных данных](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Щелкните **Добавить**, чтобы добавить сервер VMware в список серверов. Затем нажмите кнопку **Далее**.

    ![Добавление сервера VMware и учетных данных](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. На странице **Сводка** щелкните **Добавить**, чтобы добавить сервер VMware на Azure Backup Server. Новый сервер добавляется сразу, на сервере VMware агент не нужен.

    ![Добавление сервера VMware в Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Проверьте параметры на странице **Готово**.

   ![Страница "Готово"](./media/backup-azure-backup-server-vmware/summary-screen.png)

Если у вас есть несколько узлов ESXi, которыми сервер vCenter не управляет, или несколько экземпляров vCenter Server, для добавления серверов необходимо снова запустить мастер.




## <a name="configure-a-protection-group"></a>Настройка группы защиты

Добавьте виртуальные машины VMware для резервного копирования. Группы защиты собирают несколько виртуальных машин и применяют одинаковые параметры хранения данных и резервного копирования ко всем виртуальным машинам в группе.


1. На консоли Azure Backup Server щелкните **Защита**, а затем **Создать**.

    ![Открытие мастера создания групп защиты](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. На домашней странице мастера **Create New Protection Group** (Создание групп защиты) щелкните **Далее**.

    ![Диалоговое окно мастера создания групп защиты](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. На странице **Выбор типа группы защиты** выберите **Серверы** и нажмите кнопку **Далее**. Появится страница **Выбор элементов группы**.

1. В **Select group members** (Выбор элементов группы) > выберите виртуальные машины (или папки виртуальных машин), для которых вы хотите создать резервную копию. Затем нажмите кнопку **Далее**.

    - Когда вы выберите папку, виртуальные машины или папки внутри этой папки также будут выбраны для резервного копирования. Можно отменить выбор папок или виртуальных машин, для которых вы не хотите создавать резервную копию.
1. Это сделать невозможно, если уже выполняется резервное копирование виртуальной машины или папки. Благодаря этому повторяющиеся точки восстановления не создаются для виртуальной машины. .

     ![Выберите членов группы](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


1. На странице **Select Data Protection Method** (Выбор метода защиты данных) введите имя для группы защиты и параметры защиты. Чтобы создать резервную копию Azure, установите краткосрочную защиту **Диска** и разрешите защиту в сети. Затем нажмите кнопку **Далее**.

    ![Выберите метод защиты данных](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. В окне **Specify Short-Term Goals** (Выбор краткосрочных целей) укажите, как долго вы хотите хранить резервную копию данных на диске.
   - В окне **Диапазон хранения** укажите количество дней для хранения точек восстановления диска.
   - В окне **Частота синхронизации** укажите частоту создания точек восстановления диска.
       - Если вы не хотите указывать интервал резервного копирования, можно выбрать опцию **Непосредственно перед точкой восстановления**, чтобы резервная копия выполнялась непосредственно перед каждой запланированной точкой восстановления.
       - Краткосрочные резервные копии являются полными резервными копиями, а не добавочным.
       - Чтобы изменить дату или время, когда произошло краткосрочное резервное копирование, нажмите кнопку **Изменить**.

     ![Выберите краткосрочные цели](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. В окне **Проверить выделение дискового пространства** проверьте дисковое пространство для резервных копий виртуальных машин. Для виртуальных машин.

   - Рекомендуемые дисковые пространства основаны на указанном диапазоне периода удержания, типе рабочей нагрузки и размере защищенных данных. Внесите необходимые изменения и щелкните **Далее**.
   - **Размер данных.** Размер данных в группе защиты.
   - **Место на диске.** Рекомендуемый объем дискового пространства для группы защиты. Если вы хотите изменить этот параметр, вы должны выделить общее место, объем которого будет немного превышать рассчитанный вами объем для каждого источника данных.
   - **Совместное размещение данных.** Если вы включите совместное размещение, несколько источников данных в защите могут сопоставляться с отдельной репликой и томом точек восстановления. Совместное размещение поддерживается не для всех рабочих нагрузок.
   - **Автоматическое увеличение.** Если вы включите этот параметр, то в случае, когда объем данных в защищенной группе превысит первоначальный выделенный объем, Azure Backup Server попытается увеличить размер диска на 25 %.
   - **Дополнительные сведения о пуле носителей.** Этот параметр отображает состояние пула хранилища, включая общий размер диска и оставшийся.

     ![Просмотр выделения дискового пространства](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. В окне **Choose Replica Creation Method** (Выбрать метод создания реплики) определите, как вы хотите выполнить начальное резервное копирование, а затем щелкните **Далее**.
   - Значения по умолчанию — **Автоматически по сети** и **Сейчас**.
   - Если вы используете значения по умолчанию, укажите время низкой нагрузки. Выберите параметр **Позже** и укажите день и время.
   - Для больших объемов данных или неоптимальных условий сети лучше выбрать автономную репликацию данных с использованием съемных носителей.

     ![Выберите метод создания реплики](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. На странице **Consistency Check Options** (Параметры проверки согласованности) выберите, как и когда автоматически запускать проверки на согласованность. Затем нажмите кнопку **Далее**.
      - В случае несогласованности данных реплики вы можете запустить проверку на согласованность, либо эта проверка может выполняться по расписанию.
      - Если настраивать автоматическую проверку согласованности не требуется, можно выполнить проверку вручную. Чтобы это сделать, щелкните правой кнопкой мыши группу защиты, а затем нажмите **Perform Consistency Check** (Выполнить проверку согласованности).

1. На странице **Specify Online Protection Data** (Указание данных для оперативной защиты) выберите виртуальные машины или папки виртуальной машины для создания резервной копии. Вы можете выбрать элементы по отдельности или же щелкнуть **Выделить все**. Затем нажмите кнопку **Далее**.

      ![Выбор оперативной защиты данных](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. На странице **Specify Online Backup Schedule**  (Укажите расписание архивации в сети) укажите частоту резервного копирования данных из локального хранилища в Azure.

    - Точки восстановления для данных в облаке будут созданы согласно с расписанием. Затем нажмите кнопку **Далее**.
    - Созданная точка восстановления передается в хранилище служб восстановления в Azure.

      ![Выбор расписания оперативного резервного копирования](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. На странице **Specify Online Retention Policy** (Выбор политику хранения в сети) укажите, каким образом точки восстановления, созданные из ежедневных, еженедельных, ежемесячных и ежегодных резервных копий, должны храниться в Azure. Затем нажмите **Далее**.

    - Для хранения данных в Azure нет временных ограничений.
    - Единственным ограничением является то, что на защищенный экземпляр у вас может быть не более 9999 точек восстановления. В этом примере защищенным экземпляром является сервер VMware.

      ![Выбор политики оперативного хранения](./media/backup-azure-backup-server-vmware/retention-policy.png)


1. На странице **Сводка** просмотрите параметры и нажмите кнопку **Создать группу**.

     ![Сводка параметров и элементов группы защиты](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Чтобы выполнить резервное копирование vSphere 6,7, выполните следующие действия.

- Включение TLS 1,2 на сервере DPM
  >[!Note]
  >В VMWare 6,7 включены протокол TLS в качестве протокола связи.

- Задайте следующие разделы реестра:  

  Редактор реестра Windows версии 5,00

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v2.0.50727] "Системдефаулттлсверсионс" = DWORD: 00000001 "SchUseStrongCrypto" = DWORD: 00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319] "Системдефаулттлсверсионс" = DWORD: 00000001 "SchUseStrongCrypto" = DWORD: 00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v2.0.50727] "Системдефаулттлсверсионс" = DWORD: 00000001 "SchUseStrongCrypto" = DWORD: 00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v4.0.30319] "Системдефаулттлсверсионс" = DWORD: 00000001 s "SchUseStrongCrypto" = DWORD: 00000001


## <a name="next-steps"></a>Следующие шаги

Для решения проблем с устранением неполадок при настройке резервного копирования см. статью [Устранение неполадок Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
