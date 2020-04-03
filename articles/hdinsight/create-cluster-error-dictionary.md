---
title: Azure HDInsight Создать кластер - словарь ошибок
description: Узнайте, как устранить ошибки, возникающие при создании кластеров Azure HDInsight
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 803783eddfbffd5c3dbab7353ee00dd7f11a09e5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618901"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: ошибки создания кластеров

В этой статье описаны разрешения на ошибки, с которые можно встретить при создании кластеров.

> [!NOTE]
> Первые три ошибки, описанные в этой статье, являются ошибками проверки. Они могут возникать, когда продукт Azure HDInsight использует **CsmDocument_2_0** класс.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Код ошибки: РазвертываниеДокумент "CsmDocument_2_0" не удалось проверки

### <a name="error"></a>Error

"Место действия сценария не может\<быть\>доступно URI: SCRIPT ACTION URL "

#### <a name="error-message"></a>Сообщение об ошибке

"Удаленный сервер вернул ошибку: (404) не найдено."

### <a name="cause"></a>Причина

Служба HDInsight не может получить доступ к URL-адресу действий скрипта, который вы предоставили в рамках запроса Create Cluster. Служба получает предыдущее сообщение об ошибке при попытке получить доступ к действию скрипта.

### <a name="resolution"></a>Решение

- Для HTTP или HTTPS URL, проверить URL, пытаясь перейти к нему из окна браузера инкогнито.
- Для URL-адресов WASB убедитесь, что скрипт существует в учетной записи хранилища, которую вы даете в запросе. Также убедитесь, что ключ хранения для этой учетной записи хранилища является правильным.
- Для URL ADLS убедитесь, что скрипт существует в учетной записи хранилища.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Код ошибки: РазвертываниеДокумент "CsmDocument_2_0" не удалось проверки

### <a name="error"></a>Error

"Место действия сценария не может \<\>быть доступно URI: SCRIPT_ACTION_URL "

#### <a name="error-message"></a>Сообщение об ошибке

"Данный скрипт URI \<SCRIPT_URI\> находится в ADLS, но этот кластер не имеет данных хранения озер основной"

### <a name="cause"></a>Причина

Служба HDInsight не может получить доступ к URL-адресу действий скрипта, который вы предоставили в рамках запроса Create Cluster. Служба получает предыдущее сообщение об ошибке при попытке получить доступ к действию скрипта.

### <a name="resolution"></a>Решение

Добавьте в кластер соответствующую учетную запись Azure Data Lake Storage Gen 1. Также добавьте в кластер основную службу, доступ к учетной записи Data Lake Storage Gen 1.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Код ошибки: РазвертываниеДокумент "CsmDocument_2_0" не удалось проверки

### <a name="error"></a>Error

"VM размер\<\>" CUSTOMER_SPECIFIED_VM_SIZE ', представленный в запросе, является недействительным или не поддерживается для роли '\<ROLE 'ROLE\>'. Действительные значения: \<\>VALID_VM_SIZE_FOR_ROLE."

### <a name="cause"></a>Причина

Виртуальный размер машины, указанный вами, не допускается для роли. Эта ошибка может произойти, потому что значение размера VM не работает, как ожидалось, или не подходит для роли компьютера.

### <a name="resolution"></a>Решение

Сообщение об ошибке перечисляет допустимые значения для размера VM. Выберите одно из этих значений и повторно попробуйте запрос Create Cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Код ошибки: InvalidVirtualNetworkId  

### <a name="error"></a>Error

"VirtualNetworkId не действителен. VirtualNetworkId\<'\>USER_VIRTUALNETWORKID '"

### <a name="cause"></a>Причина

Значение **VirtualNetworkId,** указанное при создании кластера, не в нужном формате.

### <a name="resolution"></a>Решение

Убедитесь, что значения **VirtualNetworkId** и подсети находятся в правильном формате. Чтобы получить значение **VirtualNetworkId:**

1. Перейдите на портал Azure.
1. Выберите виртуальную сеть.
1. Выберите пункт меню **Свойства.** Значение свойства **ResourceID** является значением **VirtualNetworkId.**

Вот пример виртуального идентификатора сети:

"/подписка/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Код ошибки: НастройкаFailedОшибкаКоди

