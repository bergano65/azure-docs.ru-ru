---
title: Распространенные вопросы и рекомендации по Azure Databricks
description: Ответы на часто задаваемые вопросы и сведения об устранении неполадок для Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 0436a0898f27d3059069ce2df1fdfb143a98f325
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601807"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Часто задаваемые вопросы об Azure Databricks

В этой статье перечислены самые популярные вопросы, которые могут возникнуть в связи с Azure Databricks. Также здесь указаны некоторые распространенные проблемы, которые могут возникать при использовании Databricks. Дополнительные сведения см. в статье [Что такое Azure Databricks?](what-is-azure-databricks.md) 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Можно ли использовать Azure Key Vault для хранения ключей (секретов), используемых в Azure Databricks?
Да. Azure Key Vault можно использовать для хранения ключей (секретов), используемых в Azure Databricks. Дополнительные сведения см. в документации по [Create an Azure Key Vault-backed secret scope](/azure/databricks/security/secrets/secret-scopes) (Создании секретной области в Azure Key Vault).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Можно ли использовать виртуальные сети Azure в Azure Databricks?
Да. Виртуальные сети Azure можно использовать в Azure Databricks. Дополнительные сведения см. в статье [Deploying Azure Databricks in your Azure Virtual Network (Preview)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject) (Развертывание Azure Databricks в виртуальной сети Azure (предварительная версия)).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Разделы справки получить доступ к Azure Data Lake Storage из записной книжки? 

Выполните следующие действия.
1. Подготовьте субъект-службу в Azure Active Directory (Azure AD) и запишите его ключ.
1. Назначьте необходимые разрешения субъекту-службе в Data Lake Storage.
1. Чтобы получить доступ к файлу в Data Lake Storage, используйте учетные данные субъекта-службы в записной книжке.

