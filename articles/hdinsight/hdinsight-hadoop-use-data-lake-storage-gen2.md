---
title: Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight
description: Сведения об использовании Gen2 хранилища Озера данных Azure с кластерами Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 45b34d12fbcecbf5f6bf1225c5bb82c5385224ed
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338400"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight

Azure Gen2 хранилища Озера данных — это служба хранения облака, предназначенный для анализа больших данных, созданных в хранилище BLOB-объектов Azure. Gen2 хранилища Озера данных сочетает в себе возможности хранилища BLOB-объектов Azure и Gen1 хранилища Озера данных Azure. Итоговый служба предлагает функции из Gen1 хранилища Озера данных Azure, например семантике файлов системы, безопасности каталогов и файлов и масштабируемость, вместе с низкой стоимостью, многоуровневого хранилища, высокого уровня доступности и аварийного восстановления из хранилища BLOB-объектов Azure.

## <a name="data-lake-storage-gen2-availability"></a>Доступность Azure Data Lake Storage 2-го поколения

Gen2 хранилища Озера данных доступен в качестве хранилища для почти всех типов кластера HDInsight Azure как дополнительную учетную запись хранения и по умолчанию. Тем не менее, HBase, может иметь только одну учетную запись Gen2 хранилища Озера данных.

> [!Note] 
> После выбора Gen2 хранилища Озера данных как к **тип первичного хранилища**, вы не выберете учетную запись Gen1 хранилища Озера данных как дополнительное хранилище.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Создание кластера с Gen2 хранилища Озера данных на портале Azure

Чтобы создать кластер HDInsight, который использует Gen2 хранилища Озера данных для хранения, выполните следующие действия для настройки учетной записи Gen2 хранилища Озера данных.

### <a name="create-a-user-managed-identity"></a>Создание пользовательского управляемого удостоверения

