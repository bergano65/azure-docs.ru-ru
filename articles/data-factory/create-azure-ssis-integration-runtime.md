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
ms.openlocfilehash: 18555fbffbc48594793163894c010998094b3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336228"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure

В этой статье приводятся шаги по подготовке службы интеграции серверов Сервера Azure-S'L (SSIS) на фабрике обработки данных Azure. Среда выполнения интеграции Azure-SSIS поддерживает следующие возможности:

- Выполнение пакетов, развернутых в каталоге SSIS (SSISDB), которые размещаются на сервере Базы данных SQL Azure или управляемом экземпляре (модель развертывания проектов).
- Выполнение пакетов, развернутых в файловых системах, на общих файловых ресурсах или в Файлах Azure (модель развертывания пакетов). 

После подготовки Azure-SSIS IR вы можете использовать для развертывания и запуска пакетов в Azure любые привычные средства, например SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) или средства командной строки `dtinstall`, `dtutil` и `dtexec`.

В [иК-учебнике по подготовке Azure-SSIS](tutorial-create-azure-ssis-runtime-portal.md) показано, как создать ИК Azure-SSIS через портал Azure или приложение Data Factory. В учебнике также показано, как дополнительно использовать сервер базы данных Azure s'L или управляемый экземпляр для размещения SSISDB. Эта статья расширяет учебник и описывает, как сделать эти дополнительные задачи:

- Используйте сервер базы данных Azure S'L database с правилами IP-брандмауэра/виртуальными конечными точками сетевого обслуживания или управляемым экземпляром с частной конечней точкой для размещения SSISDB. В качестве предварительного условия необходимо настроить виртуальные сетевые разрешения и настройки для присоединения к виртуальной сети.

- Используйте активную каталог Azure (Azure AD) аутентификацию с управляемой интилитацией для вашей фабрики данных для подключения к серверу базы данных Azure s-L или управляемому экземпляру. В качестве предварительного условия необходимо добавить управляемую идентификацию для фабрики данных в качестве пользователя базы данных, который может создать экземпляр SSISDB.

- Присоединитесь к ИК Azure-SSIS в виртуальную сеть или назначьте самостоятельно размещенный ИК в качестве прокси для вашего ИК Azure-SSIS для доступа к данным на месте.

