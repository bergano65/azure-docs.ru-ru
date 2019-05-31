---
title: Устранение неполадок Azure Red Hat OpenShift | Документация Майкрософт
description: Устранение неполадок и разрешении распространенных проблем с Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 7f2bdf643f12671bec3d0c087d8775844099fe9a
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306252"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Устранение неполадок для Azure Red Hat OpenShift

В этой статье описаны некоторые распространенные неполадки, возникающие при создании или управлении кластерами Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Повторная попытка создания сбоя кластера

Если создание Azure Red Hat OpenShift кластер с помощью `az` завершается со сбоем команда интерфейса командной строки, повторная попытка создания будет завершаться сбоем.
Используйте `az openshift delete` для удаления сбоя кластера, затем создать совершенно новый кластер.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Скрытые группы ресурсов кластера Azure Red Hat OpenShift

В настоящее время `Microsoft.ContainerService/openShiftManagedClusters` ресурс, который автоматически создается в Azure CLI (`az openshift create` команду) является скрытым на портале Azure. В **группы ресурсов** представление, проверьте **Показать скрытые типы** для просмотра группы ресурсов.

![Снимок экрана: флажок Скрытый тип на портале](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Создание кластера результаты в ошибку, обнаруженную нет зарегистрированного поставщика ресурсов

Если создание кластере, отражается в ошибку, `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, затем были частью предварительной версии и теперь нужно [покупки виртуальной машины Azure зарезервированные экземпляры](https://aka.ms/openshift/buy) использование общедоступной продукта. Резервирование позволяет сократить ваши расходы по предварительной оплаты полностью управляемых служб Azure. Ссылаться на [ *Каковы резервирования Azure* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) получить дополнительные сведения о резервировании и как они помочь сэкономить денежные средства.

## <a name="next-steps"></a>Дальнейшие действия

- Попробуйте [центр справки Red Hat OpenShift](https://help.openshift.com/) для дополнительные on OpenShift устранения неполадок.

- Найдите ответы на [часто задаваемые вопросы об Azure Red Hat OpenShift](openshift-faq.md).