Дополнительные сведения см. в разделе [использование Azure Data Lake Storage с Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Устранение распространенных проблем

Здесь описаны несколько проблем, которые могут возникнуть при работе с Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Проблема. Эта подписка не зарегистрирована для использования пространства имен Microsoft.Databricks

#### <a name="error-message"></a>Сообщение об ошибке

Эта подписка не зарегистрирована для использования пространства имен Microsoft.Databricks. "See https://aka.ms/rps-not-found for how to register subscriptions" (Чтобы узнать больше о регистрации подписок, перейдите на страницу https://aka.ms/rps-not-found). (Код: MissingSubscriptionRegistration)

#### <a name="solution"></a>Решение

1. Перейдите на [портал Azure](https://portal.azure.com).
1. Выберите элемент **Подписки**, затем используемую подписку и щелкните **Поставщики ресурсов**. 
1. В списке поставщиков ресурсов выберите действие **Зарегистрировать** рядом с элементом **Microsoft.Databricks**. Чтобы зарегистрировать поставщика ресурсов, нужно иметь в подписке роль участника или владельца.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Проблема. Для вашей учетной записи {email} не назначена роль владельца или участника в ресурсе рабочей области Databricks на портале Azure

#### <a name="error-message"></a>Сообщение об ошибке

"Your account {email} does not have Owner or Contributor role on the Databricks workspace resource in the Azure portal" (Для вашей учетной записи {адрес электронной почты} не назначена роль владельца или участника в ресурсе рабочей области Databricks на портале Azure). Это сообщение об ошибке может отображаться также для гостевых пользователей клиента. Обратитесь к администратору, чтобы он предоставил вам доступ или добавил вас в качестве пользователя непосредственно в рабочей области Databricks. 

#### <a name="solution"></a>Решение

Ниже приведено несколько решений для этой проблемы.

* Чтобы инициализировать клиент, нужно войти от имени обычного (не гостевого) пользователя этого клиента. Также этот пользователь должен иметь роль участника для ресурса рабочей области Databricks. Чтобы предоставить доступ пользователю, воспользуйтесь вкладкой **Управление доступом (IAM)** в рабочей области Databricks на портале Azure.

* Эта ошибка также может возникнуть, если доменное имя из используемого адреса электронной почты назначено многим каталогам в Azure Active Directory. Чтобы обойти эту проблему, создайте нового пользователя в каталоге, который содержит подписку с рабочей областью Databricks.

    а. На портале Azure перейдите к Azure AD. Выберите **Пользователи и группы**, а затем —  > **Добавить пользователя**.

    b. Добавьте пользователя с адресом электронной почты в формате `@<tenant_name>.onmicrosoft.com` вместо `@<your_domain>`. Этот параметр можно найти в списке **Пользовательские домены** в разделе Azure AD на портале Azure.
    
    c. Назначьте новому пользователю роль **участника** для ресурса рабочей области Databricks.
    
    г) Войдите на портал Azure с учетными данными нового пользователя и найдите нужную рабочую область Databricks.
    
    д. Запустите рабочую область Databricks от имени этого пользователя.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Проблема. Ваша учетная запись {адрес электронной почты} не зарегистрирована в Databricks 

#### <a name="solution"></a>Решение

Если вы не создавали эту рабочую область, вас нужно добавить в качестве пользователя. Для этого обратитесь к создателю рабочей области. Попросите добавить вас с помощью консоли администрирования Azure Databricks. Эта процедура описана в статье [Adding and managing users](/azure/databricks/administration-guide/users-groups/users) (Добавление пользователей и управление ими). Если вы получаете такое сообщение, когда сами создавали эту рабочую область, попробуйте еще раз выполнить действие **Инициализация рабочей области** на портале Azure.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Проблема. Сбой запуска поставщика облачных служб при настройке кластера (PublicIPCountLimitReached)

#### <a name="error-message"></a>Сообщение об ошибке

"Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster" (Сбой запуска поставщика облачных служб: при настройке кластера произошла ошибка поставщика облачных служб). Дополнительные сведения см. в руководстве по Databricks. Код ошибки Azure: PublicIPCountLimitReached. Сообщение об ошибке Azure: "Cannot create more than 60 public IP addresses for this subscription in this region" (Нельзя создать более 60 общедоступных IP-адресов для этой подписки в этом регионе).

#### <a name="solution"></a>Решение

Кластер Databricks использует один общедоступный IP-адрес на каждый узел. Если для подписки уже использованы все общедоступные IP-адреса, отправьте нам [запрос на увеличение квоты](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Выберите значение **Квоты** для параметра **Тип проблемы** и значение **Сети: ARM** для параметра **Тип квоты**. В области **Сведения** сформулируйте просьбу увеличить квоту на общедоступные IP-адреса. Например, если установлен лимит 60, а вам нужно создать кластер на 100 узлов, попросите увеличить лимит до 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Проблема. Второй тип сбоя запуска поставщика облачных служб при настройке кластера (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Сообщение об ошибке

"Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster" (Сбой запуска поставщика облачных служб: при настройке кластера произошла ошибка поставщика облачных служб). Дополнительные сведения см. в руководстве по Databricks.
Код ошибки Azure: MissingSubscriptionRegistration. Сообщение об ошибке Azure: The subscription is not registered to use namespace 'Microsoft.Compute' (Эта подписка не зарегистрирована для использования пространства имен Microsoft.Compute). Чтобы узнать больше о регистрации подписок, перейдите на страницу https://aka.ms/rps-not-found .

#### <a name="solution"></a>Решение

1. Перейдите на [портал Azure](https://portal.azure.com).
1. Выберите элемент **Подписки**, затем используемую подписку и щелкните **Поставщики ресурсов**. 
1. В списке поставщиков ресурсов выберите действие **Зарегистрировать** рядом с **Microsoft.Compute**. Чтобы зарегистрировать поставщика ресурсов, нужно иметь в подписке роль участника или владельца.

Подробные инструкции см. в статье [Поставщики и типы ресурсов](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Проблема. Azure Databricks требуются разрешения на доступ к ресурсам в вашей организации, которые может предоставить только администратор

#### <a name="background"></a>Общие сведения

Azure Databricks интегрируется с Azure Active Directory. Можно задать разрешения в Azure Databricks (например, для записных книжек или кластеров), указав пользователей из Azure AD. Чтобы в Azure Databricks можно было вывести список имен пользователей из Azure AD, требуется разрешение на чтение этих данных и согласие на их предоставление. Если оно недоступно, возникнет ошибка.

#### <a name="solution"></a>Решение

Войдите на портал Azure как глобальный администратор. В разделе Azure Active Directory перейдите на вкладку **Параметры пользователей** и задайте для параметра **Пользователи могут разрешать приложениям доступ к корпоративным данным от своего имени** значение **Да**.

## <a name="next-steps"></a>Дальнейшие действия

- [Краткое руководство по началу работы с Azure Databricks](quickstart-create-databricks-workspace-portal.md).
- [What is Azure Databricks?](what-is-azure-databricks.md) (Что такое Azure Databricks).

