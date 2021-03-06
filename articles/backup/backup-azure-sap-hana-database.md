---
title: Резервное копирование базы данных SAP HANA в Azure с помощью Azure Backup
description: Из этой статьи вы узнаете, как создать резервную копию базы данных SAP HANA на виртуальных машинах Azure со службой Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ed47f18c9dabc685d6fbe02804562ef86a93190a
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285803"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Резервное копирование баз данных SAP HANA на виртуальных машинах Azure

SAP HANA базы данных — это критически важные рабочие нагрузки, для которых требуется низкая Целевая точка восстановления (RPO) и долгосрочное хранение. Вы можете выполнять резервное копирование SAP HANA баз данных, работающих на виртуальных машинах Azure, с помощью [Azure Backup](backup-overview.md).

В этой статье показано, как выполнить резервное копирование SAP HANA баз данных, работающих на виртуальных машинах Azure, в хранилище служб восстановления Azure Backup.

В этой статье раскрываются следующие темы:
> [!div class="checklist"]
>
> * Создание и Настройка хранилища
> * Обнаружение баз данных
> * Настройка резервного копирования
> * Выполнение задания архивации по запросу

## <a name="prerequisites"></a>предварительным требованиям

Дополнительные сведения о настройке базы данных для резервного копирования см. в разделах [Предварительные требования](tutorial-backup-sap-hana-db.md#prerequisites) и [Настройка разрешений](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

### <a name="set-up-network-connectivity"></a>Настройка сетевого подключения

Для всех операций SAP HANA виртуальной машине требуется подключение к общедоступным IP-адресам Azure. Операции виртуальной машины (обнаружение базы данных, Настройка резервного копирования, планирование резервного копирования, восстановление точек восстановления и т. д.) не могут работать без подключения. Установите подключение, предоставив доступ к диапазонам IP-адресов центра обработки данных Azure:

* Вы можете скачать [диапазоны IP-адресов](https://www.microsoft.com/download/details.aspx?id=41653) для центров обработки данных Azure, а затем разрешить доступ к этим IP-адресам.
* Если вы используете группы безопасности сети (группы безопасности сети), вы можете использовать [тег службы](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud, чтобы разрешить все общедоступные IP-адреса Azure. Для изменения правил NSG можно использовать [командлет Set-азуренетворксекуритируле](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) .
* Порт 443 должен быть добавлен в список разрешений, так как транспорт осуществляется через HTTPS.

## <a name="onboard-to-the-public-preview"></a>Подключение к общедоступной предварительной версии

Подключитесь к общедоступной предварительной версии, как показано ниже.

* На портале Зарегистрируйте идентификатор подписки для поставщика служб восстановления, [следуя этой статье](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Для PowerShell выполните этот командлет. Она должна быть выполнена как зарегистрированная.

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Обнаружение баз данных

1. В хранилище в **Начало работы**щелкните **резервное копирование**. В разделе **где выполняется рабочая нагрузка?** выберите **SAP HANA на виртуальной машине Azure**.
2. Нажмите кнопку **начать обнаружение**. Это инициирует обнаружение незащищенных виртуальных машин Linux в регионе хранилища.

   * После обнаружения на портале отображаются незащищенные виртуальные машины, перечисленные по имени и группе ресурсов.
   * Если виртуальная машина не указана ожидаемым образом, проверьте, не была ли она создана в хранилище.
   * Несколько виртуальных машин могут иметь одно и то же имя, но они принадлежат разным группам ресурсов.

3. В окне **Выбор виртуальных машин**щелкните ссылку, чтобы скачать скрипт, предоставляющий разрешения для службы Azure Backup, чтобы получить доступ к SAP HANAным виртуальным машинам для обнаружения базы данных.
4. Запустите сценарий на каждой виртуальной машине, где размещаются SAP HANA базы данных, для которых требуется создать резервную копию.
5. После выполнения скрипта на виртуальных машинах в области **Выбор виртуальных машин**выберите виртуальные машины. Затем нажмите кнопку **обнаружить баз данных**.
6. Azure Backup обнаруживает все базы данных SAP HANA на виртуальной машине. Во время обнаружения Azure Backup регистрирует виртуальную машину в хранилище и устанавливает расширение на виртуальной машине. Агент не установлен в базе данных.

    ![Обнаружение баз данных SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Настройка резервного копирования  

Теперь включите резервное копирование.

1. На шаге 2 Нажмите кнопку **Настройка резервного копирования**.

    ![Настройка резервного копирования](./media/backup-azure-sap-hana-database/configure-backup.png)
2. В поле **выберите элементы для резервного копирования**выберите все базы данных, которые необходимо защитить > **ОК**.

    ![Выберите элементы для резервного копирования](./media/backup-azure-sap-hana-database/select-items.png)
3. В разделе **политика резервного копирования** > **выберите Политика архивации**, создайте новую политику резервного копирования для баз данных в соответствии с приведенными ниже инструкциями.

    ![Выбор политики архивации](./media/backup-azure-sap-hana-database/backup-policy.png)
4. После создания политики в меню **резервного копирования** выберите **включить резервное копирование**.

    ![Включение резервного копирования](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Отслеживать ход настройки архивации в области **уведомлений** на портале.

### <a name="create-a-backup-policy"></a>Создание политики архивации

Политика архивации определяет время создания резервных копий и время их хранения.

* Политика создается на уровне хранилища.
* Несколько хранилищ могут использовать одну и ту же политику резервного копирования, но тогда необходимо применить эту политику резервного копирования к каждому хранилищу.

Укажите параметры политики следующим образом.

1. Введите имя новой политики в поле **Имя политики**.

   ![Введите имя политики](./media/backup-azure-sap-hana-database/policy-name.png)
2. В меню **Full Backup policy** (Политика полного резервного копирования) для параметра **Частота архивации** выберите **Ежедневно** или **Еженедельно**.
   * **Ежедневно**. Выберите час и часовой пояс, в котором начинается задание резервного копирования.
       * Необходимо запустить полную резервную копию. Этот параметр нельзя отключить.
       * Щелкните **Полная резервная копия**, чтобы просмотреть политику.
       * При ежедневном создании полных резервных копий невозможно создавать разностные резервные копии.
   * **Еженедельно**. Выберите день недели, часа и часовой пояс, в котором будет выполняться задание резервного копирования.

   ![Выберите периодичность резервного копирования](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. В поле **диапазон хранения**настройте параметры хранения для полной резервной копии.
    * По умолчанию выбраны все параметры. Удалите все ограничения диапазона хранения, которые вы не хотите использовать, и настройте их.
    * Минимальный срок хранения для любого типа резервной копии (полная/разностная/log) составляет семь дней.
    * Точки восстановления отмечены для хранения исходя из их диапазона хранения. Например, если выбран параметр "Ежедневно", то каждый день активируется создание только одной полной резервной копии.
    * Резервная копия за конкретный день помечается и сохраняется в зависимости от еженедельного диапазона хранения и параметра.
    * Месячный и годовой диапазоны хранения действуют аналогичным образом.

4. В меню **Политика полного резервного копирования** нажмите кнопку **ОК** , чтобы принять параметры.
5. Выберите **разностное резервное копирование** , чтобы добавить политику разностного копирования.
6. В меню **Differential Backup policy** (Политика разностной резервной копии) выберите **Включить** для открытия элементов управления периодичностью и хранением.
    * Максимально в день можно активировать одну разностную резервную копию.
    * Максимальный срок хранения разностных резервных копий составляет 180 дней. Если требуется более длительное хранение, необходимо использовать полные резервные копии.

    ![Политика разностного резервного копирования](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Добавочные резервные копии в настоящее время не поддерживаются.

7. Нажмите кнопку **ОК** , чтобы сохранить политику и вернуться в главное меню **политики резервного копирования** .
8. Выберите **резервная копия журнала** , чтобы добавить политику резервного копирования журналов транзакций.
    * В **резервной копии журнала**выберите **включить**.  Это невозможно отключить, так как SAP HANA управляет всеми резервными копиями журналов.
    * Задайте частоту и элементы управления удержания.

    > [!NOTE]
    > Резервные копии журналов начинают работать только после успешного завершения полного резервного копирования.

9. Нажмите кнопку **ОК** , чтобы сохранить политику и вернуться в главное меню **политики резервного копирования** .
10. После завершения определения политики архивации нажмите кнопку **ОК**.

## <a name="run-an-on-demand-backup"></a>Выполнение резервного копирования по требованию

Резервные копии выполняются в соответствии с расписанием политики. Резервное копирование по запросу можно выполнить следующим образом:

1. В меню хранилища щелкните **Элементы архивации**.
2. В поле **архивные элементы**выберите виртуальную машину, на которой выполняется SAP HANAная база данных, а затем щелкните **Архивация сейчас**.
3. В поле **создать резервную копию**используйте элемент управления Calendar, чтобы выбрать последний день, в который должна храниться точка восстановления. Нажмите кнопку **ОК**.
4. Отслеживайте уведомления на портале. Вы можете отслеживать ход выполнения задания на панели мониторинга хранилища в разделе **Задания резервного копирования** > **Выполняется**. В зависимости от размера базы данных создание начальной резервной копии может занять некоторое время.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Выполнение резервного копирования SAP HANA Studio в базе данных с включенным Azure Backup

Если вы хотите создать локальную резервную копию (с помощью HANA Studio) базы данных, для которой выполняется резервное копирование Azure Backup, выполните следующие действия.

1. Дождитесь завершения всех полных или резервных копий журналов для базы данных. Проверьте состояние в SAP HANA Studio или панели.
2. Отключите резервное копирование журналов и задайте в качестве каталога резервного копирования файловую систему для соответствующей базы данных.
3. Для этого дважды щелкните **системдб** > **Configuration** > **выберите База данных** > **фильтр (журнал)** .
4. Задайте для параметра **enable_auto_log_backup** значение **нет**.
5. Задайте для Log_backup_using_backint **значение false**.
6. Создайте полную резервную копию базы данных по требованию.
7. Дождитесь завершения полного резервного копирования и резервного копирования каталога.
8. Верните предыдущие параметры в Azure.
    * Задайте для параметра **enable_auto_log_backup** значение **Да**.
    * Задайте для Log_backup_using_backint **значение true**.

## <a name="next-steps"></a>Дополнительная информация

* Узнайте, как [восстановить базы данных SAP Hana, работающие на виртуальных машинах Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) .
* Узнайте, как [управлять базами данных SAP Hana, для которых выполняется резервное копирование с помощью Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
