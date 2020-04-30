---
title: Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure
description: Узнайте, как создать среду выполнения интеграции Azure–SSIS в фабрике данных Azure, чтобы запускать и развертывать пакеты Integration Services в Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 1c3f58d42b6f311e4e238dcffe7da42afd8a5306
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416726"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описаны действия по подготовке среды выполнения интеграции Azure-SQL Server Integration Services (SSIS) в фабрике данных Azure. Среда выполнения интеграции Azure-SSIS поддерживает следующие возможности:

- Выполнение пакетов, развернутых в каталоге SSIS (SSISDB), которые размещаются на сервере Базы данных SQL Azure или управляемом экземпляре (модель развертывания проектов).
- Выполнение пакетов, развернутых в файловых системах, на общих файловых ресурсах или в Файлах Azure (модель развертывания пакетов). 

После подготовки Azure-SSIS IR вы можете использовать для развертывания и запуска пакетов в Azure любые привычные средства, например SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) или средства командной строки `dtinstall`, `dtutil` и `dtexec`.

В руководстве [Azure-SSIS IR подготовки](tutorial-create-azure-ssis-runtime-portal.md) показано, как создать Azure-SSIS IR с помощью портал Azure или приложения фабрики данных. В этом учебнике также показано, как при необходимости использовать сервер базы данных SQL Azure или управляемый экземпляр для размещения SSISDB. Эта статья разворачивается в этом руководстве и описывает, как выполнять эти дополнительные задачи:

- Используйте сервер базы данных SQL Azure с правилами брандмауэра IP-адресов, конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой для размещения SSISDB. В качестве необходимого компонента необходимо настроить разрешения и параметры виртуальной сети для Azure-SSIS IR присоединиться к виртуальной сети.

- Используйте проверку подлинности Azure Active Directory (Azure AD) с управляемым удостоверением для фабрики данных, чтобы подключиться к серверу базы данных SQL Azure или управляемому экземпляру. В качестве необходимого компонента необходимо добавить управляемое удостоверение для фабрики данных в качестве пользователя базы данных, который может создать экземпляр SSISDB.

- Присоедините Azure-SSIS IR к виртуальной сети или настройте локальную IR в качестве прокси-сервера для Azure-SSIS IR доступа к данным в локальной среде.

