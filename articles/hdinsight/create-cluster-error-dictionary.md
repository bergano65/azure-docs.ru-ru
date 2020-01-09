---
title: Создание словаря ошибок кластера
description: Узнайте, как создать словарь ошибок кластера.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483067"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: ошибки создания кластера

В этой статье описываются способы устранения ошибок, возникающих при создании кластеров. 

> [!NOTE]
> Первые три ошибки в приведенном ниже списке возникают из-за ошибок проверки, если класс CsmDocument_2_0 используется продуктом HDInsight.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Код ошибки: Деплойментдокумент "CsmDocument_2_0" не прошел проверку

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Ошибка**: не удается получить доступ к расположению действия скрипта URI:\<URL-адрес действия скрипта\>

**Сообщение об ошибке: "удаленный сервер вернул ошибку: (404) не найден".**

### <a name="cause"></a>Причина
URL-адрес действия скрипта, который предоставляется в составе запроса на создание кластера, недоступен из службы HDInsight. При попытке получить доступ к действию сценария мы получаем «ErrorMessage».

### <a name="resolution"></a>Разрешение 
  - Для URL-адреса HTTP или HTTPS можно проверить, попытайтесь открыть URL-адрес из окна браузера режиме инкогнито. 
  - Для URL-адреса WASB убедитесь, что сценарий существует в учетной записи хранения, которая указана в запросе. Убедитесь, что ключ хранилища для этой учетной записи хранения является точным. 
  - Для URL-адреса ADLS убедитесь, что сценарий существует в учетной записи хранения.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Код ошибки: Деплойментдокумент "CsmDocument_2_0" не прошел проверку

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Ошибка**: не удается получить доступ к расположению действия скрипта URI: \<URL-адрес действия скрипта\>

**Сообщение об ошибке: указанный URI скрипта \<URI\> находится в ADLS, но в этом кластере нет субъекта хранилища Data Lake**

### <a name="cause"></a>Причина
URL-адрес действия скрипта, указанный в составе запроса на создание кластера, недоступен из службы HDInsight. При попытке получить доступ к действию сценария мы получаем «ErrorMessage». 

### <a name="resolution"></a>Разрешение

Убедитесь, что соответствующая учетная запись Azure Data Lake Store Gen 1 добавлена в кластер. Субъект-служба, используемый для доступа к учетной записи Azure Data Lake Store Gen 1, также добавляется в кластер. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Код ошибки: Деплойментдокумент "CsmDocument_2_0" не прошел проверку

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Ошибка**: размер виртуальной машины "\<указанная клиентом размер виртуальной машины\>", указанный в запросе, недопустима или не поддерживается для роли "\<Role\>". Допустимые значения: \<ДОПУСТИМЫй размер виртуальной машины для\>РОЛЕЙ.

### <a name="cause"></a>Причина
Размеры виртуальных машин, указанные клиентом, не разрешены для этой роли. Это может быть истинно, так как значение размера виртуальной машины не работает должным образом или не подходит для роли компьютера. 

### <a name="resolution"></a>Разрешение
В сообщении об ошибке перечислены допустимые значения размера виртуальной машины. Выберите одно из этих допустимых значений и повторите запрос на создание кластера. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Код ошибки: Инвалидвиртуалнетворкид  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Ошибка**: недопустимый VirtualNetworkId. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>Причина
Значение **VirtualNetworkId** , указанное при создании кластера, имеет неправильный формат. 

### <a name="resolution"></a>Разрешение
Убедитесь, что **VirtualNetworkId** и подсеть имеют правильный формат. Чтобы получить значение **VirtualnetworkId** , перейдите в портал Azure, выберите виртуальную сеть, а затем в меню выберите пункт **свойства** . Свойство **ResourceId** имеет значение **VirtualNetworkId** . 

Пример идентификатора виртуальной сети: 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>Код ошибки: Кустомизатионфаиледерроркоде

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Ошибка**: сбой развертывания кластера из-за ошибки в действии пользовательского скрипта. Действия, завершившиеся сбоем: \<SCRIPT_NAME\>, перейдите в пользовательский интерфейс Ambari для дальнейшей отладки сбоя.

### <a name="cause"></a>Причина
Пользовательский скрипт пользователя, предоставленный во время запроса на создание кластера, выполняется после успешного развертывания кластера. Этот код ошибки означает, что при выполнении этого пользовательского скрипта с именем \<SCRIPT_NAME\>возникла ошибка.   