В этой статье показано, как предоставить ИК Azure-SSIS с помощью портала Azure, Azure PowerShell и шаблона менеджера ресурсов Azure.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Подписка Azure**. Если у вас еще нет подписки, можно создать [бесплатную пробную](https://azure.microsoft.com/pricing/free-trial/) учетную запись.

- **Сервер базы данных Azure s-L или управляемый экземпляр (необязательно)**. Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. Фабрика данных, в свою очередь, создаст экземпляр SSISDB на этом сервере базы данных. 

  Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в SSISDB, не пересекая регионы Azure.

  Учитывайте следующие моменты:

  - В зависимости от выбранного сервера базы данных экземпляр SSISDB может быть создан от вашего имени как отдельная база данных, как часть эластичного пула или в виде управляемого экземпляра. Доступ к нему можно получить через общедоступную сеть или после присоединения к виртуальной сети. Для получения рекомендаций при выборе типа сервера баз данных для размещения SSISDB в этой статье можно ознакомиться с [единой базой данных базы данных Azure S'L, эластичным пулом и разделом управляемых экземпляров.](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) 
  
    Если сервер Базы данных SQL Azure используется с правилами брандмауэра для IP-адресов или конечными точками служб в виртуальной сети или с управляемым экземпляром и частной конечной точкой для размещения SSISDB, или вам нужен доступ к локальным данным без настройки локальной среды выполнения интеграции, необходимо присоединить среду выполнения интеграции Azure-SSIS к виртуальной сети. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

  - Убедитесь, что для сервера базы данных включен параметр **Разрешить доступ к службам Azure**. Этот параметр не применяется, если сервер Базы данных SQL Azure используется с правилами брандмауэра для IP-адресов или конечными точками служб для виртуальной сети или с управляемым экземпляром и частной конечной точкой для размещения базы данных SSISDB. Дополнительные сведения см. в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Сведения о включении этого параметра с помощью PowerShell см. в статье [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Добавьте IP-адрес клиентского компьютера или диапазон IP-адресов, который включает IP-адрес клиентского компьютера, в список IP-адресов клиента в параметрах брандмауэра для сервера базы данных. Дополнительные сведения см. в разделе [Правила брандмауэра уровня сервера Базы данных SQL Azure и уровня базы данных SQL Azure](../sql-database/sql-database-firewall-configure.md).

  - К серверу базы данных можно подключиться с использованием аутентификации SQL и учетных данных администратора сервера или аутентификации AAD и управляемого удостоверения фабрики данных. Для последнего варианта управляемое удостоверение ADF нужно добавить в группу AAD с разрешениями на доступ к серверу базы данных. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)

  - Убедитесь, что на сервере базы данных еще нет экземпляра SSISDB. При подготовке Azure-SSIS IR не поддерживается использование существующего экземпляра SSISDB.

- **Виртуальная сеть Azure Resource Manager (необязательно)**. Виртуальная сеть Azure Resource Manager нужна, если выполняется хотя бы одно из следующих условий.
  - Вы размещаете SSISDB на сервере базы данных Azure s'L database с правилами IP-брандмауэра/виртуальными конечными точками сетевого обслуживания или управляемым экземпляром с частной конечной точкой.
  - Необходимо подключиться к магазинам данных из пакетов SSIS, работающих на ИК Azure-SSIS, без настройки самостоятельной ИК-иК., хозяйничающей.

- **Azure PowerShell (по желанию)**. Если вы хотите выполнить подготовку среды выполнения интеграции Azure-SSIS с помощью скрипта PowerShell, следуйте инструкциям в разделе [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="regional-support"></a>Региональная поддержка

Для списка регионов Azure, в которых доступны фабрика данных и [Data Factory and SSIS IR availability by region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)ИК Azure-SSIS, см.

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Сравнение единой базы данных базы данных, эластичного пула и управляемого экземпляра

В следующей таблице сравниваются некоторые особенности сервера базы данных Azure s'L и управляемый экземпляр, связанный с ИК Azure-SSIR:

| Компонент | Единая база данных/упругий пул| управляемый экземпляр |
|---------|--------------|------------------|
| **Планирования** | Серверный агент S'L недоступен.<br/><br/>Смотрите [Расписание выполнения пакета в конвейере Data Factory.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)| Агент управляемой инстанции доступен. |
| **Проверка подлинности** | Можно создать экземпляр SSISDB с пользователем содержащейся базы данных, который представляет любую группу Azure AD с управляемой личностью вашей фабрики данных в качестве участника **db_owner** роли.<br/><br/>[См. проверку подлинности Включения Azure AD для создания экземпляра SSISDB на сервере базы данных Azure S'L.](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database) | Можно создать экземпляр SSISDB с пользователем базы данных, который представляет управляемую идентификацию вашей фабрики данных. <br/><br/>[См. Проверку подлинности Включения Azure AD для создания экземпляра SSISDB в управляемой экземпляре базы данных Azure S'L.](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance) |
| **Уровень служб** | При создании ИК Azure-SSIS с сервером базы данных Azure S'L можно выбрать уровень обслуживания для SSISDB. Существует несколько уровней служб. | При создании ИК Azure-SSIS с помощью управляемого экземпляра нельзя выбрать уровень службы для SSISDB. Все базы данных в управляемом экземпляре имеют один и тот же ресурс, выделенный этому экземпляру. |
| **Виртуальная сеть** | Ваш ИК Azure-SSIS может присоединиться к виртуальной сети Azure Resource Manager, если вы используете сервер базы данных Azure S'L Database с правилами IP-огнемета/виртуальными конечными точками сетевого обслуживания. | ИК Azure-SSIS может присоединиться к виртуальной сети Azure Resource Manager, если вы используете управляемый экземпляр с частной конечной точкой. Виртуальная сеть необходима, если вы не включите общедоступную точку для управляемого экземпляра.<br/><br/>Если вы присоединитесь к ИК Azure-SSIS к той же виртуальной сети, что и управляемый экземпляр, убедитесь, что ваш ИК Azure-SSIS находится в другой подсети от управляемого экземпляра. Если вы присоединитесь к ИК Azure-SSIS в другой виртуальной сети от управляемого экземпляра, мы рекомендуем либо виртуальную сеть пиринга, либо подключение от сети к сети. [Просноверное Подключение приложения к управляемому экземпляру базы данных Azure S'L.](../sql-database/sql-database-managed-instance-connect-app.md) |
| **Распределенные транзакции** | Эта функция поддерживается с помощью упругих транзакций. Транзакции координатора распределенных транзакций (MSDTC) не поддерживаются. Если пакеты SSIS используют MSDTC для координации распределенных транзакций, рассмотрите возможность перехода на эластичные транзакции для базы данных Azure S'L. Для получения дополнительной информации [см. Распределенные транзакции в облачных базах данных.](../sql-database/sql-database-elastic-transactions-overview.md) | Не поддерживается. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Используйте портал Azure для создания времени выполнения интеграции

В этом разделе для создания ИК Azure-SSIS используется портал Azure, в частности пользовательский интерфейс (UI) Data Factory (UI).

### <a name="create-a-data-factory"></a>Создание фабрики данных

Чтобы создать фабрику данных с помощью портала Azure, следуйте [пошаговым инструкциям](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). После создания выберите действие **Закрепить на панели мониторинга**, чтобы организовать быстрый доступ к ней. 

Создав фабрику данных, откройте страницу сводных сведений о ней на портале Azure. Выберите плитку **Author & Monitor,** чтобы открыть свою страницу **Let's Get started** на отдельной вкладке. Там вы можете продолжить создание ИК Azure-SSIS.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Подготовка среды выполнения интеграции Azure SSIS

1. На странице **начала работы** щелкните плитку **Configure SSIS Integration Runtime** (Настройка среды выполнения интеграции SSIS).

   ![Плитка настройки среды выполнения интеграции SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. В разделе **Общие параметры** панели **Настройка среды выполнения интеграции** выполните следующие действия.

   ![Общие параметры](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. В поле **Имя** введите имя среды выполнения интеграции.

   1. В поле **Описание** введите описание среды выполнения интеграции.

   1. В поле **Расположение** введите расположение среды выполнения интеграции. Здесь отображаются только поддерживаемые расположения. Для узла SSISDB сервера базы данных рекомендуется выбирать одинаковые расположения.

   1. Для **размера узлов**выберите размер узла в кластере времени выполнения интеграции. Отображаются только поддерживаемые размеры узла. Если вы планируете использовать пакеты с высокими требованиями к количеству компьютеров или объему памяти, выберите большой размер узла (увеличение масштаба).

   1. В поле **Номер узла** введите количество узлов в кластере среды выполнения интеграции. Отображается только поддерживаемое число узлов. Если нужно запускать несколько пакетов в параллельном режиме, выберите большой кластер с большим числом узлов (с поддержкой горизонтального масштабирования).

   1. Для **edition/License**выберите издание S'L Server для выполнения интеграции: Standard или Enterprise. Если в среде выполнения интеграции вы будете использовать расширенные функции, выберите SQL Server Enterprise.

   1. Для **экономии средств**выберите опцию Azure Hybrid Benefit для выполнения интеграции: **«Да** или **нет».** Выберите **Да,** если вы хотите принести свою собственную лицензию s'L Server с Software Assurance, чтобы извлечь выгоду из экономии средств при гибридном использовании.

   1. Нажмите кнопку **Далее**.

1. В разделе **Параметры SQL** выполните следующие шаги.

   ![Раздел "SQL Settings" (Параметры SQL)](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Установите флажок **Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs** (Создать каталог SSIS (SSISDB), размещенный на сервере базы данных SQL Azure или на управляемом экземпляре, чтобы сохранять проекты, пакеты, среды и журналы выполнения), чтобы выбрать модель развертывания для пакетов, выполняемых в Azure-SSIS IR. Вам нужно выбрать модель развертывания проектов, при которой пакеты развертываются в SSISDB, размещенной на сервере базы данных, или модель развертывания пакетов, при которой пакеты развертываются в файловых системах, на общих файловых ресурсах или в службе "Файлы Azure". 
    
      Если вы выберете флажок, вам нужно будет взять с собой собственный сервер базы данных для размещения экземпляра SSISDB, который мы создадим и управляем от вашего имени.
   
      1. В раскрывающемся списке **Подписка** выберите подписку Azure, которая соответствует серверу базы данных узла SSISDB. 

      1. В раскрывающемся списке **Расположение** выберите расположение сервера базы данных узла SSISDB. Для среды выполнения интеграции рекомендуется выбирать одинаковые расположения. 

      1. В раскрывающемся списке **Catalog Database Server Endpoint** (Конечная точка сервера базы данных каталога) выберите конечную точку сервера базы данных узла SSISDB. 
    
         В зависимости от выбранного сервера базы данных экземпляр SSISDB может быть создан от вашего имени как отдельная база данных, как часть эластичного пула или в виде управляемого экземпляра. Доступ к нему можно получить через общедоступную сеть или после присоединения к виртуальной сети. Для получения рекомендаций при выборе типа сервера баз данных для размещения SSISDB в этой статье можно ознакомиться с [единой базой данных базы данных Azure S'L, эластичным пулом и разделом управляемых экземпляров.](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) 
    
         Если выбран сервер Базы данных SQL Azure с правилами брандмауэра для IP-адресов или конечными точками служб в виртуальной сети или с управляемым экземпляром и частной конечной точкой для размещения SSISDB, или вам нужен доступ к локальным данным без настройки локальной среды выполнения интеграции, необходимо присоединить среду выполнения интеграции Azure-SSIS к виртуальной сети. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) 

      1. Установите флажок **Использовать аутентификацию AAD с управляемым удостоверением для Фабрики данных Azure**, чтобы выбрать метод проверки подлинности сервера базы данных на узле SSISDB. Это может быть аутентификация SQL или аутентификация AAD с использованием управляемого удостоверения для Фабрики данных Azure. 
    
         Если этот флажок установлен, управляемое удостоверение ADF нужно добавить в группу AAD с правами на доступ к серверу базы данных. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir) 

      1. В поле **Имя администратора** введите имя входа SQL для проверки подлинности базы данных сервера узла SSISDB. 

      1. В поле **Пароль администратора** введите пароль проверки подлинности SQL для входа сервера базы данных на узел SSISDB. 

      1. В поле **Каталог уровней служб базы данных** выберите уровень служб для сервера базы данных, на котором будет размещаться SSISDB. Это может быть уровень "Базовый", "Стандартный", "Премиум" или имя эластичного пула. 

      1. Выберите **подключение к тесту**. Если проверка пройдет успешно, щелкните **Далее**. 

1. В разделе **Дополнительные параметры** выполните следующие шаги.

   ![Дополнительные параметры](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. В раскрывающемся списке **Максимальное количество параллельных выполнений на каждом узле** выберите максимальное количество пакетов, которые будут выполняться одновременно на одном узле кластера интегрированной среды выполнения. Отображается только поддерживаемое число пакетов. Если возникла необходимость использовать больше одного ядра для запуска большого пакета с высокими требованиями к вычислительным ресурсам или памяти, выберите меньшее число. Более высокое число позволит запустить несколько небольших пакетов на одном ядре.

   1. Установите флажок **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations** (Настроить для Azure-SSIS Integration Runtime дополнительные конфигурации системы и (или) установки компонентов), чтобы добавить стандартные (быстрые) пользовательские настройки для Azure-SSIS IR. См. сведения о [пользовательской установке для среды выполнения интеграции Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Если вы выберете флажок, выполните следующие шаги.

      ![Расширенные настройки с пользовательскими настройками](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Для **контейнера пользовательской настройки SAS URI**введите SAS URI вашего контейнера, где вы храните скрипты и связанные файлы для стандартных пользовательских настроек.

      1. Для **настройки пользовательского режима Express,** выберите **Новый,** чтобы открыть пользовательскую панель **настройки Add Express,** а затем выберите любые типы в меню типа настройки **Express,** например, **выполнить команду cmdkey,** **Добавить переменную среды,** **Установить лицензированный компонент**и т.д.

         При выборе типа **лицензионного компонента Install** можно выбрать все интегрированные компоненты из наших партнеров ISV в меню выпадения **имени компонента** и при необходимости введите ключ лицензии продукта, приобретенный у них в поле **ключлицензии.**
  
         Дополнительные пользовательские настройки экспресс-экспресса будут отображаться в разделе **Расширенные настройки.** Чтобы удалить их, вы можете выбрать их флажки, а затем выбрать **Удалить**.

   1. Выберите **VNet для вашего Runtime интеграции Azure-SSIS, чтобы присоединиться, позвольте ADF создавать определенные сетевые ресурсы и по желанию привести свой собственный статический общедоступный IP-адресов** чековую коробку, чтобы выбрать, хотите ли вы присоединиться к вашей интеграции время выполнения виртуальной сети. 

      Выберите его, если вы используете сервер базы данных Azure S'L database с правилами IP-огнемета/виртуальными конечными точками сетевого обслуживания или управляемый экземпляр с частной конечной точкой для размещения SSISDB, или если вам требуется доступ к внутренним данным (т.е. у вас есть внутренние источники данных или направления в ваших пакетах SSIS) без настройки самостоятельно йОС- Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) 

      Если вы выберете флажок, выполните следующие шаги.

      ![Дополнительные параметры с использованием виртуальной сети](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. В поле **Подписка** выберите подписку Azure, в которой расположена виртуальная сеть.

      1. В поле **Расположение** введите то же расположение среды выполнения интеграции.

      1. Для **type**выберите тип виртуальной сети: классический или Azure Resource Manager. Мы рекомендуем выбрать виртуальную сеть Azure Resource Manager, поскольку классические виртуальные сети вскоре будут утиханы.

      1. В поле **Имя виртуальной сети** выберите имя вашей виртуальной сети. Это должен быть тот же, который используется для вашего сервера базы данных Azure S'L с виртуальными конечными точками сетевого обслуживания или управляемым экземпляром с частной конечной точкой для размещения SSISDB. Или он должен быть таким же, как и к вашей сети. В противном случае, это может быть любая виртуальная сеть, чтобы принести свои собственные статические общедоступные IP-адреса для Azure-SSIS ИК.

      1. В поле **Имя подсети** выберите имя подсети в виртуальной сети. Это должно быть то же самое, что используется для вашего сервера базы данных Azure S'L с виртуальными конечными точками сетевого обслуживания для размещения SSISDB. Или это должна быть другая подсеть, чем та, которая используется для управляемого экземпляра с частной конечной точкой для размещения SSISDB. В противном случае, это может быть любая подсеть, чтобы принести свои собственные статические общедоступные IP-адреса для Azure-SSIS ИК.

      1. Выберите **статичные общедоступные IP-адреса для вашего флажка Azure-SSIS Integration Runtime,** чтобы выбрать, хотите ли вы предоставить свои собственные статичные общедоступные IP-адреса для Azure-SSIS IR, чтобы вы могли разрешить их в брандмауэре для источников данных.

         Если вы выберете флажок, выполните следующие шаги.

         1. Для **первого статического общедоступного IP-адреса**выберите первый статический общедоступный IP-адрес, отвечающий требованиям, предъявляемомке к ИК Azure-SSIS. Если у вас их нет, нажмите **«Создайте новую** ссылку» для создания статических общедоступных IP-адресов на портале Azure, а затем нажмите кнопку обновления здесь, чтобы вы могли выбрать их.
      
         1. Для **второго статического общедоступного IP-адреса**выберите второй статический общедоступный IP-адрес, отвечающий требованиям, предъявляемомке к ИК Azure-SSIS. Если у вас их нет, нажмите **«Создайте новую** ссылку» для создания статических общедоступных IP-адресов на портале Azure, а затем нажмите кнопку обновления здесь, чтобы вы могли выбрать их.

   1. Установите флажок **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime** (Настроить локальную среду выполнения интеграции в качестве прокси-сервера для Azure-SSIS Integration Runtime), если вы хотите использовать локальную среду выполнения интеграции в качестве прокси-сервера для Azure-SSIS IR. См. подробнее о [настройке автономной среды выполнения интеграции в качестве прокси-сервера](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Если вы выберете флажок, выполните следующие шаги.

      ![Расширенные настройки с самостоятельной ИК](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Для **автономного запуска интеграции**выберите существующую иС-усеченную иуссамку в качестве прокси для ИК Azure-SSIS.

      1. Для **службы хранения данных**выберите существующую службу хранения Azure Blob или создайте новую службу для постановки.

      1. Для **staging Path**укажите контейнер blob в выбранной учетной записи хранения Azure Blob или оставьте его пустым для использования по умолчанию для постановки.

   1. Выберите **VNet Проверка** > **Продолжить**. 

1. В разделе **Сводка** проверьте все параметры подготовки, закладку рекомендуемых ссылок на документацию и щелкните **Готово**, чтобы создать среду выполнения интеграции.

   > [!NOTE]
   > Этот процесс должен завершиться в течение пяти минут, не считая времени на пользовательские процессы настройки. Но для присоединения ИК Azure-SSIS может потребоваться 20-30 минут.
   >
   > Если вы используете SSISDB, Фабрика данных Azure подключится к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и настройки для вашей виртуальной сети, если указано, и присоединяется к ИК Azure-SSIS к виртуальной сети.
   > 
   > При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel или Access и другим источникам данных Azure помимо тех, которые уже поддерживаются встроенными компонентами. См. сведения о других компонентах, доступных для установки, в руководстве по [пользовательской установке для среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

1. На вкладке **Подключения** перейдите к **среде выполнения интеграции**. Щелкните **Обновить**, чтобы обновить состояние.

   ![Состояние создания](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. С помощью ссылок в столбце **Действия** вы можете остановить, запустить, изменить или удалить среду выполнения интеграции. Последняя из этих ссылок позволяет просмотреть код JSON для среды выполнения интеграции. Кнопки изменения и удаления действуют только в том случае, если среда выполнения интеграции остановлена.

   ![Действия Azure SSIS IR](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Среда выполнения интеграции Azure SSIS на портале

1. В пользовательском интерфейсе фабрики данных переключитесь на вкладку **Правка** и щелкните **Подключения**. Затем откройте вкладку **Среды выполнения интеграции**, чтобы просмотреть все имеющиеся в фабрике данных среды выполнения интеграции.

   ![Просмотр существующих сред выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Выберите **новый** для создания нового ИК Azure-SSIS.

   ![Доступ к среде выполнения интеграции через меню](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. На панели **Integration Runtime Setup** (Настройка среды выполнения интеграции) выберите **Lift-and-shift existing SSIS packages to execute in Azure** (Перенос имеющихся пакетов служб SSIS по методике lift-and-shift для выполнения в Azure), а затем щелкните **Далее**.

   ![Указание типа среды выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Для остальных шагов по настройке ИК Azure-SSIS см. [Provision an Azure SSIS integration runtime](#provision-an-azure-ssis-integration-runtime)

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Используйте Azure PowerShell для создания времени выполнения интеграции

В этом разделе используется Azure PowerShell для создания ИК Azure-SSIS.

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

### <a name="sign-in-and-select-a-subscription"></a>Вопийте и выберите подписку

Добавьте следующий скрипт, чтобы войти в систему и выбрать подписку Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Проверьте подключение к серверу базы данных

Добавьте следующий скрипт для проверки сервера базы данных Azure s-L или управляемого экземпляра.

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

Добавьте следующий скрипт для автоматической настройки виртуальных сетевых разрешений и настроек для вашего времени выполнения интеграции Azure-SSIS для соединения.

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

Если вы не используете SSISDB, вы `CatalogServerEndpoint` `CatalogPricingTier`можете `CatalogAdminCredential` пропустить, и параметры.

Если вы не используете сервер базы данных Azure S'L Database с правилами IP-огнемета/виртуальными конечными точками сетевого обслуживания или управляемый `VNetId` `Subnet` экземпляр с частной конечной точкой для размещения SSISDB, или требуете доступа к данным, накоторыерым, можно опустить и параметры или передать пустые значения для них. Вы также можете опустить их, если вы наверстируете самостоятельно размещенный ИК в качестве прокси для вашего ИК Azure-SSIS для доступа к данным на месте. В противном случае, вы не можете пропустить их и должны пройти действительные значения из вашей виртуальной конфигурации сети. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

Если для размещения SSISDB используется управляемый экземпляр, можно пропустить `CatalogPricingTier` параметр или передать пустое значение для него. В противном случае вы не можете пропустить его и должны пройти действительное значение из списка поддерживаемых уровней ценообразования для базы данных Azure S'L. Для получения дополнительной [информации, см.](../sql-database/sql-database-resource-limits.md)

Если вы используете аутентификацию Azure AD с управляемой идентификацией для `CatalogAdminCredential` вашей фабрики данных для подключения к серверу базы данных, можно пропустить параметр. Но необходимо добавить управляемый идентатор для фабрики данных в группу Azure AD с разрешениями доступа к серверу базы данных. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir) В противном случае вы не можете пропустить его и должны пройти действительный объект, сформированный из вашего администратора администратора имя пользователя и пароль для проверки подлинности S'L.

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

### <a name="start-the-integration-runtime"></a>Начало выполнения интеграции

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
> Этот процесс должен завершиться в течение пяти минут, не считая времени на пользовательские процессы настройки. Но для присоединения ИК Azure-SSIS может потребоваться 20-30 минут.
>
> Если вы используете SSISDB, Фабрика данных Azure подключится к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и настройки для вашей виртуальной сети, если указано, и присоединяется к ИК Azure-SSIS к виртуальной сети.
> 
> При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel или Access и другим источникам данных Azure помимо тех, которые уже поддерживаются встроенными компонентами. См. сведения о других компонентах, доступных для установки, в руководстве по [пользовательской установке для среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Полный сценарий

Вот полный скрипт, который создает время выполнения интеграции Azure-SSIS.

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

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Используйте шаблон менеджера ресурсов Azure для создания времени выполнения интеграции

В этом разделе используется шаблон менеджера ресурсов Azure для создания времени выполнения интеграции Azure-SSIS. Вот пример пошаговых откинется:

1. Создайте файл JSON со следующим шаблоном Resource Manager Azure. Замените значения в угловых скобках (заполнителях) на собственные значения.

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

2. Чтобы развернуть шаблон диспетчера ресурсов `New-AzResourceGroupDeployment` Azure, запустите команду, показанную в следующем примере. В примере `ADFTutorialResourceGroup` — название группы ресурсов. `ADFTutorialARM.json`— это файл, содержащий определение JSON для фабрики данных и ИК Azure-SSIS.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Эта команда создает фабрику данных и ИК Azure-SSIS, но не начинает ИК.

3. Чтобы запустить ИК Azure-SSIS, запустите `Start-AzDataFactoryV2IntegrationRuntime` команду:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Этот процесс должен завершиться в течение пяти минут, не считая времени на пользовательские процессы настройки. Но для присоединения ИК Azure-SSIS может потребоваться 20-30 минут.
>
> Если вы используете SSISDB, Фабрика данных Azure подключится к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и настройки для вашей виртуальной сети, если указано, и присоединяется к ИК Azure-SSIS к виртуальной сети.
> 
> При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel или Access и другим источникам данных Azure помимо тех, которые уже поддерживаются встроенными компонентами. См. сведения о других компонентах, доступных для установки, в руководстве по [пользовательской установке для среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.

Если вы используете SSISDB, вы можете развернуть свои пакеты в него и запустить их на Вашем ИК Azure-SSIS, используя инструменты S'L Server Data Tools (SSDT) или S'L Server Management Studio (SSMS). Эти средства подключаются к серверу базы данных через конечную точку сервера. 

- Для сервера Базы данных SQL Azure используется формат конечной точки `<server name>.database.windows.net`.
- Для управляемого экземпляра с частной конечной точкой используется формат `<server name>.<dns prefix>.database.windows.net`.
- Для управляемого экземпляра с общедоступной конечной точкой используется формат `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Если вы не используете SSISDB, вы можете развернуть пакеты в файловых системах, файловых долях или файлах `dtutil`Azure и запустить их на ИК Azure-SSIS с помощью инструментов ИК Azure-SSIS `dtexec` с помощью `dtinstall`инструментов и командных строк. Дополнительные сведения см. в разделе [Развертывание пакетов на сервере служб Integration Services](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

В обоих случаях вы также можете запустить развернутые пакеты в Azure-SSIS IR, используя действие "Выполнить пакеты служб SSIS" в конвейерах Фабрики данных Azure. См. сведения о [запуске выполнения пакета SSIS с использованием действия Фабрики данных](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Дальнейшие действия

В этой документации смотрите другие темы ИК Azure-SSIS:

- [Время выполнения интеграции Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). В этой статье содержится информация о времени выполнения интеграции в целом, включая ИК Azure-SSIS.
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этой статье показано, как получить и понять информацию о вашем ИК Azure-SSIS.
- [Manage an Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md) (Управление средой выполнения интеграции Azure SSIS). В этой статье показано, как остановить, запустить или удалить ИК Azure-SSIS. Он также показывает, как масштабировать ваш Azure-SSIS ИК, добавив больше узлов.
- [Руководство. Развертывание и выполнение пакета служб SQL Server Integration Services (SSI) в Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Подключение к каталогу SSIS (SSISDB) в Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Подключение к локальным источникам данных с помощью аутентификации Windows) 
- [Планирование выполнения пакетов служб SQL Server Integration Services (SSIS), развернутых в Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)