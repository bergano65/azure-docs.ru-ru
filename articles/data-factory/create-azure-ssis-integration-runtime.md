---
title: Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure
description: Узнайте, как создать среду выполнения интеграции Azure–SSIS в фабрике данных Azure, чтобы запускать и развертывать пакеты Integration Services в Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ce688248a205981f4a4c60ad01231c0b8f6bae3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677358"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure

В этой статье описаны действия по подготовке среды выполнения интеграции Azure-SQL Server Integration Services (SSIS) в фабрике данных Azure. Azure-SSIS IR поддерживает:

- Выполнение пакетов, развернутых в каталоге служб SSIS (SSISDB), размещенном на сервере базы данных SQL Azure или управляемом экземпляре (модель развертывания проекта).
- Выполнение пакетов, развернутых в файловых системах, файловых ресурсах или файлах Azure (модель развертывания пакетов). 

После подготовки Azure-SSIS IR можно использовать знакомые средства для развертывания и запуска пакетов в Azure. К этим средствам относятся SQL Server Data Tools, SQL Server Management Studio и средства командной строки, такие как `dtinstall`, `dtutil`и `dtexec`.

В руководстве [Azure-SSIS IR подготовки](tutorial-create-azure-ssis-runtime-portal.md) показано, как создать Azure-SSIS IR с помощью портал Azure или приложения фабрики данных. В этом учебнике также показано, как при необходимости использовать сервер базы данных SQL Azure или управляемый экземпляр для размещения SSISDB. Эта статья разворачивается в этом руководстве и описывает, как выполнять эти дополнительные задачи:

- Используйте сервер базы данных SQL Azure с конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой для размещения SSISDB. В качестве необходимого компонента необходимо настроить разрешения и параметры виртуальной сети для Azure-SSIS IR присоединиться к виртуальной сети.

- Используйте проверку подлинности Azure Active Directory (Azure AD) с управляемым удостоверением для фабрики данных, чтобы подключиться к серверу базы данных SQL Azure или управляемому экземпляру. В качестве необходимого компонента необходимо добавить управляемое удостоверение для фабрики данных в качестве пользователя базы данных, который может создать экземпляр SSISDB.

- Присоедините Azure-SSIS IR к виртуальной сети или настройте локально размещенное IR в качестве прокси-сервера для Azure-SSIS IR доступа к данным в локальной среде.

