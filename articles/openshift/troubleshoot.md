---
title: Устранение неполадок в Azure Red Hat OpenShift
description: Устранение неполадок и решение распространенных проблем с помощью Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274929"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Устранение неполадок в Azure Red Hat OpenShift

В этой статье описаны некоторые распространенные проблемы, возникающие при создании или управлении Microsoft Azure кластерах Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Повторная попытка создания кластера с ошибками

Если при создании кластера Azure Red Hat OpenShift с помощью команды CLI `az` происходит сбой, повторная попытка создания завершится ошибкой.
Используйте `az openshift delete`, чтобы удалить неисправный кластер, а затем создайте совершенно новый кластер.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Скрытая группа ресурсов кластера Azure Red Hat OpenShift

В настоящее время `Microsoft.ContainerService/openShiftManagedClusters`ный ресурс, автоматически созданный Azure CLI (командой`az openshift create`), скрыт в портал Azure. В представлении " **Группа ресурсов** " установите флажок **Показать скрытые типы** , чтобы просмотреть группу ресурсов.

![Снимок экрана: флажок скрытого типа на портале](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Создание кластера приводит к ошибке, что зарегистрированный поставщик ресурсов не найден.

Если создание кластера приводит к ошибке, которая `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, то вы были частью предварительной версии и теперь должны [приобрести зарезервированные экземпляры виртуальных машин Azure](https://aka.ms/openshift/buy) для использования общедоступного продукта. Резервирование сокращает расходы за счет предварительной оплаты для полностью управляемых служб Azure. Дополнительные сведения о резервировании и способах экономии денег см. в разделе [*что такое резервирование Azure*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) .

## <a name="next-steps"></a>Дальнейшие действия

- Воспользуйтесь [центром справки Red Hat OpenShift](https://help.openshift.com/) для получения дополнительных сведений об устранении неполадок OpenShift.

- Найдите ответы на [часто задаваемые вопросы о Azure Red Hat OpenShift](openshift-faq.md).
