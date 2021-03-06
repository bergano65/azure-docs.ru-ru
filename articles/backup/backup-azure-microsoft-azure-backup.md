---
title: Использование Azure Backup Server для резервного копирования рабочих нагрузок
description: Из этой статьи вы узнаете, как подготовить среду для защиты и резервного копирования рабочих нагрузок с помощью Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 7379992eeb441372a9140621f9d90b337ad0d2e2
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172991"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Установка и обновление Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Применимо к: MABS v3. (MABS версии 2 больше не поддерживается. Если вы используете более раннюю версию, чем MABS v3, обновите ее до последней версии.)

В этой статье рассматривается подготовка среды к резервному копированию рабочих нагрузок с помощью Microsoft Azure Backup Server (MABS). Используя Azure Backup Server, вы можете создавать резервные копии рабочих нагрузок приложения, таких как виртуальные машины Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange и клиенты Windows, с помощью единой консоли.

> [!NOTE]
> Azure Backup Server позволяет защитить виртуальные машины VMware и предоставляет возможности для повышения безопасности. Установите этот продукт, как описано в следующих разделах, и последнюю версию агента Azure Backup. Дополнительные сведения о резервном копировании серверов VMware с помощью Azure Backup Server см. в статье [Резервное копирование сервера VMware с помощью сервера резервного копирования Azure](backup-azure-backup-server-vmware.md). Чтобы узнать о возможностях обеспечения безопасности, обратитесь к [документации по функциям безопасности службы архивации Azure](backup-azure-security-feature.md).
>
>

MABS, развернутые на виртуальной машине Azure, могут выполнять резервное копирование виртуальных машин в Azure, но они должны находиться в одном домене для выполнения операции резервного копирования. Процесс отката виртуальной машины Azure остается таким же, как и резервное копирование виртуальных машин в локальной среде, однако развертывание MABS в Azure имеет некоторые ограничения. Дополнительные сведения об ограничениях см. в статье [DPM как виртуальная машина Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) .

> [!NOTE]
> В Azure предусмотрены две модели развертывания, позволяющие создавать ресурсы и работать с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). Данная статья содержит сведения о восстановлении виртуальных машин, развернутых с использованием модели Resource Manager.
>
>

Azure Backup Server наследует большую часть функциональных возможностей резервного копирования рабочих нагрузок Data Protection Manager (DPM). В этой статье содержатся ссылки на документацию DPM, где рассматриваются некоторые общие функциональные возможности. Хотя Azure Backup Server предоставляет большую часть функциональных возможностей, что и DPM, Azure Backup Server не поддерживает резервное копирование на магнитную ленту, а также интеграцию с System Center.

## <a name="choose-an-installation-platform"></a>Выбор платформы для установки

Для начала работы с Azure Backup Server нужно настроить Windows Server. Он может находиться как в Azure, так в локальной среде.

### <a name="using-a-server-in-azure"></a>Использование сервера в Azure

При выборе сервера для работы Azure Backup Server рекомендуется начать с образа коллекции Windows Server 2016 Datacenter или Windows Server 2019 Datacenter. Статья [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) содержит руководство по началу работы с рекомендуемой виртуальной машиной в Azure. Оно подходит даже тем, кто только начинает знакомиться с Azure. Рекомендуемые минимальные требования для виртуальной машины сервера: Standard_A4_v2 с четырьмя ядрами и 8 ГБ ОЗУ.