В этой статье показано, как подготавливать Azure-SSIS IR с помощью портал Azure, Azure PowerShell и шаблона Azure Resource Manager.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Подписка Azure**. Если у вас еще нет подписки, вы можете создать [бесплатную пробную](https://azure.microsoft.com/pricing/free-trial/) учетную запись.

- **Сервер базы данных SQL Azure или управляемый экземпляр (необязательно)**. Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. Фабрика данных, в свою очередь, создаст экземпляр SSISDB на этом сервере базы данных. 

  Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в SSISDB, не пересекая регионы Azure.

  Учитывайте следующие моменты:

  - В зависимости от выбранного сервера базы данных экземпляр SSISDB может быть создан от вашего имени как отдельная база данных, как часть эластичного пула или в виде управляемого экземпляра. Доступ к нему можно получить через общедоступную сеть или после присоединения к виртуальной сети. Рекомендации по выбору типа сервера базы данных для размещения SSISDB см. в разделе [Сравнение одной базы данных, эластичного пула и управляемого экземпляра базы данных SQL Azure](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) в этой статье. 
  
    Если сервер Базы данных SQL Azure используется с правилами брандмауэра для IP-адресов или конечными точками служб в виртуальной сети или с управляемым экземпляром и частной конечной точкой для размещения SSISDB, или вам нужен доступ к локальным данным без настройки локальной среды выполнения интеграции, необходимо присоединить среду выполнения интеграции Azure-SSIS к виртуальной сети. Дополнительные сведения см. в статье [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Убедитесь, что для сервера базы данных включен параметр **Разрешить доступ к службам Azure**. Этот параметр не применяется, если сервер Базы данных SQL Azure используется с правилами брандмауэра для IP-адресов или конечными точками служб для виртуальной сети или с управляемым экземпляром и частной конечной точкой для размещения базы данных SSISDB. Дополнительные сведения см. в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Сведения о включении этого параметра с помощью PowerShell см. в статье [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Добавьте IP-адрес клиентского компьютера или диапазон IP-адресов, который включает IP-адрес клиентского компьютера, в список IP-адресов клиента в параметрах брандмауэра для сервера базы данных. Дополнительные сведения см. в разделе [Правила брандмауэра уровня сервера Базы данных SQL Azure и уровня базы данных SQL Azure](../sql-database/sql-database-firewall-configure.md).

  - К серверу базы данных можно подключиться с использованием аутентификации SQL и учетных данных администратора сервера или аутентификации AAD и управляемого удостоверения фабрики данных. Для последнего варианта управляемое удостоверение ADF нужно добавить в группу AAD с разрешениями на доступ к серверу базы данных. Дополнительные сведения см. [в статье Включение проверки подлинности Azure AD для Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Убедитесь, что на сервере базы данных еще нет экземпляра SSISDB. При подготовке Azure-SSIS IR не поддерживается использование существующего экземпляра SSISDB.

- **Виртуальная сеть Azure Resource Manager (необязательно)**. Виртуальная сеть Azure Resource Manager нужна, если выполняется хотя бы одно из следующих условий.
  - Вы размещаете SSISDB на сервере базы данных SQL Azure с правилами брандмауэра IP-адресов, конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой.
  - Необходимо подключиться к локальным хранилищам данных из пакетов служб SSIS, выполняющихся на Azure-SSIS IR, без настройки автономного IR.

- **Azure PowerShell (необязательно)**. Если вы хотите выполнить подготовку среды выполнения интеграции Azure-SSIS с помощью скрипта PowerShell, следуйте инструкциям в разделе [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="regional-support"></a>Региональная поддержка

Список регионов Azure, в которых доступны фабрика данных и Azure-SSIS IR, см. в статье [фабрика данных и доступность IR в службах SSIS по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Сравнение отдельной базы данных SQL, эластичного пула и управляемого экземпляра

В следующей таблице сравниваются некоторые функции сервера базы данных SQL Azure и управляемого экземпляра, которые связаны с Azure-ССИР IR.

| Компонент | Одна база данных или эластичный пул| управляемый экземпляр |
|---------|--------------|------------------|
| **Планирование** | Агент SQL Server недоступна.<br/><br/>См. раздел [Планирование выполнения пакета в конвейере фабрики данных](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Доступен агент Управляемый экземпляр. |
| **Аутентификация** | Экземпляр SSISDB можно создать с пользователем автономной базы данных, который представляет любую группу Azure AD с управляемым удостоверением фабрики данных в качестве члена роли **db_owner** .<br/><br/>См. раздел [Включение аутентификации Azure AD для создания экземпляра SSISDB на сервере базы данных SQL Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Экземпляр SSISDB можно создать с пользователь автономной базы данных, который представляет управляемое удостоверение фабрики данных. <br/><br/>См. раздел [Включение аутентификации Azure AD для создания экземпляра SSISDB в управляемом экземпляре базы данных SQL Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Уровень служб** | При создании Azure-SSIS IR с сервером базы данных SQL Azure можно выбрать уровень службы для SSISDB. Существует несколько уровней служб. | При создании Azure-SSIS IR с управляемым экземпляром нельзя выбрать уровень служб для SSISDB. Все базы данных в управляемом экземпляре имеют общий ресурс, выделенный для этого экземпляра. |
| **Виртуальная сеть** | Azure-SSIS IR может присоединиться к Azure Resource Manager виртуальной сети, если вы используете сервер базы данных SQL Azure с правилами брандмауэра IP-адресов или конечными точками службы виртуальной сети. | Если вы используете управляемый экземпляр с частной конечной точкой, ваш Azure-SSIS IR может присоединиться к Azure Resource Manager виртуальной сети. Виртуальная сеть необходима, если не включить общедоступную конечную точку для управляемого экземпляра.<br/><br/>При присоединении Azure-SSIS IR к той же виртуальной сети, что и управляемый экземпляр, убедитесь, что Azure-SSIS IR находится в другой подсети из управляемого экземпляра. При присоединении Azure-SSIS IR к другой виртуальной сети из управляемого экземпляра рекомендуется использовать пиринг виртуальной сети или подключение типа "сеть — сеть". См. раздел [Подключение приложения к управляемому экземпляру базы данных SQL Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Распределенные транзакции** | Эта функция поддерживается с помощью эластичных транзакций. Транзакции координатора распределенных транзакций (MSDTC) не поддерживаются. Если пакеты служб SSIS используют MSDTC для координации распределенных транзакций, рассмотрите возможность перехода на эластичные транзакции для базы данных SQL Azure. Дополнительные сведения см. в разделе [распределенные транзакции между облачными базами данных](../sql-database/sql-database-elastic-transactions-overview.md). | Не поддерживается. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Использование портал Azure для создания среды выполнения интеграции

В этом разделе используется портал Azure, в частности пользовательский интерфейс фабрики данных (UI) или приложение, для создания Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Создание фабрики данных

Чтобы создать фабрику данных с помощью портала Azure, следуйте [пошаговым инструкциям](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). После создания выберите действие **Закрепить на панели мониторинга**, чтобы организовать быстрый доступ к ней. 

Создав фабрику данных, откройте страницу сводных сведений о ней на портале Azure. Щелкните плитку " **автор & монитор** ", чтобы открыть страницу " **Начало работы** " на отдельной вкладке. В нем можно продолжить создание Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Подготовка среды выполнения интеграции Azure SSIS

1. На странице **начала работы** щелкните плитку **Configure SSIS Integration Runtime** (Настройка среды выполнения интеграции SSIS).

   ![Плитка настройки среды выполнения интеграции SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. В разделе **Общие параметры** панели **Настройка среды выполнения интеграции** выполните следующие действия.

   ![Общие параметры](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. В поле **Имя** введите имя среды выполнения интеграции.

   1. В поле **Описание** введите описание среды выполнения интеграции.

   1. В поле **Расположение** введите расположение среды выполнения интеграции. Здесь отображаются только поддерживаемые расположения. Для узла SSISDB сервера базы данных рекомендуется выбирать одинаковые расположения.

   1. В поле **размер узла**выберите размер узла в кластере среды выполнения интеграции. Отображаются только поддерживаемые размеры узла. Если вы планируете использовать пакеты с высокими требованиями к количеству компьютеров или объему памяти, выберите большой размер узла (увеличение масштаба).

   1. В поле **Номер узла** введите количество узлов в кластере среды выполнения интеграции. Отображается только поддерживаемое число узлов. Если нужно запускать несколько пакетов в параллельном режиме, выберите большой кластер с большим числом узлов (с поддержкой горизонтального масштабирования).

   1. Для **выпуска/лицензии**выберите SQL Server выпуск для среды выполнения интеграции: Standard или Enterprise. Если в среде выполнения интеграции вы будете использовать расширенные функции, выберите SQL Server Enterprise.

   1. Для параметра **сохранить деньги**выберите параметр преимущество гибридного использования Azure для среды выполнения интеграции: **Да** или **нет**. Выберите **Да** , если хотите использовать собственную лицензию на SQL Server с Software Assurance, чтобы воспользоваться преимуществами экономичного использования.

   1. Выберите **Далее**.

1. В разделе **Параметры SQL** выполните следующие шаги.

   ![Раздел "SQL Settings" (Параметры SQL)](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Установите флажок **Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs** (Создать каталог SSIS (SSISDB), размещенный на сервере базы данных SQL Azure или на управляемом экземпляре, чтобы сохранять проекты, пакеты, среды и журналы выполнения), чтобы выбрать модель развертывания для пакетов, выполняемых в Azure-SSIS IR. Вам нужно выбрать модель развертывания проектов, при которой пакеты развертываются в SSISDB, размещенной на сервере базы данных, или модель развертывания пакетов, при которой пакеты развертываются в файловых системах, на общих файловых ресурсах или в службе "Файлы Azure". 
    
      Если установить этот флажок, потребуется создать собственный сервер базы данных для размещения экземпляра SSISDB, который мы создадим и управляем от вашего имени.
   
      1. В раскрывающемся списке **Подписка** выберите подписку Azure, которая соответствует серверу базы данных узла SSISDB. 

      1. В раскрывающемся списке **Расположение** выберите расположение сервера базы данных узла SSISDB. Для среды выполнения интеграции рекомендуется выбирать одинаковые расположения. 

      1. В раскрывающемся списке **Catalog Database Server Endpoint** (Конечная точка сервера базы данных каталога) выберите конечную точку сервера базы данных узла SSISDB. 
    
         В зависимости от выбранного сервера базы данных экземпляр SSISDB может быть создан от вашего имени как отдельная база данных, как часть эластичного пула или в виде управляемого экземпляра. Доступ к нему можно получить через общедоступную сеть или после присоединения к виртуальной сети. Рекомендации по выбору типа сервера базы данных для размещения SSISDB см. в разделе [Сравнение одной базы данных, эластичного пула и управляемого экземпляра базы данных SQL Azure](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) в этой статье. 
    
         Если выбран сервер Базы данных SQL Azure с правилами брандмауэра для IP-адресов или конечными точками служб в виртуальной сети или с управляемым экземпляром и частной конечной точкой для размещения SSISDB, или вам нужен доступ к локальным данным без настройки локальной среды выполнения интеграции, необходимо присоединить среду выполнения интеграции Azure-SSIS к виртуальной сети. Дополнительные сведения см. в статье [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      1. Установите флажок **Использовать аутентификацию AAD с управляемым удостоверением для Фабрики данных Azure**, чтобы выбрать метод проверки подлинности сервера базы данных на узле SSISDB. Это может быть аутентификация SQL или аутентификация AAD с использованием управляемого удостоверения для Фабрики данных Azure. 
    
         Если этот флажок установлен, управляемое удостоверение ADF нужно добавить в группу AAD с правами на доступ к серверу базы данных. Дополнительные сведения см. [в статье Включение проверки подлинности Azure AD для Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. В поле **Имя администратора** введите имя входа SQL для проверки подлинности базы данных сервера узла SSISDB. 

      1. В поле **Пароль администратора** введите пароль проверки подлинности SQL для входа сервера базы данных на узел SSISDB. 

      1. В поле **Каталог уровней служб базы данных** выберите уровень служб для сервера базы данных, на котором будет размещаться SSISDB. Это может быть уровень "Базовый", "Стандартный", "Премиум" или имя эластичного пула. 

      1. Выберите **проверить подключение**. Если проверка пройдет успешно, щелкните **Далее**. 

1. В разделе **Дополнительные параметры** выполните следующие шаги.

   ![Дополнительные параметры](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. В раскрывающемся списке **Максимальное количество параллельных выполнений на каждом узле** выберите максимальное количество пакетов, которые будут выполняться одновременно на одном узле кластера интегрированной среды выполнения. Отображается только поддерживаемое число пакетов. Если возникла необходимость использовать больше одного ядра для запуска большого пакета с высокими требованиями к вычислительным ресурсам или памяти, выберите меньшее число. Более высокое число позволит запустить несколько небольших пакетов на одном ядре.

   1. Установите флажок **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations** (Настроить для Azure-SSIS Integration Runtime дополнительные конфигурации системы и (или) установки компонентов), чтобы добавить стандартные (быстрые) пользовательские настройки для Azure-SSIS IR. См. сведения о [пользовательской установке для среды выполнения интеграции Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Если вы установите флажок, выполните следующие действия.

      ![Дополнительные параметры с пользовательскими установками](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Для **URI SAS контейнера настраиваемой установки**введите URI SAS контейнера, в котором хранятся сценарии и связанные файлы для стандартных пользовательских настроек.

      1. Для **Экспресс выборочной установки**выберите **создать** , чтобы открыть панель " **Добавить Экспресс Выборочная настройка** ", а затем выберите любые типы в раскрывающемся меню **Экспресс Выборочная установка тип** , например **выполните команду cmdkey**, **добавьте переменную среды**, **установите лицензированный компонент**и т. д.

         Если выбран тип **компонента установить лицензированный компонент** , можно выбрать все интегрированные компоненты наших партнеров независимых поставщиков программного обеспечения в раскрывающемся меню **имя компонента** и при необходимости ввести лицензионный ключ продукта, который вы приобрели в поле **лицензионный ключ** .
  
         В разделе **Дополнительные параметры** отобразятся добавленные Экспресс пользовательские настройки. Чтобы удалить их, можно установить их флажки, а затем нажать кнопку **Удалить**.

   1. Выберите **вариант выбрать виртуальную сеть Azure-SSIS Integration Runtime для присоединиться, разрешить ADF создавать определенные сетевые ресурсы и при необходимости установить собственный статический список общедоступных IP-адресов,** чтобы выбрать, следует ли присоединить среду выполнения интеграции к виртуальной сети. 

      Выберите его, если вы используете сервер базы данных SQL Azure с правилами брандмауэра IP-адресов или конечными точками службы виртуальной сети либо управляемым экземпляром с частной конечной точкой для размещения SSISDB, или если вам требуется доступ к локальным данным (то есть к локальным источникам данных или назначениям в пакетах SSIS) без настройки автономного IR. Дополнительные сведения см. в разделе [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Если вы установите флажок, выполните следующие действия.

      ![Дополнительные параметры с использованием виртуальной сети](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. В поле **Подписка** выберите подписку Azure, в которой расположена виртуальная сеть.

      1. В поле **Расположение** введите то же расположение среды выполнения интеграции.

      1. В поле **тип**выберите тип виртуальной сети: классический или Azure Resource Manager. Рекомендуется выбрать виртуальную сеть Azure Resource Manager, так как классические виртуальные сети скоро будут считаться устаревшими.

      1. В поле **Имя виртуальной сети** выберите имя вашей виртуальной сети. Он должен быть тем же, который используется для сервера базы данных SQL Azure с конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой для размещения SSISDB. Он должен быть тем же, что и подключение к локальной сети. В противном случае может быть любая виртуальная сеть, чтобы использовать собственные статические общедоступные IP-адреса для Azure-SSIS IR.

      1. В поле **Имя подсети** выберите имя подсети в виртуальной сети. Он должен быть тем же, который используется для сервера базы данных SQL Azure с конечными точками службы виртуальной сети для размещения SSISDB. Или это должна быть другая подсеть, которая используется для управляемого экземпляра с частной конечной точкой для размещения SSISDB. В противном случае она может быть любой подсетью, чтобы использовать собственные статические общедоступные IP-адреса для Azure-SSIS IR.

      1. Установите флажок **привести статические общедоступные IP-адреса для Azure-SSIS Integration Runtime** , чтобы выбрать, следует ли использовать собственные статические общедоступные IP-адреса для Azure-SSIS IR, чтобы разрешить их в брандмауэре для источников данных.

         Если вы установите флажок, выполните следующие действия.

         1. Для **первого статического общедоступного IP-адреса**выберите первый статический общедоступный IP-адрес, соответствующий требованиям для Azure-SSIS IR. Если у вас нет, щелкните **создать новую** ссылку, чтобы создать статические общедоступные IP-адреса на портал Azure а затем нажмите кнопку обновить здесь, чтобы их можно было выбрать.
      
         1. Для **второго статического общедоступного IP-адреса**выберите второй статический общедоступный IP-адрес, соответствующий требованиям для Azure-SSIS IR. Если у вас нет, щелкните **создать новую** ссылку, чтобы создать статические общедоступные IP-адреса на портал Azure а затем нажмите кнопку обновить здесь, чтобы их можно было выбрать.

   1. Установите флажок **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime** (Настроить локальную среду выполнения интеграции в качестве прокси-сервера для Azure-SSIS Integration Runtime), если вы хотите использовать локальную среду выполнения интеграции в качестве прокси-сервера для Azure-SSIS IR. См. подробнее о [настройке автономной среды выполнения интеграции в качестве прокси-сервера](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Если вы установите флажок, выполните следующие действия.

      ![Дополнительные параметры с локальным IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Для **автономных Integration Runtime**выберите имеющуюся локальную среду IR в качестве прокси-сервера для Azure-SSIS IR.

      1. Для **связанной службы промежуточного хранилища**выберите существующую связанную службу хранилища BLOB-объектов Azure или создайте новую для промежуточного хранения.

      1. В поле **промежуточный путь**укажите контейнер больших двоичных объектов в выбранной учетной записи хранилища BLOB-объектов Azure или оставьте пустым, чтобы использовать по умолчанию для промежуточного хранения.

   1.  > Выберите **Проверка виртуальной сети****продолжить**. 

1. В разделе **Сводка** проверьте все параметры подготовки, закладку рекомендуемых ссылок на документацию и щелкните **Готово**, чтобы создать среду выполнения интеграции.

   > [!NOTE]
   > Этот процесс должен завершиться в течение пяти минут, не считая времени на пользовательские процессы настройки. Но Azure-SSIS IR присоединиться к виртуальной сети может занять 20-30 минут.
   >
   > Если вы используете SSISDB, Фабрика данных Azure подключится к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и параметры для виртуальной сети, если они указаны, и присоединяет Azure-SSIS IR к виртуальной сети.
   > 
   > При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel или Access и другим источникам данных Azure помимо тех, которые уже поддерживаются встроенными компонентами. См. сведения о других компонентах, доступных для установки, в руководстве по [пользовательской установке для среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

1. На вкладке **Подключения** перейдите к **среде выполнения интеграции**. Щелкните **Обновить**, чтобы обновить состояние.

   ![Состояние создания](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. С помощью ссылок в столбце **Действия** вы можете остановить, запустить, изменить или удалить среду выполнения интеграции. Последняя из этих ссылок позволяет просмотреть код JSON для среды выполнения интеграции. Кнопки изменения и удаления действуют только в том случае, если среда выполнения интеграции остановлена.

   ![Действия IR Azure SSIS](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Среда выполнения интеграции Azure SSIS на портале

1. В пользовательском интерфейсе фабрики данных переключитесь на вкладку **Правка** и щелкните **Подключения**. Затем откройте вкладку **Среды выполнения интеграции**, чтобы просмотреть все имеющиеся в фабрике данных среды выполнения интеграции.

   ![Просмотр существующих сред выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Выберите **создать, чтобы** создать новый Azure-SSIS IR.

   ![Доступ к среде выполнения интеграции через меню](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. На панели **Integration Runtime Setup** (Настройка среды выполнения интеграции) выберите **Lift-and-shift existing SSIS packages to execute in Azure** (Перенос имеющихся пакетов служб SSIS по методике lift-and-shift для выполнения в Azure), а затем щелкните **Далее**.

   ![Указание типа среды выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Оставшиеся действия по настройке Azure-SSIS IR см. в разделе " [предоставление среды выполнения интеграции Azure SSIS](#provision-an-azure-ssis-integration-runtime) ".

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Использование Azure PowerShell для создания среды выполнения интеграции

В этом разделе для создания Azure-SSIS IR используется Azure PowerShell.

### <a name="create-variables"></a>Создание переменных

Скопируйте и вставьте следующий скрипт. Укажите значения для переменных. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Войдите и выберите подписку.

Добавьте следующий скрипт, чтобы войти и выбрать подписку Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Проверьте подключение к серверу базы данных

Добавьте следующий скрипт для проверки сервера базы данных SQL Azure или управляемого экземпляра.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Настройка виртуальной сети

Добавьте следующий скрипт, чтобы автоматически настроить разрешения и параметры виртуальной сети для приподключения среды выполнения интеграции Azure SSIS.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure](../azure-resource-manager/management/overview.md) с помощью команды [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

Если группа ресурсов уже существует, не копируйте этот код в скрипт. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Создание фабрики данных

Чтобы создать фабрику данных, выполните следующую команду.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Создание среды выполнения интеграции

Выполните следующую команду для создания среды выполнения интеграции Azure SSIS, запускающей пакеты служб SSIS в Azure:

Если вы не используете SSISDB, можно опустить параметры `CatalogServerEndpoint`, `CatalogPricingTier`и. `CatalogAdminCredential`

Если вы не используете сервер базы данных SQL Azure с правилами брандмауэра IP-адресов, конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой для размещения SSISDB или требуется доступ к локальным данным, `VNetId` можно `Subnet` опустить параметры и или передать пустые значения для них. Их также можно опустить, если настроить локальное IR в качестве прокси-сервера для Azure-SSIS IR локального доступа к данным. В противном случае их нельзя опустить и передать допустимые значения из конфигурации виртуальной сети. Дополнительные сведения см. в статье [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Если для размещения SSISDB используется управляемый экземпляр, можно опустить `CatalogPricingTier` параметр или передать для него пустое значение. В противном случае его нельзя опустить и передать допустимое значение из списка поддерживаемых ценовых категорий для базы данных SQL Azure. Дополнительные сведения см. в разделе [ограничения ресурсов базы данных SQL](../sql-database/sql-database-resource-limits.md).

Если вы используете аутентификацию Azure AD с управляемым удостоверением для подключения фабрики данных к серверу базы данных, `CatalogAdminCredential` параметр можно опустить. Но вы должны добавить управляемое удостоверение для фабрики данных в группу Azure AD с разрешениями на доступ к серверу базы данных. Дополнительные сведения см. [в статье Включение проверки подлинности Azure AD для Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). В противном случае его нельзя опустить и передать допустимый объект, сформированный на основе имени пользователя и пароля администратора сервера для проверки подлинности SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Запуск среды выполнения интеграции

Выполните следующие команды для запуска среды выполнения интеграции SSIS Azure.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Этот процесс должен завершиться в течение пяти минут, не считая времени на пользовательские процессы настройки. Но Azure-SSIS IR присоединиться к виртуальной сети может занять 20-30 минут.
>
> Если вы используете SSISDB, Фабрика данных Azure подключится к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и параметры для виртуальной сети, если они указаны, и присоединяет Azure-SSIS IR к виртуальной сети.
> 
> При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel или Access и другим источникам данных Azure помимо тех, которые уже поддерживаются встроенными компонентами. См. сведения о других компонентах, доступных для установки, в руководстве по [пользовательской установке для среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Полный сценарий

Ниже приведен полный сценарий, создающий среду выполнения интеграции Azure SSIS.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Использование шаблона Azure Resource Manager для создания среды выполнения интеграции

В этом разделе описано, как использовать шаблон Azure Resource Manager для создания среды выполнения интеграции Azure SSIS. Ниже приведен пример пошагового руководства.

1. Создайте файл JSON со следующим шаблоном Resource Manager Azure. Замените значения в угловых скобках (заполнителями) собственными значениями.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Чтобы развернуть шаблон Azure Resource Manager, выполните `New-AzResourceGroupDeployment` команду, как показано в следующем примере. В примере `ADFTutorialResourceGroup` — это имя группы ресурсов. `ADFTutorialARM.json`— Это файл, содержащий определение JSON для фабрики данных и Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Эта команда создает фабрику данных и Azure-SSIS IR в ней, но не запускает IR.

3. Чтобы запустить Azure-SSIS IR, выполните `Start-AzDataFactoryV2IntegrationRuntime` команду:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Этот процесс должен завершиться в течение пяти минут, не считая времени на пользовательские процессы настройки. Но Azure-SSIS IR присоединиться к виртуальной сети может занять 20-30 минут.
>
> Если вы используете SSISDB, Фабрика данных Azure подключится к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и параметры для виртуальной сети, если они указаны, и присоединяет Azure-SSIS IR к виртуальной сети.
> 
> При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel или Access и другим источникам данных Azure помимо тех, которые уже поддерживаются встроенными компонентами. См. сведения о других компонентах, доступных для установки, в руководстве по [пользовательской установке для среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.

Если вы используете SSISDB, вы можете развернуть пакеты в нем и запустить их на Azure-SSIS IR с помощью средств SQL Server Data Tools (SSDT) или SQL Server Management Studio (SSMS). Эти средства подключаются к серверу базы данных через конечную точку сервера. 

- Для сервера Базы данных SQL Azure используется формат конечной точки `<server name>.database.windows.net`.
- Для управляемого экземпляра с частной конечной точкой используется формат `<server name>.<dns prefix>.database.windows.net`.
- Для управляемого экземпляра с общедоступной конечной точкой используется формат `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Если вы не используете SSISDB, вы можете развернуть пакеты в файловые системы, файловые ресурсы или файлы Azure и запустить их на Azure-SSIS IR с помощью программ командной `dtinstall`строки `dtutil`, и `dtexec` . Дополнительные сведения см. в разделе [Развертывание пакетов на сервере служб Integration Services](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

В обоих случаях вы также можете запустить развернутые пакеты в Azure-SSIS IR, используя действие "Выполнить пакеты служб SSIS" в конвейерах Фабрики данных Azure. См. сведения о [запуске выполнения пакета SSIS с использованием действия Фабрики данных](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Дальнейшие шаги

См. другие разделы Azure-SSIS IR в этой документации:

- [Среда выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). В этой статье содержатся сведения о средах выполнения интеграции в целом, включая Azure-SSIS IR.
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этой статье показано, как получить и понять сведения о Azure-SSIS IR.
- [Manage an Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md) (Управление средой выполнения интеграции Azure SSIS). В этой статье показано, как приступить к работе, запускать или удалять Azure-SSIS IR. Здесь также показано, как масштабировать Azure-SSIS IR, добавляя дополнительные узлы.
- [Руководство. Развертывание и выполнение пакета служб SQL Server Integration Services (SSI) в Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Подключение к каталогу SSIS (SSISDB) в Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Подключение к локальным источникам данных с помощью аутентификации Windows) 
- [Планирование выполнения пакетов служб SQL Server Integration Services (SSIS), развернутых в Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)