В этой статье показано, как подготавливать Azure-SSIS IR с помощью портал Azure, Azure PowerShell и шаблона Azure Resource Manager.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Подписка Azure**. Если у вас еще нет подписки, вы можете создать [бесплатную пробную](https://azure.microsoft.com/pricing/free-trial/) учетную запись.
- **Сервер базы данных SQL Azure или управляемый экземпляр (необязательно)** . Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. В свою очередь, фабрика данных создаст экземпляр SSISDB на этом сервере базы данных. 

  Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в SSISDB, не пересекая регионы Azure.

  Учитывайте следующие моменты:

  - На основе выбранного сервера базы данных экземпляр SSISDB может быть создан от вашего имени как отдельная база данных, как часть эластичного пула или в управляемом экземпляре. Она может быть доступна в общедоступной сети или при присоединении к виртуальной сети. Рекомендации по выбору типа сервера базы данных для размещения SSISDB см. в разделе [Сравнение одной базы данных, эластичного пула и управляемого экземпляра базы данных SQL Azure](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) в этой статье. 
  
    Если вы используете сервер базы данных SQL Azure с конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой для размещения SSISDB или вам требуется доступ к локальным данным без настройки локальной среды IR, необходимо присоединить Azure-SSIS IR к виртуальной сети. Дополнительные сведения см. в статье [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
  - Убедитесь, что для сервера базы данных включен параметр **Разрешить доступ к службам Azure**. Этот параметр неприменим при использовании сервера базы данных SQL Azure с конечными точками службы виртуальной сети или с помощью управляемого экземпляра с частной конечной точкой для размещения SSISDB. Дополнительные сведения см. в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Сведения о включении этого параметра с помощью PowerShell см. в статье [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - Добавьте IP-адрес клиентского компьютера или диапазон IP-адресов, включающих IP-адрес клиентского компьютера, в список IP-адресов клиента в параметрах брандмауэра для сервера базы данных. Дополнительные сведения см. в разделе [Правила брандмауэра уровня сервера Базы данных SQL Azure и уровня базы данных SQL Azure](../sql-database/sql-database-firewall-configure.md).
  - Подключиться к серверу базы данных можно с помощью проверки подлинности SQL с учетными данными администратора сервера или с помощью аутентификации Azure AD с управляемым удостоверением для фабрики данных. В последнем случае необходимо добавить управляемое удостоверение для фабрики данных в группу Azure AD с разрешениями на доступ к серверу базы данных. Дополнительные сведения см. [в статье Включение проверки подлинности Azure AD для Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
  - Убедитесь, что у сервера базы данных еще нет экземпляра SSISDB. Подготовка Azure-SSIS IR не поддерживает использование существующего экземпляра SSISDB.
- **Виртуальная сеть Azure Resource Manager (необязательно)** . Виртуальная сеть Azure Resource Manager нужна, если выполняется хотя бы одно из следующих условий.
    - Вы размещаете SSISDB на сервере базы данных SQL Azure с конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой.
    - Необходимо подключиться к локальным хранилищам данных из пакетов служб SSIS, выполняющихся на Azure-SSIS IR, без настройки автономного IR.
- **Azure PowerShell (необязательно)** . Если вы хотите выполнить подготовку среды выполнения интеграции Azure-SSIS с помощью скрипта PowerShell, следуйте инструкциям в разделе [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="regional-support"></a>Региональная поддержка

Список регионов Azure, в которых доступны фабрика данных и Azure-SSIS IR, см. в статье [фабрика данных и доступность IR в службах SSIS по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Сравнение отдельной базы данных SQL, эластичного пула и управляемого экземпляра

В следующей таблице сравниваются некоторые функции сервера базы данных SQL Azure и управляемого экземпляра, которые связаны с Azure-ССИР IR.

| Функция | Одна база данных или эластичный пул| Управляемый экземпляр |
|---------|--------------|------------------|
| **Планирование** | Агент SQL Server недоступна.<br/><br/>См. раздел [Планирование выполнения пакета в конвейере фабрики данных](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Доступен агент Управляемый экземпляр. |
| **Проверка подлинности** | Экземпляр SSISDB можно создать с пользователем автономной базы данных, который представляет любую группу Azure AD с управляемым удостоверением фабрики данных в качестве члена роли **db_owner** .<br/><br/>См. раздел [Включение аутентификации Azure AD для создания экземпляра SSISDB на сервере базы данных SQL Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Экземпляр SSISDB можно создать с пользователь автономной базы данных, который представляет управляемое удостоверение фабрики данных. <br/><br/>См. раздел [Включение аутентификации Azure AD для создания экземпляра SSISDB в управляемом экземпляре базы данных SQL Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Уровень служб** | При создании Azure-SSIS IR с сервером базы данных SQL Azure можно выбрать уровень службы для SSISDB. Существует несколько уровней служб. | При создании Azure-SSIS IR с управляемым экземпляром нельзя выбрать уровень служб для SSISDB. Все базы данных в управляемом экземпляре имеют общий ресурс, выделенный для этого экземпляра. |
| **Виртуальная сеть** | Azure-SSIS IR может соединяться с Azure Resource Manager виртуальными сетями только при использовании сервера базы данных SQL Azure с конечными точками службы виртуальной сети или при необходимости доступа к локальным хранилищам данных без настройки локальных IR-устройств. | Azure-SSIS IR может присоединяться только к Azure Resource Managerным виртуальным сетям. Виртуальная сеть необходима, если не включить общедоступную конечную точку для управляемого экземпляра.<br/><br/>При присоединении Azure-SSIS IR к той же виртуальной сети, что и управляемый экземпляр, убедитесь, что Azure-SSIS IR находится в другой подсети из управляемого экземпляра. При присоединении Azure-SSIS IR к другой виртуальной сети из управляемого экземпляра рекомендуется использовать пиринг виртуальной сети или подключение типа "сеть — сеть". См. раздел [Подключение приложения к управляемому экземпляру базы данных SQL Azure](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Распределенные транзакции** | Эта функция поддерживается с помощью эластичных транзакций. Транзакции координатора распределенных транзакций (MSDTC) не поддерживаются. Если пакеты служб SSIS используют MSDTC для координации распределенных транзакций, рассмотрите возможность перехода на эластичные транзакции для базы данных SQL Azure. Дополнительные сведения см. в разделе [распределенные транзакции между облачными базами данных](../sql-database/sql-database-elastic-transactions-overview.md). | Не поддерживается. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Использование портал Azure для создания среды выполнения интеграции

В этом разделе используется портал Azure, в частности пользовательский интерфейс фабрики данных (UI) или приложение, для создания Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Создать фабрику данных

Чтобы создать фабрику данных с помощью портал Azure, следуйте пошаговым инструкциям в статье [Создание фабрики данных с помощью пользовательского интерфейса](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Установите флажок **закрепить на панели мониторинга** , чтобы разрешить быстрый доступ после его создания. 

После создания фабрики данных откройте ее страницу обзора в портал Azure. Щелкните плитку " **автор & монитор** ", чтобы открыть страницу " **Начало работы** " на отдельной вкладке. В нем можно продолжить создание Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Подготовка среды выполнения интеграции Azure SSIS

1. На странице **начала работы** щелкните плитку **Configure SSIS Integration Runtime** (Настройка среды выполнения интеграции SSIS).

   ![Плитка настройки среды выполнения интеграции SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. На странице **Общие параметры** **программы установки Integration Runtime**выполните следующие действия.

   ![Общие параметры](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    а. В поле **Имя** введите имя среды выполнения интеграции.

    b. В поле **Описание** введите описание среды выполнения интеграции.

    c. В поле **Расположение** введите расположение среды выполнения интеграции. Здесь отображаются только поддерживаемые расположения. Для узла SSISDB сервера базы данных рекомендуется выбирать одинаковые расположения.

    г) В поле **размер узла**выберите размер узла в кластере среды выполнения интеграции. Отображаются только поддерживаемые размеры узла. Выберите размер крупного узла (увеличить масштаб), если требуется выполнить много ресурсоемких или требовательных к памяти пакетов.

    д. В поле **Номер узла** введите количество узлов в кластере среды выполнения интеграции. Отображается только поддерживаемое число узлов. Выберите большой кластер с большим количеством узлов (горизонтальное развертывание), если требуется параллельное выполнение нескольких пакетов.

    Е. Для **выпуска/лицензии**выберите SQL Server выпуск для среды выполнения интеграции: Standard или Enterprise. Выберите корпоративный, если вы хотите использовать дополнительные функции в среде выполнения интеграции.

    g. Для параметра **сохранить деньги**выберите параметр преимущество гибридного использования Azure для среды выполнения интеграции: **Да** или **нет**. Выберите **Да** , если хотите использовать собственную лицензию на SQL Server с Software Assurance, чтобы воспользоваться преимуществами экономичного использования.

    h. Щелкните **Далее**.

3. На странице **параметры SQL** выполните следующие действия.

   ![Раздел "SQL Settings" (Параметры SQL)](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   а. Установите флажок **создать каталог служб SSIS...** , чтобы выбрать модель развертывания пакетов для запуска на Azure-SSIS IR. Вы выбираете модель развертывания проекта, в которой пакеты развертываются в SSISDB, размещенном на сервере базы данных, или модель развертывания пакетов, в которой пакеты развертываются в файловых системах, файловых ресурсах или файлах Azure. 
    
   Если установить этот флажок, потребуется создать собственный сервер базы данных для размещения экземпляра SSISDB, который мы создадим и управляем от вашего имени.
   
   b. В раскрывающемся списке **Подписка** выберите подписку Azure, которая соответствует серверу базы данных узла SSISDB. 

   c. В раскрывающемся списке **Расположение** выберите расположение сервера базы данных узла SSISDB. Для среды выполнения интеграции рекомендуется выбирать одинаковые расположения. 

   г) В раскрывающемся списке **Catalog Database Server Endpoint** (Конечная точка сервера базы данных каталога) выберите конечную точку сервера базы данных узла SSISDB. 
    
   На основе выбранного сервера базы данных экземпляр SSISDB может быть создан от вашего имени как отдельная база данных, как часть эластичного пула или в управляемом экземпляре. Она может быть доступна в общедоступной сети или при присоединении к виртуальной сети. Рекомендации по выбору типа сервера базы данных для размещения SSISDB см. в разделе [Сравнение одной базы данных, эластичного пула и управляемого экземпляра базы данных SQL Azure](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) в этой статье. 
    
   При выборе сервера базы данных SQL Azure с конечными точками службы виртуальной сети или с помощью управляемого экземпляра с частной конечной точкой для размещения SSISDB или при необходимости доступа к локальным данным без настройки локальных IR-устройств необходимо присоединить Azure-SSIS IR к Виртуальная сеть. Дополнительные сведения см. в статье [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   д. Установите флажок **использовать проверку подлинности AAD с управляемым удостоверением для ADF** , чтобы выбрать метод проверки подлинности для сервера базы данных, на котором будет размещен SSISDB. Вы выбираете проверку подлинности SQL или Azure AD с помощью управляемого удостоверения для вашей фабрики данных. 
    
   Если вы установите этот флажок, вам потребуется добавить управляемое удостоверение для фабрики данных в группу Azure AD с разрешениями на доступ к серверу базы данных. Дополнительные сведения см. [в статье Включение проверки подлинности Azure AD для Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

   Е. Для **имени пользователя администратора**введите имя пользователя проверки подлинности SQL для сервера базы данных, на котором будет размещен SSISDB. 

   g. В поле **пароль администратора**введите пароль проверки подлинности SQL для сервера базы данных, на котором будет размещен SSISDB. 

   h. Для **уровня службы базы данных каталога**выберите уровень служб для сервера базы данных, на котором будет размещен SSISDB. Выберите уровень "базовый", "Стандартный" или "Премиум" или выберите имя эластичного пула. 

   i. Выберите **Проверить подключение**. Если проверка прошла успешно, нажмите кнопку **Далее**. 

4. На странице **Дополнительные параметры** выполните следующие действия.

   ![Дополнительные параметры](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   а. Для параметра **Максимальное число параллельных выполнений на каждом узле**выберите максимальное количество пакетов, выполняемых одновременно для каждого узла в кластере среды выполнения интеграции. Отображается только поддерживаемое число пакетов. Выберите низкое значение, если требуется использовать более одного ядра для запуска одного большого пакета, который является вычислительным или интенсивно используемым объемом памяти. Выберите большое число, если требуется запустить один или несколько небольших пакетов в одном ядре.

   b. Для **URI SAS контейнера настраиваемой установки**при необходимости введите универсальный код ресурса (URI) для контейнера хранилища BLOB-объектов Azure, в котором хранятся скрипт установки и связанные с ним файлы. Дополнительные сведения см. в разделе [Выборочная установка для Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Установите флажок **выберите виртуальную сеть для Azure-SSIS Integration Runtime присоединиться и разрешить ADF создавать определенные сетевые ресурсы** , чтобы выбрать, следует ли присоединить среду выполнения интеграции к виртуальной сети. 

   Выберите его, если вы используете сервер базы данных SQL Azure с конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой для размещения SSISDB или если требуется доступ к локальным данным. (То есть у вас есть локальные источники данных или назначения в пакетах служб SSIS, без настройки локальной среды IR). Дополнительные сведения см. в статье [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   Если вы установите флажок, выполните следующие действия.

   ![Дополнительные параметры с использованием виртуальной сети](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

   а. В поле **Подписка** выберите подписку Azure, в которой расположена виртуальная сеть.

   b. В поле **Расположение** введите то же расположение среды выполнения интеграции.

   c. В поле **тип**выберите тип виртуальной сети: классический или Azure Resource Manager. Рекомендуется выбрать виртуальную сеть Azure Resource Manager, так как классические виртуальные сети скоро будут считаться устаревшими.

   г) В поле **Имя виртуальной сети** выберите имя вашей виртуальной сети. Эта виртуальная сеть должна быть той же, которая использовалась для сервера базы данных SQL Azure с конечными точками службы виртуальной сети или управляемого экземпляра в виртуальной сети для размещения SSISDB. Или эта виртуальная сеть должна совпадать с виртуальной сетью, подключенной к локальной сети.

   д. В поле **Имя подсети** выберите имя подсети в виртуальной сети. Это должна быть другая подсеть, используемая для управляемого экземпляра в виртуальной сети для размещения SSISDB.

6. Установите флажок **настроить автономный Integration Runtime в качестве прокси-сервера для Azure-SSIS Integration Runtime** , чтобы выбрать, следует ли настроить ЛОКАЛЬНОе IR в качестве прокси-сервера для вашей Azure-SSIS IR. Дополнительные сведения см. в разделе [Настройка автономного IR в качестве прокси-сервера](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

   Если вы установите флажок, выполните следующие действия.

   ![Дополнительные параметры с локальным IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

   а. Для Integration Runtime с локальным **размещением**выберите имеющееся ЛОКАЛЬНОе IR в качестве прокси для Azure-SSIS IR.

   b. Для **связанной службы промежуточного хранилища**выберите существующую связанную службу хранилища BLOB-объектов Azure. Или создайте новый для промежуточного хранения.

   c. В качестве **промежуточного пути**укажите контейнер больших двоичных объектов в выбранной учетной записи хранилища BLOB-объектов Azure. Или оставьте его пустым, чтобы использовать значение по умолчанию для промежуточного хранения.

7. Выберите **Проверка виртуальной сети** > **Далее**. 

8. На странице **Сводка** проверьте все параметры подготовки, закладка рекомендуемых ссылок на документацию и нажмите кнопку **Готово** , чтобы начать создание среды выполнения интеграции.

    > [!NOTE]
    > За исключением времени выборочной установки этот процесс должен завершиться в течение 5 минут. Но Azure-SSIS IR присоединиться к виртуальной сети может занять 20-30 минут.
    >
    > При использовании SSISDB служба фабрики данных будет подключаться к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и параметры для виртуальной сети, если они указаны, и присоединяет Azure-SSIS IR к виртуальной сети.
    > 
    > При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel, доступ к файлам и различным источникам данных Azure в дополнение к источникам данных, которые уже поддерживаются встроенными компонентами. Сведения о других компонентах, которые можно установить, см. [в разделе Выборочная установка Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

7. На вкладке **Подключения** перейдите к **среде выполнения интеграции**. Щелкните **Обновить**, чтобы обновить состояние.

   ![Состояние создания](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. С помощью ссылок в столбце **Действия** вы можете остановить, запустить, изменить или удалить среду выполнения интеграции. Последняя из этих ссылок позволяет просмотреть код JSON для среды выполнения интеграции. Кнопки изменения и удаления действуют только в том случае, если среда выполнения интеграции остановлена.

   ![Действия IR Azure SSIS](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Среда выполнения интеграции Azure SSIS на портале

1. В пользовательском интерфейсе фабрики данных Azure перейдите на вкладку **Правка** и выберите **подключения**. Затем перейдите на вкладку **среды выполнения интеграции** , чтобы просмотреть существующие среды выполнения интеграции в фабрике данных.

   ![Просмотр существующих сред выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Выберите **создать, чтобы** создать новый Azure-SSIS IR.

   ![Доступ к среде выполнения интеграции через меню](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

4. В окне **Integration Runtime Setup** (Настройка среды выполнения интеграции) выберите **Lift-and-shift existing SSIS packages to execute in Azure** (Перенос имеющихся пакетов служб SSIS по методике lift-and-shift для выполнения в Azure), а затем щелкните **Далее**.

   ![Указание типа среды выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Оставшиеся действия по настройке Azure-SSIS IR см. в разделе " [предоставление среды выполнения интеграции Azure SSIS](#provision-an-azure-ssis-integration-runtime) ".

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Использование Azure PowerShell для создания среды выполнения интеграции

В этом разделе для создания Azure-SSIS IR используется Azure PowerShell.

### <a name="create-variables"></a>Создание переменных

Скопируйте и вставьте следующий скрипт. Укажите значения для переменных. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
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
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"
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

Создайте [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md) с помощью команды [New-азресаурцеграуп](/powershell/module/az.resources/new-azresourcegroup) . Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

Если группа ресурсов уже существует, не копируйте этот код в скрипт. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Создать фабрику данных

Чтобы создать фабрику данных, выполните следующую команду.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Создание среды выполнения интеграции

Выполните следующую команду для создания среды выполнения интеграции Azure SSIS, запускающей пакеты служб SSIS в Azure:

Если вы не используете SSISDB, можно опустить параметры `CatalogServerEndpoint`, `CatalogPricingTier`и `CatalogAdminCredential`.

Если вы не используете сервер базы данных SQL Azure с конечными точками службы виртуальной сети или управляемым экземпляром с частной конечной точкой для размещения SSISDB или вам требуется доступ к локальным данным, можно опустить параметры `VNetId` и `Subnet` или передать пустые значения для сделать. Их также можно опустить, если настроить локальное IR в качестве прокси-сервера для Azure-SSIS IR локального доступа к данным. В противном случае их нельзя опустить и передать допустимые значения из конфигурации виртуальной сети. Дополнительные сведения см. в статье [Присоединение Azure-SSIS IR к виртуальной сети](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Если для размещения SSISDB используется управляемый экземпляр, можно опустить параметр `CatalogPricingTier` или передать для него пустое значение. В противном случае его нельзя опустить и передать допустимое значение из списка поддерживаемых ценовых категорий для базы данных SQL Azure. Дополнительные сведения см. в разделе [ограничения ресурсов базы данных SQL](../sql-database/sql-database-resource-limits.md).

Если вы используете аутентификацию Azure AD с управляемым удостоверением для подключения фабрики данных к серверу базы данных, можно опустить параметр `CatalogAdminCredential`. Но вы должны добавить управляемое удостоверение для фабрики данных в группу Azure AD с разрешениями на доступ к серверу базы данных. Дополнительные сведения см. [в статье Включение проверки подлинности Azure AD для Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). В противном случае его нельзя опустить и передать допустимый объект, сформированный на основе имени пользователя и пароля администратора сервера для проверки подлинности SQL.

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

# Add the SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
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
> За исключением времени выборочной установки этот процесс должен завершиться в течение 5 минут. Но Azure-SSIS IR присоединиться к виртуальной сети может занять 20-30 минут.
>
> При использовании SSISDB служба фабрики данных будет подключаться к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и параметры для виртуальной сети, если они указаны, и присоединяет Azure-SSIS IR к виртуальной сети.
> 
> При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel, доступ к файлам и различным источникам данных Azure в дополнение к источникам данных, которые уже поддерживаются встроенными компонентами. Сведения о других компонентах, которые можно установить, см. [в разделе Выборочная установка Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Полный сценарий

Ниже приведен полный сценарий, создающий среду выполнения интеграции Azure SSIS.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
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
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

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

# Add the SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
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

2. Чтобы развернуть шаблон Azure Resource Manager, выполните команду `New-AzResourceGroupDeployment`, как показано в следующем примере. В этом примере `ADFTutorialResourceGroup` — имя группы ресурсов. `ADFTutorialARM.json` — это файл, содержащий определение JSON для фабрики данных и Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Эта команда создает фабрику данных и Azure-SSIS IR в ней, но не запускает IR.

3. Чтобы запустить Azure-SSIS IR, выполните команду `Start-AzDataFactoryV2IntegrationRuntime`.

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> За исключением времени выборочной установки этот процесс должен завершиться в течение 5 минут. Но Azure-SSIS IR присоединиться к виртуальной сети может занять 20-30 минут.
>
> При использовании SSISDB служба фабрики данных будет подключаться к серверу базы данных для подготовки SSISDB. Он также настраивает разрешения и параметры для виртуальной сети, если они указаны, и присоединяет Azure-SSIS IR к виртуальной сети.
> 
> При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel, доступ к файлам и различным источникам данных Azure в дополнение к источникам данных, которые уже поддерживаются встроенными компонентами. Сведения о других компонентах, которые можно установить, см. [в разделе Выборочная установка Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.

Если вы используете SSISDB, вы можете развернуть пакеты в нем и запустить их на Azure-SSIS IR с помощью SQL Server Data Tools или средств SQL Server Management Studio. Эти средства подключаются к серверу базы данных через конечную точку сервера: 

- Для сервера базы данных SQL Azure с частной конечной точкой формат конечной точки сервера `<server name>.database.windows.net`.
- Для управляемого экземпляра с частной конечной точкой формат конечной точки сервера `<server name>.<dns prefix>.database.windows.net`.
- Для управляемого экземпляра с общедоступной конечной точкой формат конечной точки сервера `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Если вы не используете SSISDB, вы можете развернуть пакеты в файловых системах, файловых ресурсах или файлах Azure. Затем их можно запустить на Azure-SSIS IR с помощью средств командной строки `dtinstall`, `dtutil`и `dtexec`. Дополнительные сведения см. в разделе [Развертывание пакетов на сервере служб Integration Services](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

В обоих случаях развернутые пакеты можно также запускать на Azure-SSIS IR с помощью действия выполнить пакет служб SSIS в конвейерах фабрики данных. Дополнительные сведения см. [в разделе вызов выполнения пакета служб SSIS как действия фабрики данных первого класса](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Дальнейшие действия

См. другие разделы Azure-SSIS IR в этой документации:

- [Среда выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). В этой статье содержатся сведения о средах выполнения интеграции в целом, включая Azure-SSIS IR.
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этой статье показано, как получить и понять сведения о Azure-SSIS IR.
- [Manage an Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md) (Управление средой выполнения интеграции Azure SSIS). В этой статье показано, как приступить к работе, запускать или удалять Azure-SSIS IR. Здесь также показано, как масштабировать Azure-SSIS IR, добавляя дополнительные узлы.
- [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md) В этой статье содержатся сведения о присоединении Azure-SSIS IR к виртуальной сети.
