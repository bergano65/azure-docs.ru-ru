---
title: Устранение распространенных проблем со Службой Azure Kubernetes
description: Узнайте, как устранить распространенные проблемы при использовании Службы Azure Kubernetes (AKS).
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 17f6971cfa2dcd8c8988edc063c89859abec5367
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468841"
---
# <a name="aks-troubleshooting"></a>Устранение неполадок с AKS

При создании кластеров Azure Kubernetes Service (AKS) или управлении ими иногда вы можете сталкиваться с проблемами. В этой статье описаны некоторые распространенные проблемы и действия по устранению неполадок.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Где найти общие сведения об отладке проблем Kubernetes?

[Вот официальное руководство по устранению неполадок с кластерами Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
[Это руководство по устранению неполадок](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) модулей pod, узлов, кластеров и т. д., опубликованное инженером Майкрософт.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Я получаю ошибку превышения квоты во время создания или обновления. Что делать? 

Вам необходимо [запросить ядра](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Каково максимальное число модулей pod на узел, установленное для AKS?

Если вы развертываете кластер AKS на портале Azure, максимальное число модулей pod на узел по умолчанию составляет 30.
Если вы развертываете кластер AKS в Azure CLI, максимальное число модулей pod на узел по умолчанию составляет 110. Убедитесь, что используется последняя версия Azure CLI. Этот параметр по умолчанию можно изменить с помощью флага `–-max-pods` в команде `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Я получаю ошибку insufficientSubnetSize при развертывании кластера AKS с использованием расширенного сетевого взаимодействия. Что делать?

В варианте пользовательской конфигурации виртуальной сети Azure, выбранном для сетей во время создания AKS, для IPAM используется Azure CNI. В кластере AKS может быть любое число узлов в диапазоне от 1 до 100. Исходя из вышесказанного, размер подсети должен быть больше произведения количества узлов и максимального числа модулей pod на узел: размер подсети > количество узлов в кластере * максимальное число модулей pod на узел.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Мой модуль pod завис в режиме CrashLoopBackOff. Что делать?

Модуль pod может зависнуть в этом режиме по различным причинам. Вы можете просмотреть:

* сведения о самом модуле pod с помощью команды `kubectl describe pod <pod-name>`;
* сведения в журналах с помощью команды `kubectl log <pod-name>`.

Дополнительные сведения об устранении неполадок модуля pod см. в статье об [отладке приложений](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Я пытаюсь включить RBAC в существующем кластере. Как это сделать?

К сожалению, включение RBAC в существующих кластерах в настоящее время не поддерживается. Необходимо явно создать кластеры. Если вы используете CLI, RBAC включается по умолчанию. Если вы используете портал AKS, в рабочем процессе создания будет доступен переключатель для включения RBAC.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Кластер создан с поддержкой RBAC помощью Azure CLI со значениями по умолчанию или на портале Azure, при этом на панели мониторинга Kubernetes появились многочисленные предупреждения. Ранее на панели мониторинга не возникало никаких предупреждений. Что делать?

Причина отображения предупреждений на панели мониторинга заключается в том, что теперь в кластере включена функция RBAC, в которой доступ к этой панели отключен по умолчанию. В целом этот подход является рекомендуемым, так как раскрытие панели мониторинга по умолчанию для всех пользователей кластера может привести к угрозам безопасности. Если вы по-прежнему хотите включить панель мониторинга, следуйте указаниям в этом [блоге](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Мне не удается подключиться к панели мониторинга. Что делать?

Самый простой способ получить доступ к службе за пределами кластера — запустить `kubectl proxy`, который будет запрашивать ваш локальный порт 8001 для подключения к серверу API Kubernetes. После этого сервер API может установить прокси-подключение к вашей службе: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Если вы не видите панель мониторинга Kubernetes, проверьте, запущен ли модуль pod `kube-proxy` в пространстве имен `kube-system`. Если модуль pod находится в нерабочем состоянии, удалите его, и он перезапустится.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Мне не удается получить журналы kubectl, или подключение к серверу API завершается сбоем. Выводится ошибка Error from server: error dialing backend: dial tcp… (На сервере возникла ошибка: ошибка вызова серверной части: вызывается протокол tcp...). Что делать?

Убедитесь, что группа безопасности сети по умолчанию не изменена и порт 22 открыт для соединения с сервером API. Проверьте, запущен ли модуль pod `tunnelfront` в пространстве имен `kube-system`. Если он не запущен, принудительно удалите его, и он перезапустится.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>При попытке обновления или масштабирования я получаю подобное сообщение: Changing property 'imageReference' is not allowed (Не разрешено изменять свойство imageReference).  Как устранить эту проблему?

Возможно, вы получаете эту ошибку, потому что изменили теги в узлах агента внутри кластера AKS. Изменение и удаление тегов и других свойств ресурсов в группе ресурсов MC_* может привести к непредвиденным результатам. Изменение ресурсов в группе MC_* в кластере AKS нарушает цель уровня обслуживания (SLO).