Создайте управляемое удостоверение, назначаемое пользователем, если у вас его еще нет. См. статью [Создание, получение списка, удаление ролей и их назначение для управляемого удостоверения, назначаемого пользователем, с помощью портала Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Дополнительные сведения об управляемых рабочих удостоверений в Azure HDInsight, см. в разделе [управляемые удостоверения в Azure HDInsight](hdinsight-managed-identities.md).

![Создание управляемого удостоверения, назначаемого пользователем](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Создание учетной записи Azure Data Lake Storage 2-го поколения

Создайте учетную запись хранения Azure Data Lake Storage 2-го поколения. Убедитесь, что **иерархического пространства имен** включен параметр. Дополнительные сведения см. в [кратком руководстве Создание поддерживаемой учетной записи хранения Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Снимок экрана, на котором показано создание учетной записи хранения на портале Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Настроить разрешения для управляемого удостоверения учетной записи Gen2 хранилища Озера данных

В учетной записи хранения назначьте управляемое удостоверение пользователю с ролью **Владелец для данных больших двоичных объектов хранилища (предварительная версия)**. Дополнительные сведения см. в разделе [Управление правами доступа к данным в BLOB-объектах и очередях Azure с помощью RBAC (предварительная версия)](../storage/common/storage-auth-aad-rbac.md).

1. Войдите в свою учетную запись хранения на [портале Azure](https://portal.azure.com).
1. Выберите свою учетную запись хранения, а затем **управление доступом (IAM)** чтобы отобразить параметры управления доступом для учетной записи. Выберите вкладку **Назначения ролей**, чтобы просмотреть список назначений ролей.
    
    ![Снимок экрана с параметрами управления доступом к хранилищу](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Выберите **+ добавить назначение роли** кнопку, чтобы добавить новую роль.
1. В окне **Добавить назначение ролей** выберите роль **Участник для данных больших двоичных объектов хранилища (предварительная версия)**. Затем выберите подписку с управляемым удостоверением и учетной записью хранения. Затем найдите управляемое удостоверение, назначаемое пользователем, которое было создано ранее. Наконец, выберите управляемое удостоверение, и он будет указан в разделе **выбранные элементы**.
    
    ![Снимок экрана, на котором показано, как назначить роль RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Щелкните **Сохранить**. Назначаемое пользователем удостоверение, которое вы выбрали теперь находится в списке выбранной роли.
1. После завершения первоначальной настройки вы можете создать кластер на портале. Кластер должен быть расположен в том же регионе Azure, что и учетная запись хранения. В разделе меню создания кластера **Хранилище** выберите следующие параметры:
        
    * Для **тип первичного хранилища**выберите **Gen2 хранилища Озера данных Azure**.
    * В разделе **выберите учетную запись хранения**, найдите и выберите созданную учетную запись Gen2 хранилища Озера данных.
        
        ![Параметры хранилища для Azure Data Lake Storage 2-го поколения с Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * В разделе **удостоверений**, выберите правильную подписку, и только что созданный назначаемого пользователем управляемого удостоверения.
        
        ![Параметры удостоверения для Azure Data Lake Storage 2-го поколения с HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Один или несколько учетных записей Gen2 хранилища Озера данных можно добавить в качестве дополнительного хранилища в одном кластере. Просто повторите описанные выше действия для каждой учетной записи Gen2 хранилища Озера данных, который вы хотите добавить, используйте то же удостоверение управляемый.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Создание кластера с Gen2 хранилища Озера данных с помощью Azure CLI

Вы можете [загрузить образец файла шаблона](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) и [загрузить образец файла параметров](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Перед использованием шаблона, замените строку `<SUBSCRIPTION_ID>` своим идентификатором фактическое подписки Azure. Кроме того, замените строку `<PASSWORD>` с выбранным пароль, чтобы задать пароль, который вы используете для входа в кластер и пароль SSH.

Приведенный ниже фрагмент кода выполняет следующие начальные действия:

1. Журналы в учетную запись Azure.
1. Задает активную подписку, где будут выполняться операции create.
1. Создает новую группу ресурсов для новых операций развертывания с именем `hdinsight-deployment-rg`.
1. Создание назначаемого пользователем управляемого удостоверения с именем `test-hdinsight-msi`.
1. Добавляет расширение Azure CLI для использования функций для Gen2 хранилища Озера данных.
1. Создает новую учетную запись Gen2 хранилища Озера данных с именем `hdinsightadlsgen2`, с помощью `--hierarchical-namespace true` флаг.

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Затем войдите на портал. Добавление нового назначаемого пользователем управляемого удостоверения для **участник для данных больших двоичных объектов хранилища (Предварительная версия)** роли в учетной записи хранения, как описано в шаге 3 в разделе [с помощью портала Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Назначив роль для назначаемого пользователем управляемого удостоверения, разверните шаблон, используя следующий фрагмент кода.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Управление доступом для Gen2 хранилища Озера данных в HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Какие типы разрешений поддерживаются в Azure Data Lake Storage 2-го поколения?

Gen2 хранилища Озера данных использует модель управления доступом, который поддерживает управление доступом на основе ролей (RBAC) и POSIX по принципу управления доступом (ACL). Поддерживает Gen1 хранилища Озера данных управления доступом только для управления доступом к данным.

RBAC использует назначения ролей, чтобы эффективно применять наборы разрешений для пользователей, групп и субъектов-служб для ресурсов Azure. Как правило эти ресурсы Azure ограничены по ресурсам верхнего уровня (например, учетные записи хранения Azure). Для службы хранилища Azure, а также Gen2 хранилища Озера данных этот механизм была расширена для ресурса файловой системы.

 Дополнительные сведения о разрешениях для файла с помощью RBAC см. в разделе [управление доступом Azure на основе ролей (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Дополнительные сведения о разрешениях файл со списками управления доступом см. в разделе [списки управления доступом для файлов и каталогов](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Как контролировать доступ с моими данными в Gen2 хранилища Озера данных?

Возможность доступа к файлам в Gen2 хранилища Озера данных в кластере HDInsight осуществляется с помощью управляемых удостоверений. Управляемое удостоверение является удостоверением зарегистрирован в Azure Active Directory (Azure AD), учетные данные управляются Azure. Управляемые удостоверения не требуется для регистрации субъектов-служб в Azure AD или сохранить учетные данные, такие как сертификаты.

Службы Azure имеют два класса управляемых удостоверений: назначенное системой и назначенным пользователем. HDInsight использует назначаемого пользователем управляемого удостоверения для доступа к Gen2 хранилища Озера данных. Назначаемое пользователем управляемое удостоверение создается как изолированный ресурс Azure. При этом Azure создает удостоверение в доверенном клиенте Azure AD используемой подписки. После создания удостоверения оно может быть назначено одному или нескольким экземплярам служб Azure.

Управление жизненным циклом назначаемого пользователем удостоверения осуществляется отдельно от жизненного цикла экземпляров служб Azure, для которых оно назначено. Дополнительные сведения об управляемых удостоверениях см. в разделе [инструкции управляемого удостоверения для работы с ресурсами Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Как задать разрешения для пользователей Azure AD для запроса данных в Gen2 хранилища Озера данных с помощью Hive или других служб?

Чтобы задать разрешения для пользователей, для запроса данных, используйте группы безопасности Azure AD как назначенный основной в списках управления доступом. Не напрямую назначать разрешения доступа к файлам для отдельных пользователей или субъекты-службы. При использовании группы безопасности Azure AD для управления потоком разрешений, вы можете добавлять и удалять пользователи или субъекты-службы не повторное применение списков управления доступом к структуре каталогов. Нужно просто добавить пользователей в соответствующую группу безопасности Azure AD или удалить их из нее. Списки управления доступом не унаследован, поэтому повторное применение списков ACL требуется обновление ACL в любом файле или подкаталога.

## <a name="next-steps"></a>Дальнейшие действия

* [Use Azure Data Lake Storage Gen2 Preview with Azure HDInsight clusters](../storage/blobs/data-lake-storage-use-hdi-cluster.md) (Использование хранилища Azure Data Lake Storage 2-го поколения (предварительная версия) с кластерами Azure HDInsight)
* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Интеграция Azure HDInsight с Data Lake Storage 2-го поколения (предварительная версия) — обновление списка управления доступом и системы безопасности)
* [Общие сведения о хранилище Data Lake Storage 2-го поколения (предварительная версия)](../storage/blobs/data-lake-storage-introduction.md)
