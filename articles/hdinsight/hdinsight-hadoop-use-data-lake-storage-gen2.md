---
title: Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight
description: Сведения об использовании Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: 1d43c7b6dd1bdec0a2507d8ce1a3883f5ce31a39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479444"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight

Azure Data Lake Storage 2-го поколения (Data Lake Storage) — это набор возможностей для аналитики больших данных, созданных на основе хранилища BLOB-объектов Azure. Data Lake Storage 2-го поколения — это результат объединения возможностей хранилища BLOB-объектов Azure и Azure Data Lake Storage 1-го поколения. В результате получилась служба с функциями Azure Data Lake Storage 1-го поколения, такими как семантика файловой системы, каталоги, защита и масштабирование на уровне файлов, а также экономичность, многоуровневая архитектура, возможности высокого уровня доступности и аварийного восстановления хранилища BLOB-объектов Azure.

## <a name="data-lake-storage-gen2-availability"></a>Доступность Azure Data Lake Storage 2-го поколения

Решение "Azure Data Lake Storage 2-го поколения" доступно в качестве варианта хранилища для почти всех типов кластеров Azure HDInsight, как дополнительная учетная запись и учетная запись хранения по умолчанию. Тем не менее в HBase может быть только одна учетная запись Azure Data Lake Storage 2-го поколения.

> [!Note] 
> Если выбрать Data Lake Storage 2-го поколения в качестве **основного типа хранилища**, учетную запись Data Lake Storage 1-го поколения невозможно будет указать как дополнительное хранилище.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Создание кластера HDInsight с Data Lake Storage 2-го поколения

Чтобы создать кластер HDInsight, использующий для хранения Data Lake Storage 2-го поколения, следуйте приведенным ниже инструкциям и создайте учетную запись Data Lake Storage 2-го поколения с правильными настройками.