Защита рабочих нагрузок с помощью Azure Backup Server имеет свои особенности. Эти особенности рассматриваются в статье [Установка DPM в виде виртуальной машины Azure](https://technet.microsoft.com/library/jj852163.aspx). Прежде чем развертывать виртуальную машину, полностью ознакомьтесь с этой статьей.

### <a name="using-an-on-premises-server"></a>Использование локального сервера

Если вы не хотите запускать базовый сервер в Azure, можно запустить сервер на виртуальной машине Hyper-V, виртуальной машине VMware или на физическом узле. Рекомендуемые минимальные требования к оборудованию сервера: два ядра и 8 ГБ ОЗУ. В следующей таблице перечислены поддерживаемые операционные системы.

| Операционная система | платформа | Sku |
|:--- | --- |:--- |
| Windows Server 2019 |64-разрядная |Standard, Datacenter, Essentials |
| Windows Server 2016 и последние пакеты обновления |64-разрядная |Standard, Datacenter, Essentials  |

С помощью дедупликации Windows Server можно выполнить дедупликацию хранилища DPM. См. дополнительные сведения об использовании [DPM и дедупликации](https://technet.microsoft.com/library/dn891438.aspx) при развертывании на виртуальных машинах Hyper-V.

> [!NOTE]
> Компонент Azure Backup Server предназначен для запуска на выделенном специализированном сервере. Azure Backup Server не может быть установлен на:
>
> * компьютере, выполняющем роль контроллера домена;
> * компьютере, на котором установлена роль сервера приложений;
> * компьютере, который является сервером управления System Center Operations Manager;
> * компьютере, на котором выполняется Exchange Server;
> * компьютере, который является узлом кластера.

Всегда присоединяйте Azure Backup Server к домену. Если вы планируете переместить сервер на другой домен, сначала установите Azure Backup Server, а затем присоедините сервер к новому домену. Перемещение имеющегося компьютера с Azure Backup Server в новый домен после развертывания *не поддерживается*.

Независимо от того, отправляются резервные копии в Azure или сохраняются в локальной среде, компонент Azure Backup Server должен быть зарегистрирован в хранилище служб восстановления.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Настройка репликации хранилища

При настройке репликации хранилища можно выбирать между геоизбыточным хранилищем и локально избыточным хранилищем. По умолчанию в качестве хранилища служб восстановления используется геоизбыточное хранилище. Если данное хранилище является основным, оставьте установленный параметр, соответствующий геоизбыточному хранилищу. Если вам нужно более дешевое и не такое надежное решение, выберите локально избыточное хранилище. Дополнительные сведения о [геоизбыточном](../storage/common/storage-redundancy-grs.md) и [локально избыточном](../storage/common/storage-redundancy-lrs.md) хранилищах см. в [обзоре репликации службы хранилища Azure](../storage/common/storage-redundancy.md).

Чтобы изменить параметр репликации хранилища:

1. В колонке **Хранилища служб восстановления** щелкните новое хранилище. В разделе **Параметры** щелкните **свойства**.
2. В окне **Свойства**в разделе **Конфигурация архивации**щелкните **Обновить**.

3. Выберите тип репликации хранилища и нажмите кнопку **сохранить**.

     ![Настройка конфигурации для нового хранилища](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Программный пакет

### <a name="downloading-the-software-package"></a>Скачивание программного пакета

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Если хранилище служб восстановления уже открыто, перейдите к шагу 3. Если хранилище служб восстановления не открыто, на портале Azure в главном меню щелкните **Обзор**.

   * В списке ресурсов введите **Службы восстановления**.
   * Как только вы начнете вводить, список отфильтруется соответствующим образом. Когда появится пункт **Хранилища служб восстановления**, щелкните его.

     ![Создание хранилища служб восстановления — шаг 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     После этого отобразится список хранилищ служб восстановления,
   * в котором нужно выбрать хранилище.

     Затем откроется панель мониторинга выбранного хранилища.

     ![Открытие колонки хранилища](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. По умолчанию открывается колонка **Параметры**. Если колонка параметров закрыта, щелкните **Параметры** , чтобы открыть ее.

    ![Открытие колонки хранилища](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Щелкните **Архивация**, чтобы открыть мастер начальной настройки.

    ![Приступая к работе со службой архивации](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    В открывшейся колонке **Начало работы с резервным копированием** будет автоматически выбран раздел **Backup Goals** (Цели архивации).

    ![Колонка целей архивации, открытая по умолчанию](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. В колонке **Backup Goals** (Цели архивации) в меню **Где выполняется рабочая нагрузка?** выберите пункт **Локально**.

    ![Выбор параметра "Локально" и рабочих нагрузок в колонке целей архивации](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    В раскрывающемся меню **что вы хотите создать резервную копию?** выберите рабочие нагрузки, которые требуется защитить с помощью Azure Backup Server, а затем нажмите кнопку **ОК**.

    Мастер **начала работы с резервным копированием** перейдет к этапу **Подготовка инфраструктуры** для настройки архивации рабочих нагрузок в Azure.

   > [!NOTE]
   > Если требуется только архивировать файлы и папки, то рекомендуется воспользоваться агентом службы архивации Azure и следовать инструкциям в статье [Первое знакомство. Резервное копирование файлов и папок с помощью службы архивации Azure с использованием модели развертывания Resource Manager](backup-try-azure-backup-in-10-mins.md). Если нужно защитить не только файлы и папки, а какие-либо рабочие нагрузки, или если в будущем планируется расширить защиту, то выберите соответствующие рабочие нагрузки.
   >
   >

    ![Изменение колонки в мастере начальной настройки](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. В открывшейся колонке **Подготовка инфраструктуры** щелкните ссылки **Скачать**, чтобы установить Azure Backup Server и скачать учетные данные хранилища. Эти учетные данные используются для регистрации Azure Backup Server в хранилище служб восстановления. В результате откроется страница Центра загрузки, где можно скачать программный пакет.

    ![Подготовка инфраструктуры для Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Выберите все файлы и нажмите кнопку **Далее**. Скачайте все файлы со страницы скачивания Microsoft Azure Backup и поместите их в одну папку.

    ![Центр загрузки 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Так как размер загружаемых файлов в совокупности составляет > 3G, по ссылке для скачивания с 10 Мбит/с загрузка может занять до 60 минут.

### <a name="extracting-the-software-package"></a>Извлечение программного пакета

После загрузки всех файлов щелкните **MicrosoftAzureBackupInstaller.exe**. Будет запущен **мастер установки Microsoft Azure Backup**, с помощью которого можно извлечь файлы установки в указанное расположение. Выполните указания мастера и нажмите кнопку **Извлечь** , чтобы начать процесс извлечения.

> [!WARNING]
> Для извлечения файлов установки требуется не менее 4 ГБ свободного места.
>
>

![Мастер установки Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Когда извлечение будет завершено, установите флажок, чтобы запустить только что извлеченный файл *setup.exe* и начать установку Microsoft Azure Backup Server, а затем нажмите кнопку **Готово**.

### <a name="installing-the-software-package"></a>Установка программного пакета

1. Щелкните **Microsoft Azure Backup**, чтобы запустить мастер установки.

    ![Мастер установки Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. На экране приветствия нажмите кнопку **Далее** . Вы перейдете в раздел *Проверки готовности* . На этом экране нажмите кнопку **Проверить**, чтобы определить, выполнены ли предварительные требования к оборудованию и программному обеспечению для Azure Backup Server. Если все обязательные условия выполнены, появится соответствующее сообщение. Нажмите кнопку **Далее** .

    ![Azure Backup Server — приветствие и проверка готовности](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Для Microsoft Azure Backup Server требуется SQL Server Enterprise. Кроме того, в пакет установки Azure Backup Server включены соответствующие двоичные файлы SQL Server. Они потребуются вам, если вы не собираетесь использовать собственный экземпляр SQL Server. При запуске новой установки Azure Backup Server выберите параметр **Установить новый экземпляр SQL Server во время этой установки** и нажмите кнопку **Проверить и установить**. После успешной установки необходимых компонентов нажмите кнопку **Далее**.

    ![Azure Backup Server — проверка SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    В случае сбоя и появления рекомендации перезагрузить компьютер, выполните инструкцию на экране и нажмите кнопку **Проверить снова**. При возникновении каких-либо проблем с конфигурацией SQL перенастройте SQL в соответствии с рекомендациями SQL и повторите попытку установки или обновления MABS с помощью существующего экземпляра SQL.

   > [!NOTE]
   > Azure Backup Server не поддерживает использование удаленного экземпляра SQL Server. Экземпляр, который использует Azure Backup Server, должен быть локальным. Если вы используете имеющийся сервер SQL Server для MABS, программа установки MABS поддерживает только использование *именованных экземпляров* SQL Server.

   **Настройка вручную**

   При использовании собственного экземпляра SQL добавьте builtin\Administrators в роль системного администратора базы данных master.

    **Конфигурация SSRS с SQL 2017**

    При использовании собственного экземпляра SQL 2017 необходимо вручную настроить службы SSRS. После настройки SSRS присвойте свойству *IsInitialized* служб SSRS значение *True*. Когда задано значение True, в MABS предполагается, что службы SSRS уже настроены и настройка служб SSRS будет пропущена.

    Для этой конфигурации SSRS используйте следующие значения:
    * Учетная запись службы: "использовать встроенную учетную запись" должна быть сетевой службой
    * URL веб-службы: "виртуальный каталог" должен быть ReportServer_\<SQLInstanceName >
    * База данных: DatabaseName должно быть ReportServer $\<SQLInstanceName >
    * URL-адрес Web Portal: "виртуальный каталог" должен быть Reports_\<SQLInstanceName >

    [Подробнее](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) о конфигурации SSRS.

    > [!NOTE]
    > Лицензирование SQL Server, используемых в качестве базы данных для MABS, регулируется [условиями Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST). В соответствии с OST, SQL Server, Объединенные с MABS, могут использоваться только в качестве базы данных для MABS.

4. Укажите расположение для установки файлов сервера Microsoft Azure Backup и нажмите кнопку **Далее**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Папка для временных файлов обязательна для резервного копирования в Azure. Убедитесь, что размер папки для временных файлов составляет по крайней мере 5 % объема данных, для которых планируется создать резервную копию в облаке. После завершения установки необходимо настроить защиту отдельных дисков. Дополнительные сведения о пулах носителей см. в статье [Настройка пулов носителей и дискового хранилища](https://technet.microsoft.com/library/hh758075.aspx).
5. Введите надежный пароль для локальных учетных записей пользователей с ограниченными правами и нажмите кнопку **Далее**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Укажите, следует ли проверять обновления с помощью *Центра обновления Майкрософт* , и нажмите кнопку **Далее**.

   > [!NOTE]
   > Рекомендуется выполнять перенаправление из Центра обновления Windows в Центр обновления Майкрософт, который предлагает обновления безопасности и другие важные обновления для Windows и других продуктов, таких как Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Просмотрите *Сводку параметров* и нажмите кнопку **Установить**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Установка выполняется поэтапно. На первом этапе агент Службы восстановления Microsoft Azure установлен на сервере. В это время мастер также проверяет наличие подключения к Интернету. При наличии подключения к Интернету можно продолжить установку. В противном случае потребуется указать сведения о прокси-сервере.

    Следующий шаг — настроить агент служб восстановления Microsoft Azure. В рамках настройки необходимо предоставить учетные данные хранилища, чтобы зарегистрировать компьютер в хранилище служб восстановления. Вам также потребуется предоставить парольную фразу для шифрования и расшифровки данных, передаваемых между Azure и локальной средой. Вы можете сгенерировать парольную фразу автоматически или ввести ее вручную. Она должна включать не менее 16 символов. Продолжайте работу мастера, пока агент не будет настроен.

    ![PreReq2 для Azure Backup Server](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. После успешного завершения регистрации сервера Microsoft Azure Backup работа мастера установки продолжается: выполняется установка и настройка SQL Server и компонентов Azure Backup Server. C установкой компонента SQL Server установка компонентов Azure Backup Server завершается.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

По завершению установки на рабочем столе должны быть созданы соответствующие значки. Дважды щелкните значок для запуска продукта.

### <a name="add-backup-storage"></a>Добавление хранилища службы архивации

Первая резервная копия хранится в хранилище, подключенном к компьютеру Azure Backup Server. Дополнительные сведения о добавлении дисков см. в статье [Настройка пулов носителей и дискового хранилища](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Хранилище службы архивации необходимо добавить, даже если вы планируете отправлять данные в Azure. В текущей архитектуре Azure Backup Server хранилище Azure Backup содержит *вторую* копию данных, а локальное хранилище — первую (и обязательную) резервную копию.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Установка и обновление агента защиты Data Protection Manager

MABS использует агент защиты System Center Data Protection Manager. [Ниже приведены действия](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) по установке агента защиты на серверах защиты.

В следующих разделах рассматривается обновление агентов защиты для клиентских компьютеров.

1. В консоли администратора Backup Server выберите **Управление** > **Агенты**.

2. На панели отображения выберите клиентские компьютеры, на которых требуется обновить агент защиты.

   > [!NOTE]
   > В столбце **Обновления агентов** отображаются сведения о доступности обновлений агента защиты для каждого защищенного компьютера. Действие **Обновить** доступно в области **Действия**, только если защищенный компьютер выбран и обновления доступны.
   >
   >

3. Чтобы установить обновленные агенты защиты на выбранных компьютерах, в области **Действия** выберите **Обновить**.

4. На клиентском компьютере, не подключенном к сети, в столбце **Состояние агента** будет отображаться элемент **Обновить ожидание**, пока компьютер не подключится к сети.

   После подключения клиентского компьютера к сети в столбце **Обновления агентов** для клиентского компьютера отобразится состояние **Обновление**.

## <a name="move-mabs-to-a-new-server"></a>Перемещение на новый сервер MABS

Ниже приведены действия по перемещению MABS на новый сервер и сохранению хранилища. Это можно сделать только в том случае, если все данные находятся в Современном хранилище резервных копий.

  > [!IMPORTANT]
  >
  > * Для нового имени сервера используется такое же имя, как и у исходного экземпляра Azure Backup Server. Нельзя изменить имя нового экземпляра Azure Backup Server, если вы хотите использовать предыдущий пул носителей и базу данных MABS (DPMDB) для хранения точек восстановления.
  > * Необходимо иметь резервную копию базы данных MABS (DPMDB). Вам понадобится восстановить базу данных.

1. На панели отображения выберите клиентские компьютеры, на которых требуется обновить агент защиты.
2. Завершите работу исходного сервера Azure Backup Server или отключите его от сети.
3. Сбросьте учетную запись компьютера в Active Directory.
4. Установите Server 2016 на новом компьютере и присвойте ему имя, совпадающее с именем компьютера исходного сервера Azure Backup Server.
5. Присоединитесь к домену.
6. Установите Azure Backup Server V3 или более поздней версии (переместите диски пула носителей MABS с старого сервера и импортируйте).
7. Восстановите DPMDB, полученную на шаге 1.
8. Подключите хранилища из исходного сервера Backup Server к новому серверу.
9. Из SQL восстановите DPMDB.
10. Из командной строки администратора на компакт-диске нового сервера в Microsoft Azure Backup установите расположение и папку bin.

    Пример пути: C:\Windows\System32 > CD "c:\Program Files\Microsoft Azure Баккуп\дпм\дпм\бин\"

11. Для службы архивации Azure выполните команду DPMSYNC-SYNC.

    Если вы добавили новые диски в пул носителей DPM вместо того, чтобы перемещать старые, выполните команду DPMSYNC-Reallocatereplica.

## <a name="network-connectivity"></a>Сетевое подключение

Для успешной работы Azure Backup Server необходимо подключение к службе Azure Backup. Чтобы проверить, подключен ли компьютер к Azure, используйте командлет ```Get-DPMCloudConnection``` в консоли PowerShell Azure Backup Server. Если выходные данные командлета имеют значение TRUE, то подключение установлено. В противном случае оно отсутствует.

При этом подписка Azure должна быть активна. Чтобы узнать состояние подписки и управлять ею, войдите на [портал подписки](https://account.windowsazure.com/Subscriptions).

Узнав состояние подключения и подписки Azure, можно использовать следующую таблицу, чтобы определить влияние на предоставляемые возможности резервного копирования и восстановления.

| Состояние подключения | Подписка Azure | Резервное копирование в Azure | Резервное копирование на диск | Восстановление из Azure | Восстановление с диска |
| --- | --- | --- | --- | --- | --- |
| Подключено |Активна |Разрешено |Разрешено |Разрешено |Разрешено |
| Подключено |Срок действия истек |Остановка |Остановка |Разрешено |Разрешено |
| Подключено |Отозвана |Остановка |Остановка |Остановлено; точки восстановления Azure удалены |Остановка |
| Подключение потеряно на 15 и более дней |Активна |Остановка |Остановка |Разрешено |Разрешено |
| Подключение потеряно на 15 и более дней |Срок действия истек |Остановка |Остановка |Разрешено |Разрешено |
| Подключение потеряно на 15 и более дней |Отозвана |Остановка |Остановка |Остановлено; точки восстановления Azure удалены |Остановка |

### <a name="recovering-from-loss-of-connectivity"></a>Восстановление после потери подключения

Если у вас есть брандмауэр или прокси-сервер, который препятствует доступу к Azure, необходимо разрешить следующие адреса домена в профиле брандмауэра или прокси-сервера:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

После восстановления подключения компьютера с Azure Backup Server к Azure доступные операции зависят от состояния подписки Azure. В таблице выше содержатся подробные сведения об операциях, которые можно выполнять после того, как состояние компьютера изменится на «Подключено».

### <a name="handling-subscription-states"></a>Сведения о состояниях подписки

Состояние подписки Azure можно изменить со *Срок действия истек* или *Отозвана* на *Активная*. Однако это имеет некоторые последствия для поведения продукта, когда состояние *неактивно*:

* Если подписка находится в состоянии *Отозвана* , во время пребывания в этом состоянии основные возможности становятся недоступными. После изменения состояния подписки на *Активная*возможности архивации и восстановления снова становятся доступными. Кроме того, можно извлечь данные резервного копирования, хранящиеся на локальном диске, если период их удержания достаточно большой. Но как только состояние подписки меняется на *Отозвана* , данные архивации в Azure безвозвратно теряются.
* Если подписка находится в состоянии *Срок действия истек*, функциональные возможности теряются только до восстановления состояния *Активная*. Если подписка пребывает в состоянии *Срок действия истек* и на этот период времени запланирована архивация, она не будет выполнена.

## <a name="upgrade-mabs"></a>Обновление MABS

Используйте следующие процедуры для обновления MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Обновление до MABS с V2 до V3

> [!NOTE]
>
> MABS V2 не является необходимым условием для установки MABS V3. Тем не менее выполнить обновление до MABS V3 можно только с MABS V2.

Вот как можно обновить MABS:

1. Чтобы выполнить обновление с MABS V2 до MABS V3, обновите свою операционную систему до Windows Server 2016 или Windows Server 2019 при необходимости.

2. Обновите сервер. Порядок действий подобен [установке](#install-and-upgrade-azure-backup-server). Тем не менее для параметров SQL вы получите возможность обновить экземпляр SQL до SQL 2017 или использовать собственный экземпляр SQL Server 2017.

   > [!NOTE]
   >
   > Не выполняйте выход, пока идет обновление вашего экземпляра SQL. Выход приведет к удалению экземпляра отчетов сервера SQL, и поэтому попытка повторного обновления MABS завершится ошибкой.

   > [!IMPORTANT]
   >
   >  В рамках обновления SQL 2017 мы выполняем резервное копирование ключей шифрования SQL и удаляем службы отчетов. После обновления SQL Server служба отчетов (14.0.6827.4788) устанавливается, а ключи шифрования восстанавливаются.
   >
   > При настройке SQL 2017 вручную см. раздел о *конфигурации служб SSRS с помощью SQL 2017* в инструкциях по установке.

3. Обновите агенты защиты на защищенных серверах.
4. Резервные копии должны выполняться и далее без необходимости перезагрузки рабочих серверов.
5. Теперь можно начать защиту данных. При обновлении до Современного хранилища резервных копий во время защиты также можно выбрать тома, в которых необходимо хранить резервные копии, и проверять наличие неподготовленного пространства. [Дополнительные сведения](backup-mabs-add-storage.md)

## <a name="troubleshooting"></a>Устранение неполадок

Если на этапе установки, резервного копирования или восстановления происходит сбой сервера Microsoft Azure Backup с указанием кода ошибки, см. информацию о кодах ошибок [в этом документе](https://support.microsoft.com/kb/3041338).
Вы также можете изучить [часто задаваемые вопросы, связанные с резервным копированием в Azure](backup-azure-backup-faq.md).

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о [подготовке среды для DPM](https://technet.microsoft.com/library/hh758176.aspx) на веб-сайте Microsoft TechNet. На этом сайте также содержатся сведения о поддерживаемых конфигурациях, при которых можно развернуть и использовать Azure Backup Server. Вы можете использовать ряд [командлетов PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) для выполнения различных операций.

Вы можете использовать эти статьи для более глубокого изучения вариантов защиты рабочих нагрузок с помощью сервера Microsoft Azure Backup.

* [Резервное копирование сервера SQL Server](backup-azure-backup-sql.md)
* [Резервное копирование сервера SharePoint](backup-azure-backup-sharepoint.md)
* [Резервное копирование альтернативного сервера](backup-azure-alternate-dpm-server.md)