### <a name="resolution"></a>Разрешение
Так как это пользовательский сценарий пользователя, пользователи должны устранить проблему и при необходимости перезапустить сценарий. Чтобы устранить ошибку сценария, просмотрите журналы в папке/Вар/либ/Амбари-ажент/*. Или откройте страницу операции в пользовательском интерфейсе Ambari, а затем выберите **run_customscriptionaction** операцию, чтобы просмотреть сведения об ошибке. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Код ошибки: Инвалиддокументерроркоде

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Ошибка**: \<META_STORE_TYPE\> хранилище метаданных версии схемы \<METASTORE_MAJOR_VERSION\> в базе данных \<DATABASE_NAME\> несовместим с версией кластера \<CLUSTER_VERSION

### <a name="cause"></a>Причина
Настраиваемый хранилище метаданных несовместим с выбранной версией кластера HDInsight. В настоящее время кластер HDInsight 4,0 поддерживает только хранилище метаданных версии 3. *x*и HDInsight 3,6 не поддерживают хранилище метаданных версии 3. *x* или более поздней версии. 

### <a name="resolution"></a>Разрешение
Убедитесь, что используются только версии хранилище метаданных, поддерживаемые каждой версией кластера HDInsight. Обратите внимание, что если пользовательская хранилище метаданных не указана, HDInsight внутренне создает хранилище метаданных. Однако этот хранилище метаданных будет автоматически удален после удаления кластера. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Код ошибки: Фаиледтоконнектвисклустерерроркоде 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Ошибка**: не удалось подключиться к конечной точке управления кластера для выполнения операции масштабирования. Убедитесь, что правила сетевой безопасности не блокируют внешний доступ к кластеру, и что доступ к пользовательскому интерфейсу диспетчера кластеров (Ambari) может быть успешно выполнен.

### <a name="cause"></a>Причина
У вас есть правило брандмауэра в группе безопасности сети (NSG), блокирующее взаимодействие кластера с критически важными службами работоспособности и управления Azure. 

### <a name="resolution"></a>Разрешение
Если вы планируете использовать **группы безопасности сети** для управления сетевым трафиком, перед установкой HDInsight выполните следующие действия. 
  - Определите регион Azure, который планируется использовать для HDInsight. 
  - Определите IP-адреса, необходимые для HDInsight. Дополнительные сведения см. в статье [HDInsight management IP addresses](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses) (IP-адреса управления HDInsight). 
    - Создайте или измените группы безопасности сети для подсети, в которую планируется установить HDInsight. 
    - **Группы безопасности сети:** Разрешите **входящий** трафик через порт **443** с IP-адресов. Это гарантирует, что службы управления HDInsight могут получить доступ к кластеру извне виртуальной сети. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Код ошибки: Сторажепермиссионсблоккедформси  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Ошибка**: управляемое удостоверение не имеет разрешений на учетную запись хранения. Убедитесь, что роль "владелец данных BLOB-объекта хранилища" назначена управляемому удостоверению для учетной записи хранения. Хранилище:/Subscriptions/\<Subscription ID\>/resourceGroups/\< имя группы ресурсов\>/Провидерс/Микрософт.стораже/сторажеаккаунтс/\<имя учетной записи хранения\>, управляемое удостоверение:/Subscriptions/\<идентификатор подписки\>/resourceGroups//\< имя группы ресурсов\>/Провидерс/Микрософт.манажедидентити/усерассигнедидентитиес/\<имя управляемого удостоверения пользователя\>

### <a name="cause"></a>Причина
Не предоставлены необходимые разрешения для **управления удостоверениями.** **Управляемому удостоверению, назначенному пользователю** , не назначена роль участника хранилища BLOB-объектов в учетной записи хранения ADLS 2-го поколения. 

### <a name="resolution"></a>Разрешение

Откройте портал Azure, перейдите к своей учетной записи хранения, в разделе " **Управление доступом (IAM)** " и убедитесь, что участник данных BLOB-объекта хранилища или роль владельца BLOB-объектов хранилища имеет "назначенный" доступ к **управляемому удостоверению, назначенному пользователю** для подписки. Дополнительные сведения см. в разделе [Настройка разрешений для управляемого удостоверения в учетной записи Data Lake Storage 2-го поколения](hdinsight-hadoop-use-data-lake-storage-gen2.md). 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Код ошибки: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Ошибка**: правила безопасности в группе безопасности сети/Subscriptions/\<SubscriptionID >\/resourceGroups/<Resource Group name> Default/providers/Microsoft. Network/networkSecurityGroups/\<имя группы безопасности сети\> настроено с помощью подсети/Subscriptions/\<SubscriptionID >\/resourceGroups/\<имя группы ресурсов > RG-westeurope-vnet-ТомТом-Default\/providers/Microsoft. Network/virtualNetworks/\<Virtual Сетевое имя >\/подсети/\<имя подсети\> 
не разрешает обязательное входящее и/или исходящее подключение. Дополнительные сведения см. в [Подплане плана виртуальной сети для Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)или обратитесь в службу поддержки. * *

### <a name="cause"></a>Причина
Если вы используете группы безопасности сети (группы безопасности сети) или определяемые пользователем маршруты (определяемые пользователем маршруты) для управления входящим трафиком к кластеру HDInsight, необходимо убедиться, что кластер может взаимодействовать с критически важными службами работоспособности и управления Azure.

### <a name="resolution"></a>Разрешение
Если вы планируете использовать **группы безопасности сети** для управления сетевым трафиком, перед установкой HDInsight выполните следующие действия. 
  - Укажите регион Azure, который планируется использовать для HDInsight, и создайте список надежных IP-адресов для вашего региона: [службы работоспособности и управления: конкретные регионы](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Найдите IP-адреса, необходимые для HDInsight. Дополнительные сведения см. в разделе [IP-адреса управления HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
  - Создайте или измените группы безопасности сети для подсети, в которую планируется установить HDInsight. **Группы безопасности сети**: разрешать входящий трафик через порт **443** с IP-адресов. Это обеспечит, чтобы службы управления HDInsight могли получить доступ к кластеру извне виртуальной сети.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Код ошибки: программе установки кластера не удалось установить компоненты на одном или нескольких узлах

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Ошибка**: программе установки кластера не удалось установить компоненты на одном или нескольких узлах. Повторите запрос. 

### <a name="cause"></a>Причина 
Обычно эта ошибка возникает при временной ошибке или в случае сбоя Azure. 

### <a name="resolution"></a>Разрешение

Проверьте страницу [состояния Azure](https://status.azure.com/status) на предмет потенциальных простоев Azure, которые могут повлиять на развертывание кластера. Если нет простоев, повторите развертывание кластера. 

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об устранении ошибок при создании кластера см. в статье [Устранение неполадок при создании кластера с помощью Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
