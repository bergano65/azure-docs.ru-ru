---
title: Управляемые удостоверения в Azure HDInsight
description: Содержит общие сведения о реализации управляемых удостоверений в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: daae9c16797ad9c1b85635f5aec7d0cf884e003f
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206016"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Управляемые удостоверения в Azure HDInsight

Управляемое удостоверение — это удостоверение, зарегистрированное в Azure Active Directory (Azure AD), учетные данные которых управляются Azure. При использовании управляемых удостоверений вам не нужно регистрировать субъекты-службы в Azure AD или хранить учетные данные, например сертификаты.

Управляемые удостоверения используются в Azure HDInsight для доступа к доменным службам Azure AD или к файлам в Azure Data Lake Storage 2-го поколения при необходимости.

Существует два типа управляемых удостоверений: назначенные пользователем и назначенные системой. Azure HDInsight поддерживает только управляемые пользователем удостоверения. HDInsight не поддерживает управляемые системой удостоверения. Назначаемое пользователем управляемое удостоверение создается как автономный ресурс Azure, который затем можно назначить одному или нескольким экземплярам службы Azure. В отличие от этого, управляемое системой удостоверение создается в Azure AD, а затем автоматически включается в конкретный экземпляр службы Azure. Жизненный цикл этого управляемого удостоверения, назначенного системой, затем привязывается к жизненному циклу экземпляра службы, на котором она включена.

## <a name="hdinsight-managed-identity-implementation"></a>Реализация управляемого удостоверения HDInsight

В Azure HDInsight управляемые удостоверения подготавливаются на каждом узле кластера. Однако эти компоненты удостоверений можно использовать только в службе HDInsight. В настоящее время нет поддерживаемого метода для создания маркеров доступа с помощью управляемых удостоверений, установленных на узлах кластера HDInsight. Для некоторых служб Azure управляемые удостоверения реализуются с помощью конечной точки, которую можно использовать для получения маркеров доступа для взаимодействия с другими службами Azure самостоятельно.

## <a name="create-a-managed-identity"></a>Создание управляемого удостоверения

Управляемые удостоверения можно создавать с помощью любого из следующих методов.

* [Портал Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Оставшиеся шаги по настройке управляемого удостоверения зависят от сценария, в котором он будет использоваться.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Сценарии управляемых удостоверений в Azure HDInsight

Управляемые удостоверения используются в Azure HDInsight в нескольких сценариях. Подробные инструкции по настройке и настройке см. в соответствующих документах:

* [Azure Data Lake Storage 2-го поколения](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Пакет безопасности корпоративного уровня](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Шифрование диска с управляемым клиентом ключом](disk-encryption.md)

## <a name="faq"></a>Вопросы и ответы
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Что произойдет, если удалить управляемое удостоверение после создания кластера?
При необходимости управляемого удостоверения кластеру будут выработаны проблемы. В настоящее время невозможно обновить или изменить управление удостоверениями после создания кластера. Поэтому мы рекомендуем убедиться, что управляемое удостоверение не удалено во время выполнения кластера. Кроме того, можно повторно создать кластер и назначить новое управляемое удостоверение.

## <a name="next-steps"></a>Следующие шаги

* [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md)
