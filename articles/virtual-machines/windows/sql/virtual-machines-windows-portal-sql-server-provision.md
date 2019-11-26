---
title: Provision virtual machine with Azure portal
description: В этом руководстве описаны варианты создания виртуальных машин Windows SQL Server 2017 на портале Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 28f00db3b604534be5ff9cee79c0aacc41f066b5
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464148"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Как подготовить виртуальную машину SQL Server на платформе Windows на портале Azure

В этом руководстве рассматриваются различные варианты, доступные при создании виртуальной машины SQL Server на платформе Windows на портале Azure. В этой статье рассматривается больше параметров конфигурации, чем в [кратком руководстве по виртуальным машинам SQL Server](quickstart-sql-vm-create-portal.md), и описывается одна возможная задача подготовки. 

Используйте это руководство, чтобы создать собственную виртуальную машину SQL Server. Его также можно использовать как справочник по параметрам, доступным на портале Azure.

> [!TIP]
> Если у вас есть вопросы по виртуальным машинам SQL Server, см. раздел [часто задаваемых вопросов](virtual-machines-windows-sql-server-iaas-faq.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a id="select"></a> Образы из коллекции виртуальных машин SQL Server

При создании виртуальной машины SQL Server можно выбрать один из нескольких предварительно настроенных образов, доступных в коллекции виртуальных машин. Ниже приведены инструкции по выбору одного из образов SQL Server 2017.

1. На портале Azure в меню слева выберите **Azure SQL**. If **Azure SQL** is not in the list, select **All services**, then type Azure SQL in the search box. (Необязательно) Щелкните звезду рядом с **Azure SQL**, чтобы добавить этот элемент в избранное и область навигации слева. 
1. Щелкните **+ Добавить**, чтобы открыть страницу **Выбор варианта развертывания SQL**. You can view additional information by selecting **Show details**. 
1. Type `2017` in the SQL Server image search box on the **SQL virtual machines** tile, and then select **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** from the drop-down. 


   ![Select SQL VM image](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > В этом пошаговом руководстве используется выпуск Developer, так как это полнофункциональный выпуск SQL Server, предоставляемый бесплатно для тестирования в процессе разработки. Вы оплачиваете только стоимость выполнения виртуальной машины. Но вы можете выбрать для использования в этом пошаговом руководстве любой образ. Описание доступных образов см. в разделе [Начало работы с виртуальными машинами SQL](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Стоимость лицензий для SQL Server включена в цену секунды использования создаваемой виртуальной машины и зависит от выпуска и числа ядер. Тем не менее выпуск SQL Server Developer предоставляется бесплатно для разработки и тестирования (не для рабочей среды), а выпуск SQL Express предоставляется бесплатно для упрощенных рабочих нагрузок (требующих менее 1 ГБ памяти и 10 ГБ хранилища). Можно также использовать собственную лицензию (BYOL) и платить только за виртуальную машину. Имена таких образов содержат префикс {BYOL}. 
   >
   > Дополнительные сведения об этих параметрах см. в [руководстве по выбору ценовой категории для виртуальных машин Azure SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).


1. Нажмите кнопку **Создать**.


## <a name="1-configure-basic-settings"></a>1. Configure basic settings


На вкладке **Основные сведения** укажите следующую информацию.

* Under **Project Details**, make sure the correct subscription is selected. 
*  In the **Resource group** section, either select an existing resource group from the list or choose **Create new** to create a new resource group. Группа ресурсов — это коллекция связанных ресурсов в Azure (виртуальные машины, учетные записи хранения, виртуальные сети и т. д.). 

    ![Subscription](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Рекомендуется использовать новую группу ресурсов для тестирования или изучения процесса развертывания SQL Server в Azure. После завершения теста удалите группу ресурсов, чтобы автоматически удалить виртуальную машину и все ресурсы, связанные с этой группой ресурсов. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).


* В разделе **Сведения об экземпляре**:
    1. Enter a unique **Virtual machine name**.  
    1. Выберите расположение для параметра **Регион**. 
    1. For the purpose of this guide, leave **Availability options** set to _No infrastructure redundancy required_. См. подробнее о [регионах и доступности виртуальных машин в Azure](../../windows/availability.md). 
    1. In the **Image** list, select _Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016_.  
    1. Выберите **Изменить размер** для параметра **Размер** виртуальной машины, а затем выберите предложение **A2 Basic**. Обязательно очистите ресурсы после завершения работы с ними, чтобы предотвратить любые непредвиденные расходы. Для рабочих нагрузок изучите рекомендации по размерам машин SQL Server и их настройке в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md).

    ![Подробности об экземпляре](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> В расчетную ежемесячную стоимость, указанную в окне **Выбор размера**, не включена стоимость лицензирования SQL Server. Это только стоимость виртуальной машины. Для выпусков SQL Server Express и Developer указана итоговая расчетная стоимость. Получить сведения о других выпусках можно на странице [цен на виртуальные машины Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), где вы можете выбрать целевой выпуск SQL Server. Also see the [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) and [Sizes for virtual machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Under **Administrator account**, provide a username and a password. Пароль должен включать минимум 12 символов и соответствовать [определенным требованиям к сложности](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Учетная запись администратора](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* В разделе **Правила входящего порта** щелкните **Разрешить выбранные порты**, а затем выберите **RDP (3389)** из раскрывающегося списка. 

   ![Правила для входящих портов](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configure optional features

### <a name="disks"></a>Диски

On the **Disks** tab, configure your disk options. 

* Under **OS disk type**, select the type of disk you want for your OS from the drop-down. Premium is recommended for production systems but is not available for a Basic VM. To utilize Premium SSD, change the virtual machine size. 
* Under **Advanced**, select **Yes** under use **Managed Disks**.

   > [!NOTE]
   > Корпорация Майкрософт рекомендует использовать управляемые диски для SQL Server. Управляемые диски управляют хранилищем в фоновом режиме. Кроме того, когда виртуальные машины с управляемыми дисками находятся в одной группе доступности, Azure распределяет ресурсы хранения, чтобы обеспечить соответствующую избыточность. For more information, see [Azure Managed Disks Overview](../managed-disks-overview.md). Подробные сведения об управляемых дисках в группе доступности см. в статье [Управление доступностью виртуальных машин Windows в Azure](../manage-availability.md).

![SQL VM Disk settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Работа в сети

On the **Networking** tab, configure your networking options. 

* Create a new **virtual network**, or use an existing vNet for your SQL Server VM. Designate a **Subnet** as well. 

* Under **NIC network security group**, select either a basic security group, or the advanced security group. Choosing the basic option allows you to select inbound ports for the SQL Server VM (the same values that were configured on the **Basic** tab). Selecting the advanced option allows you to choose an existing network security group, or create a new one. 

* Вы можете изменить другие параметры сети или оставить их значения по умолчанию.

![SQL VM Networking settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Мониторинг

On the **Monitoring** tab, configure monitoring and autoshutdown. 

* Azure enables **Boot diagnostics** by default with the same storage account designated for the VM. You can change these settings here, as well as enabling **OS guest diagnostics**. 
* You can enable **System assigned managed identity** and **autoshutdown** on this tab as well. 

![SQL VM management settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configure SQL Server settings

On the **SQL Server settings** tab, configure specific settings and optimizations for SQL Server. The settings that you can configure for SQL Server include the following:

| Параметр |
| --- |
| [Соединение](#connectivity) |
| [Authentication](#authentication) (Аутентификация) |
| [Интеграция с хранилищем ключей Azure](#azure-key-vault-integration) |
| [Конфигурация хранилища](#storage-configuration) |
| [Автоматическое исправление](#automated-patching) |
| [Автоматическая архивация](#automated-backup) |
| [Службы машинного обучения](#machine-learning-services) |


### <a name="connectivity"></a>Подключение

В разделе **Подключение SQL**укажите необходимый тип доступа к экземпляру SQL Server на этой виртуальной машине. В рамках этого пошагового руководства выберите **Общедоступный (Интернет)** для подключения к SQL Server с компьютеров или из служб в Интернете. With this option selected, Azure automatically configures the firewall and the network security group to allow traffic on the port selected.

> [!TIP]
> По умолчанию сервер SQL Server ожидает передачи данных через стандартный порт **1433**. Для повышения безопасности измените порт в предыдущем диалоговом окне, чтобы ожидать передачи данных через нестандартный порт, например 1401. После изменения порта необходимо выполнить подключение, используя этот порт, с помощью любого клиентского инструмента, например SSMS.

![SQL VM Security](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Чтобы подключиться к SQL Server через Интернет, также необходимо включить проверку подлинности SQL Server, как описано в следующем разделе.

Если вы не хотите включать подключения к ядру СУБД через Интернет, выберите один из следующих вариантов:

* **Локальное (только в виртуальной машине)** — для подключения к SQL Server только в пределах самой виртуальной машины.
* **Частное (в виртуальной сети)** — для подключения к SQL Server с машин или из служб в той же виртуальной сети.

Как правило, чтобы повысить уровень безопасности, необходимо выбрать наиболее подходящий тип подключения для выполнения вашего сценария. Но все параметры можно защитить с помощью правил группы безопасности сети и проверки подлинности Windows или SQL. Вы можете изменить группу безопасности сети после создания виртуальной машины. Дополнительные сведения см. в статье [Вопросы безопасности SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Authentication

If you require SQL Server Authentication, click **Enable** under **SQL authentication** on the **SQL Server settings** tab.

![Проверка подлинности SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Если вы планируете предоставить доступ к SQL Server через Интернет (т. е. включаете параметр общедоступного подключения), включите аутентификацию SQL здесь. Для общего доступа к SQL Server необходимо использовать проверку подлинности SQL.

Если вы включаете проверку подлинности SQL Server, укажите **имя для входа** и **пароль**. This login name is configured as a SQL Server Authentication login and member of the **sysadmin** fixed server role. Дополнительные сведения о режимах проверки подлинности см. в [этой статье](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Если проверка подлинности SQL Server не включена, подключиться к экземпляру SQL Server можно с помощью локальной учетной записи администратора на виртуальной машине.


### <a name="azure-key-vault-integration"></a>Интеграция с хранилищем ключей Azure

To store security secrets in Azure for encryption, select **SQL Server settings**, and scroll down to  **Azure key vault integration**. Select **Enable** and fill in the requested information. 

![Интеграция с хранилищем ключей Azure](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

В следующей таблице перечислены параметры, необходимые для настройки интеграции с хранилищем ключей Azure.

| ПАРАМЕТР | ОПИСАНИЕ | ПРИМЕР |
| --- | --- | --- |
| **URL-адрес хранилища ключей** |Расположение хранилища ключей. |https:\//contosokeyvault.vault.azure.net/ |
| **Имя субъекта** |Имя субъекта-службы Azure Active Directory. Этот имя также называется идентификатором клиента. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Секрет субъекта** |Секрет субъекта-службы Azure Active Directory. Этот секрет также называется секретом клиента. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Имя учетных данных** |**Учетное имя**: интеграция AKV создает учетные данные в рамках SQL Server, позволяя виртуальной машине иметь доступ к хранилищу ключей. Выберите имя для этих учетных данных. |mycred1 |

Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Конфигурация хранилища

On the **SQL Server settings** tab, under **Storage configuration**, select **Change configuration** to open the Performance Optimized Storage Configuration page and specify the storage requirements.

![SQL VM Storage configuration](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

В разделе **Storage optimized for**(Оптимизация хранилища) выберите один из следующих вариантов.

* **Общая** — это значение по умолчанию, которое поддерживает большинство рабочих нагрузок.
* **Transactional processing** optimizes the storage for traditional database OLTP workloads.
* **Хранилище данных** оптимизирует хранилище для рабочих нагрузок аналитики и отчетов.

![SQL VM Storage configuration](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

You can choose to leave the values at default, or you can manually change the storage topology to suit your IOPS needs. For more information, see [storage configuration](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server License
If you're a Software Assurance customer, you can utilize the [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) to bring your own SQL Server license and save on resources. 

![SQL VM License](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Автоматическое исправление

**Automated patching** включен по умолчанию. Автоматическая установка исправлений позволяет Azure автоматически исправлять SQL Server и операционную систему. Укажите день недели, время и длительность периода обслуживания. Azure устанавливает исправления в период обслуживания. Расписание периода обслуживания использует для определения времени региональные параметры VM. Если вы не хотите, чтобы платформа Azure автоматически устанавливала исправления для SQL Server и операционной системы, выберите вариант **Отключить**.  

![SQL VM automated patching](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Автоматическая архивация

Включите автоматическую архивацию для всех баз данных в разделе **Автоматическая архивация**. По умолчанию автоматическая архивация отключена.

Во время включения автоматического резервного копирования SQL можно настроить следующие параметры:

* срок хранения (в днях) для резервных копий;
* учетная запись хранения, используемая для резервных копий;
* параметр шифрования и пароль для резервных копий.
* архивация системных баз данных;
* настройка расписания архивации баз данных.

Для шифрования резервной копии щелкните **Включить**. Затем введите **пароль**. Azure создает сертификат для шифрования резервных копий и использует указанный пароль для защиты этого сертификата. By default the schedule is set automatically, but you can create a manual schedule by selecting **Manual**. 

![SQL VM automated backups](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Дополнительную информацию см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md).


### <a name="machine-learning-services"></a>Служба машинного обучения

You have the option to enable [Machine Learning Services](/sql/advanced-analytics/). This option enables you to use machine learning with Python and R in SQL Server 2017. Select **Enable** on the **SQL Server Settings** window.


## <a name="4-review--create"></a>4. Review + create

На вкладке **Отзыв и создание** просмотрите сводные данные и выберите **Создать**, чтобы создать SQL Server, группу ресурсов и ресурсы, указанные для этой виртуальной машины.

Развертывание можно отслеживать на портале Azure. Если нажать кнопку **Уведомления** в верхней части окна, будут показаны основные сведения о состоянии развертывания.

> [!NOTE]
> Чтобы предоставить вам сведения о времени, необходимом для выполнения развертывания, в нашем сценарии виртуальная машина SQL была развернута в восточной части США с параметрами по умолчанию. На выполнение тестового развертывания требуется около 12 минут. Время развертывания зависит от региона и выбранных параметров.

## <a id="remotedesktop"></a> Открытие виртуальных машин с помощью удаленного рабочего стола

Выполните следующие действия, чтобы подключиться к виртуальной машине SQL Server через удаленный рабочий стол.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

После подключения к виртуальной машине SQL Server вы можете запустить SQL Server Management Studio и подключиться с проверкой подлинности Windows, используя свои учетные данные локального администратора. Если включена проверка подлинности SQL Server, можно также подключиться с проверкой подлинности SQL, используя имя пользователя и пароль SQL, настроенные во время подготовки.

Доступ к машине позволяет напрямую изменять настройки компьютера и SQL Server в зависимости от ваших требований. Например, можно настроить параметры брандмауэра или изменить параметры конфигурации SQL Server.

## <a id="connect"></a> Удаленное подключение к SQL Server

В рамках этого пошагового руководства для виртуальной машины используется **открытый** доступ и **аутентификация SQL Server**. Эти параметры позволяют автоматически настроить виртуальную машину для получения доступа к экземпляру SQL Server из любого клиента через Интернет (при условии, что будет использоваться правильное имя пользователя SQL).

> [!NOTE]
> Если вы не выбрали открытый доступ во время подготовки, можно изменить параметры подключения к SQL на портале после подготовки. Дополнительные сведения см. в разделе [об изменении параметров подключения SQL](virtual-machines-windows-sql-connect.md#change).

В следующих разделах описано, как подключиться к вашему экземпляру виртуальной машины SQL Server через Интернет.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > This example uses the common port 1433. However, this value will need to be modified if a different port (such as 1401) was specified during the deployment of the SQL Server VM. 


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании SQL Server в Azure см. в статье [Приступая к работе с SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md) и [Часто задаваемые вопросы об SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-faq.md).