1. Создайте управляемое удостоверение, назначаемое пользователем, если у вас его еще нет. См. статью [Создание, получение списка, удаление ролей и их назначение для управляемого удостоверения, назначаемого пользователем, с помощью портала Azure](/../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal#create-a-user-assigned-managed-identity.md).

    ![Создание управляемого удостоверения, назначаемого пользователем](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Создайте учетную запись хранения Azure Data Lake Storage 2-го поколения. Убедитесь, что параметр **HFS** включен. См. [Краткое руководство. Создание поддерживаемой учетной записи хранения Azure Data Lake Storage 2-го поколения](/../storage/blobs/data-lake-storage-quickstart-create-account.md) для получения дополнительных сведений.

    ![Снимок экрана, на котором показано создание учетной записи хранения на портале Azure](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. В учетной записи хранения назначьте управляемое удостоверение пользователю с ролью **Участник для данных больших двоичных объектов хранилища (предварительная версия)**. Дополнительные сведения см. в статье [Управление правами доступа к данным BLOB-объектов и очередей Azure с помощью RBAC (предварительная версия](/../storage/common/storage-auth-aad-rbac#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal.md).

    1. Войдите в свою учетную запись хранения на [портале Azure](https://portal.azure.com).
    1. Выберите свою учетную запись хранения, затем — **Управление доступом (IAM)**, чтобы отобразить параметры управления доступом для учетной записи. Выберите вкладку **Назначения ролей**, чтобы просмотреть список назначений ролей.
    
        ![Снимок экрана с параметрами управления доступом хранилища](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. Нажмите кнопку **Добавить назначение ролей**, чтобы добавить новую роль.
    1. В окне **Добавить назначение ролей** выберите роль **Участник для данных больших двоичных объектов хранилища (предварительная версия)**. Затем выберите подписку с управляемым удостоверением и учетной записью хранения. Затем найдите управляемое удостоверение, назначаемое пользователем, которое было создано ранее. Наконец выберите управляемое удостоверение. Оно будет отображаться в поле **Выбранные элементы**.
    
        ![Снимок экрана, на котором показано, как назначить роль RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. Выберите команду **Сохранить**. Назначаемое пользователем удостоверение, которое вы выбрали, теперь отображается в роли **участника**.

    1. После завершения первоначальной настройки вы можете создать кластер на портале. Кластер должен быть расположен в том же регионе Azure, что и учетная запись хранения. В разделе меню создания кластера **Хранилище** выберите следующие параметры:
        
        * В поле **Тип первичного хранилища** выберите **Azure Data Lake Storage 2-го поколения**.
        * В поле **Выберите учетную запись хранения** найдите и выберите созданную учетную запись хранения Azure Data Lake Storage 2-го поколения.
        
            ![Параметры хранилища для Azure Data Lake Storage 2-го поколения с Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * В разделе **Удостоверение** выберите требуемую подписку и недавно созданное управляемое удостоверение, назначаемое пользователем.
        
            ![Параметры удостоверения для Azure Data Lake Storage 2-го поколения с HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Служба контроля доступа для Azure Data Lake Storage 2-го поколения в HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Какие типы разрешений поддерживаются в Azure Data Lake Storage 2-го поколения?

В Azure Data Lake Storage 2-го поколения реализована модель управления доступом, которая поддерживает как управление доступом на основе ролей в Azure (RBAC), так и POSIX-подобные списки управления доступом (ACL). В Data Lake Storage 1-го поколения поддерживаются только списки управления доступом для управления доступом к данным.

Управление доступом на основе ролей в Azure (RBAC) предусматривает использование назначений ролей для эффективного применения наборов разрешений к пользователям, группам и субъектам-службам для ресурсов Azure. Как правило, эти ресурсы Azure ограничиваются ресурсами верхнего уровня (например, учетными записями хранения Azure). Для службы хранилища Azure и Azure Data Lake Storage 2-го поколения этот механизм расширен до ресурса файловой системы.

 Дополнительные сведения о разрешениях для файлов с использованием RBAC см. в разделе [Управление доступом на основе ролей в Azure (RBAC)](/../storage/blobs/data-lake-storage-access-control#azure-role-based-access-control-rbac.md).

Дополнительные сведения о разрешениях для файлов с использованием списков управления доступом см. в разделе [Списки управления доступом для файлов и каталогов](/../storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories.md).


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Контроль доступа к данным в Data Lake Storage 2-го поколения

Доступ кластера HDInsight к файлам в Azure Data Lake Storage 2-го поколения можно контролировать с помощью управляемых удостоверений. Управляемое удостоверение — это удостоверение, регистрируемое в Azure Active Directory, учетными данными которого управляет Azure. Регистрация субъектов-служб в Azure AD и поддержка учетных данных, таких как сертификаты, не требуется.

Существует два типа управляемых удостоверений для служб Azure: назначаемые системой и назначаемые пользователем. Для доступа к Azure Data Lake Storage 2-го поколения в Azure HDInsight используются управляемые удостоверения, назначаемые пользователем. Назначаемое пользователем управляемое удостоверение создается как изолированный ресурс Azure. При этом Azure создает удостоверение в доверенном клиенте Azure AD используемой подписки. После создания удостоверения оно может быть назначено одному или нескольким экземплярам служб Azure. Управление жизненным циклом назначаемого пользователем удостоверения осуществляется отдельно от жизненного цикла экземпляров служб Azure, для которых оно назначено. Дополнительные сведения об управляемых удостоверениях см. в разделе [Принцип работы управляемых удостоверений для ресурсов Azure](/../active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Как задать разрешения для пользователей Azure AD, чтобы они могли запрашивать данные из Azure Data Lake Storage 2-го поколения с помощью Hive или других служб?

Используйте группы безопасности Azure AD в качестве назначенного субъекта в списках ACL. Не назначайте отдельных пользователей или субъекты-службы с разрешениями доступа к файлам напрямую. При использовании групп безопасности AD для управления процессом предоставления разрешений вы можете добавлять и удалять пользователей или субъекты-службы без повторного применения списков управления доступом ко всей структуре каталога. Нужно просто добавить пользователей в соответствующую группу безопасности Azure AD или удалить их из нее. Списки ACL не наследуются, поэтому при их повторном применении требуется обновление списка ACL для каждого файла и подкаталога.

## <a name="next-steps"></a>Дополнительная информация

* [Use Azure Data Lake Storage Gen2 Preview with Azure HDInsight clusters](/../storage/blobs/data-lake-storage-use-hdi-cluster.md) (Использование хранилища Azure Data Lake Storage 2-го поколения (предварительная версия) с кластерами Azure HDInsight)
* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Интеграция Azure HDInsight с Data Lake Storage 2-го поколения (предварительная версия) — обновление списка управления доступом и системы безопасности)
* [Общие сведения о хранилище Data Lake Storage 2-го поколения (предварительная версия)](/../storage/blobs/data-lake-storage-introduction.md)