### <a name="error"></a>Error

"Развертывание кластера не удалось из-за ошибки в действии пользовательского сценария. Неудачные \<действия: SCRIPT_NAME,\>пожалуйста, перейдите к Ambari UI для дальнейшего отладки неудачи".

### <a name="cause"></a>Причина

Пользовательский скрипт, предоставленный во время запроса «Создать кластер», выполняется после успешного развертывания кластера. Этот код ошибки указывает на то, что \<ошибка\>возникла во время выполнения пользовательского скрипта под названием SCRIPT_NAME .

### <a name="resolution"></a>Решение

Поскольку скрипт является вашим пользовательским скриптом, мы рекомендуем устранить проблему и при необходимости перезапустить сценарий. Чтобы устранить сбой скрипта, изучите журналы в папке /var/lib/ambari-agent/'. Или откройте страницу **операций** в ui Ambari, а затем выберите **run_customscriptaction** операции для просмотра сведений об ошибках.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Код ошибки: НедействительныйКодИкОшибкаКод

### <a name="error"></a>Error

"Версия \<\> \<схемы META_STORE_TYPE Metastore\> METASTORE_MAJOR_VERSION \<\> в базе данных \<DATABASE_NAME\>несовместима с кластерной версией CLUSTER_VERSION"

### <a name="cause"></a>Причина

Пользовательский метамагазин несовместим с выбранной кластерной версией HDInsight. В настоящее время кластеры HDInsight 4.0 поддерживают только версию Metastore 3.0 и более поздние, в то время как кластеры HDInsight 3.6 не поддерживают версию Metastore 3.0 и более поздние.

### <a name="resolution"></a>Решение

Используйте только версии Metastore, которые поддерживает ваша кластерная версия HDInsight. Если вы не указали пользовательский метамагазин, HDInsight внутренне создает метамагазин, а затем удаляет его при удалении кластера.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Код ошибки: не удалосьподключитьСкластерошибкакода 

### <a name="error"></a>Error

"Не удается подключиться к конечной точке управления кластерами для выполнения операции масштабирования. Убедитесь, что правила сетевой безопасности не блокируют внешний доступ к кластеру и что к пользовательского системы менеджера кластера (Ambari) можно получить доступ к пользовательу" (Ambari).

### <a name="cause"></a>Причина

Правило брандмауэра в группе сетевой безопасности (NSG) блокирует кластерную связь с критически важными службами работоспособности и управления Azure.

### <a name="resolution"></a>Решение

Если вы планируете использовать группы сетевой безопасности для управления сетевым трафиком, примите следующие действия перед установкой HDInsight:

- Определите регион Azure, который планируется использовать для HDInsight.
- Определите IP-адреса, необходимые для HDInsight. Дополнительные сведения см. в статье [HDInsight management IP addresses](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses) (IP-адреса управления HDInsight).
  - Создавайте или изменяйте группы сетевой безопасности для подсети, в которую вы планируете установить HDInsight.
  - Для групп сетевой безопасности разрешить входящий трафик в порту 443 с IP-адресов. Эта конфигурация гарантирует, что службы управления HDInsight могут достичь кластера из-за пределов виртуальной сети.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Код ошибки: ХранениеПозволеныЗаблокированныеДляМси

### <a name="error"></a>Error

"Управляемая идентификация не имеет разрешений на учетную запись хранения. Пожалуйста, убедитесь, что роль владельца данных «Хранение данных Blob» присваивается управляемой учетной записи. Хранение: /подписка \</\> Подписка\< ID\> /resourceGroups/ Название группы ресурсов \</ провайдеры / Microsoft.Storage/storageAccounts / Имя\>\< учетной\> записи хранилища , Управляемая идентификация: \</подписка \<\>ID\> /resourceGroups/ / Название группы ресурсов /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ Пользовательские Управляемые идентификационные имена "

### <a name="cause"></a>Причина

Вы не предоставили разрешения, необходимые для управления личностью. Назначенная пользователем управляемая идентификация не имеет роли вклада в хранилище Blob в учетной записи хранения данных Azure Data Lake Data.

### <a name="resolution"></a>Решение

