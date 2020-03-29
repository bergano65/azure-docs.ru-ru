---
title: Устранение неполадок Azure Red Hat OpenShift
description: Устранение неполадок и решение общих проблем с Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274929"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Устранение неполадок для Azure Red Hat OpenShift

В этой статье подробно описаны некоторые распространенные проблемы, возникшие при создании или управлении кластерами Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Повторная попытка создания неудавшегося кластера

При сбою в создании кластера `az` Azure Red Hat OpenShift с помощью команды CLI повторная попытка создания будет продолжать сбой.
Используйте `az openshift delete` для удаления сбой кластера, а затем создать совершенно новый кластер.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Скрытая группа кластеров Azure Red Hat OpenShift

В настоящее время `Microsoft.ContainerService/openShiftManagedClusters` ресурс, автоматически созданный командой Azure CLI,`az openshift create` скрыт на портале Azure. В представлении **группы ресурсов** проверьте **скрытые типы** для просмотра группы ресурсов.

![Скриншот флажка флажка скрытого типа на портале](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Создание кластера приводит к ошибке, которую не нашел зарегистрированный поставщик ресурсов

Если создание кластера приводит `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`к ошибке, которая, то вы были частью предварительного просмотра и теперь необходимо [приобрести Azure виртуальной машины зарезервированы экземпляры](https://aka.ms/openshift/buy) для использования общедоступного продукта. Бронирование сокращает расходы за счет предварительной оплаты полностью управляемых служб Azure. Обратитесь к Тем, что [*такое бронирование Azure,*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) чтобы узнать больше о бронировании и как они экономят ваши деньги.

## <a name="next-steps"></a>Дальнейшие действия

- Попробуйте [Red Hat OpenShift Справочный центр](https://help.openshift.com/) для получения дополнительной информации о OpenShift устранения неполадок.

- Найдите ответы на [часто задаваемые вопросы о Azure Red Hat OpenShift.](openshift-faq.md)