1. Откройте портал Azure.
1. Войдите в свою учетную запись хранения.
1. Посмотрите под **контролем доступа (IAM)**.
1. Убедитесь, что пользователь имеет роль вкладчика хранилища данных Blob или роль владельца данных Storage Blob, назначенную ему.

Для получения дополнительной информации см. [Настройка разрешений для управляемого удостоверения личности в учетной записи Data Lake Storage Gen2.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Код ошибки: НедействительныеСетевойГруппохраняПравила

### <a name="error"></a>Error

"Правила безопасности в группе сетевой\<безопасности /подписка /\>Подписка /resourceGroups/<Ресурсная группа имя\> по умолчанию / провайдеры / Microsoft.Network/networkSecurityGroups /\<Название группы сетевой безопасности\> настроены с подсетью / подписки /\<Подписка /resourceGroups\>/\<Ресурсная группа имя\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<\>\<\> Для получения дополнительной информации, пожалуйста, посетите [веб-сайт Plan виртуальной сети для Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)или обратитесь в службу поддержки».

### <a name="cause"></a>Причина

Если группы сетевой безопасности или маршруты, определяемые пользователями(UDRs), контролируют входящий трафик в кластер HDInsight, убедитесь, что кластер может общаться с критически важными службами работоспособности и управления Azure.

### <a name="resolution"></a>Решение

Если вы планируете использовать группы сетевой безопасности для управления сетевым трафиком, примите следующие действия перед установкой HDInsight:

- Определите область Azure, которую планируется использовать для HDInsight, и создайте безопасный список IP-адресов для вашего региона. Для получения дополнительной [Health and management services: Specific regions](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)информации см.
- Определите IP-адреса, которые требуется HDInsight. Для получения дополнительной информации смотрите [IP-адреса управления HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Создавайте или изменяйте группы сетевой безопасности для подсети, в которую вы планируете установить HDInsight. Для групп сетевой безопасности разрешить входящий трафик в порту 443 с IP-адресов. Эта конфигурация гарантирует, что службы управления HDInsight могут достичь кластера из-за пределов виртуальной сети.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Код ошибки: Настройка кластера не установить компоненты на одном или нескольких узлах

### <a name="error"></a>Error

"Кластерная настройка не смогла установить компоненты на один или несколько узлов. Пожалуйста, повторите свой запрос".

### <a name="cause"></a>Причина 

Обычно эта ошибка генерируется при переходной проблеме или сбойе Azure.

### <a name="resolution"></a>Решение

Проверьте страницу [состояния Azure](https://status.azure.com) на наличие сбоев Azure, которые могут повлиять на развертывание кластеров. Если нет сбоев, повторите развертывание кластера.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Код ошибки: не удалосьподключитьСкластерошибкакода

### <a name="error"></a>Error

Невозможно подключиться к конечной точке управления кластерами. Повторите попытку позже.

### <a name="cause"></a>Причина

Служба HDInsight не может подключиться к кластеру при попытке создать кластер

### <a name="resolution"></a>Решение

Если вы используете пользовательские группы сетевой безопасности VNet (NSGs) и маршруты, определяемые пользователями (UDR), убедитесь, что ваш кластер может общаться с службами управления HDInsight. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Код ошибки: Развертывание не удалось из-за нарушения политики: 'Ресурс'<Resource URI>был запрещен политикой. Идентификаторы политики: «Назначение политики»: «имя»<Policy Name> «,id»: «id»: провайдеры/Microsoft.Management/managementGroups/providers/Microsoft.Authorization/policyAssignments/«»»«Определение<Management Group Name> <Policy Name>политики»:<Policy Definition>

### <a name="cause"></a>Причина

Политики Azure, основанные на подписке, могут отказать в создании общедоступных IP-адресов. Для создания кластера HDInsight требуется два общедоступных IP-адреса.

Следующие политики, как правило, влияют на создание кластеров:

* Политики, которые препятствуют созданию IP-адресов или балансеров нагрузки в подписке.
* Политика, препятствующая созданию учетных записей хранилища.
* Политика, предотвращающая удалять сетевые ресурсы, такие как IP-адреса или балансеры нагрузки.

### <a name="resolution"></a>Решение

Удалите или отключать политику Azure, основанную на подписке, при создании кластера HDInsight.

---

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной информации об ошибках устранения неполадок при создании кластеров [см.](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)
