---
title: Устранение распространенных проблем со Службой Azure Kubernetes
description: Узнайте, как устранить распространенные проблемы при использовании Службы Azure Kubernetes (AKS).
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: c20f2cc03565ce861dfc6317be8459fdafeef0bf
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384111"
---
# <a name="aks-troubleshooting"></a>Устранение неполадок с AKS
При создании кластеров AKS или управлении ими иногда вы можете сталкиваться с проблемами. В этой статье описаны некоторые распространенные проблемы и действия по устранению неполадок.

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>Где найти общие сведения об отладке проблем Kubernetes?

[Вот официальная ссылка](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/) на сведения об устранении неполадок с кластерами Kubernetes.
[Это](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) ссылка на руководство по устранению неполадок модулей pod, узлов, кластеров и т.д., опубликованное инженером Майкрософт.

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>Я получаю ошибку превышения квоты во время создания или обновления. Что делать? 

Вам необходимо запросить ядра [здесь](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>Какое максимальное число модулей pod на узел, установленное для AKS?

Если вы развертываете кластер AKS на портале Azure, максимальное число модулей pod на узел по умолчанию составляет 30.
Если вы развертываете кластер AKS с помощью Azure CLI, максимальное число модулей pod на узел по умолчанию составляет 110. (Убедитесь, что вы используете последнюю версию Azure CLI.) Это значение по умолчанию можно изменить с помощью параметра –max-nodes-per-pod в команде az aks create.

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Я получаю ошибку "insufficientSubnetSize" при развертывании кластера AKS с использованием расширенного сетевого взаимодействия. Что делать?

В варианте пользовательской конфигурации виртуальной сети, выбранном для сетей во время создания AKS, для IPAM используется Azure CNI. В кластере AKS может быть любое число узлов в диапазоне от 1 до 100. Исходя из вышесказанного, размер подсети должен быть больше количества узлов и максимального числа модулей pod на узел: размер подсети > количества узлов в кластере * максимальное число модулей на узел.

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Мой модуль pod завис в режиме "CrashLoopBackOff". Что делать?

Модуль pod может зависнуть в этом режиме по различным причинам. Вы можете просмотреть следующее: 
* сведения о самом модуле pod с помощью команды `kubectl describe pod <pod-name>`;
* журналы с помощью команды `kubectl log <pod-name>`.

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>Я пытаюсь включить RBAC в существующем кластере. Как это можно сделать?

К сожалению, включение RBAC в существующих кластерах в настоящее время не поддерживается. Необходимо явно создать новые кластеры. При использовании CLI функция RBAC включена по умолчанию. Ее выключатель доступен в рабочем процессе создания на портале AKS.

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>Кластер создан с помощью Azure CLI со значениями по умолчанию или на портале Azure с поддержкой RBAC, при этом на панели мониторинга Kubernetes появились многочисленные предупреждения. Ранее на панели мониторинга не возникали какие-либо предупреждения. Что делать?

Причина получения предупреждений на панели мониторинга заключается в том, что теперь включена функция RBAC, в которой доступ к этой панели отключен по умолчанию. В целом, этот подход рекомендуется, так как раскрытие панели мониторинга по умолчанию для всех пользователей кластера может привести к угрозам безопасности. Если вы по-прежнему хотите включить панель мониторинга, следуйте указаниям в этом [блоге](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>Мне не удается подключиться к панели мониторинга. Что делать?

Самый простой способ получить доступ к службе за пределами кластера — запустить прокси-сервер kubectl, который будет запрашивать ваш локальный порт 8001 для подключения к серверу API Kubernetes. После этого apiserver может установить прокси-подключение к вашей службе: http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default

Если вы не видите панель мониторинга Kubernetes, проверьте, запущен ли модуль pod прокси-сервера Kube в пространстве имен системы Kube. Если модуль pod находится в нерабочем состоянии, удалите его, и панель перезапустится.

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Мне не удалось получить журналы Kubectl, или подключение к серверу API завершается сбоем с ошибкой "Error from server: error dialing backend: dial tcp…" (На сервере возникла ошибка: ошибка вызова серверной части: вызывается протокол tcp...). Что делать?

Убедитесь, что группа безопасности сети по умолчанию не изменена и порт 22 открыт для соединения с сервером API. Проверьте, запущен ли модуль tunnelfront в пространстве имен системы Kube. Если он не запущен, принудительно удалите его, и он перезапустится.

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>При попытке обновления или масштабирования я получаю подобное сообщение: "Не разрешено изменять свойство imageReference". Ошибка.  Как устранить эту проблему?

Возможно, вы получаете эту ошибку, потому что изменили теги в узлах агента внутри кластера AKS. Изменение и удаление тегов и других свойств ресурсов в группе ресурсов MC_* может привести к непредвиденным результатам. Изменение ресурсов в группе MC_* в кластере AKS прерывает единый выход.

### <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>Как продлить срок действия секрета субъекта-службы в моем кластере AKS?

По умолчанию кластеры AKS создаются вместе с субъектом-службой, срок действия которого составляет один год. Когда будет истекать срок действия, вы можете сбросить учетные данные, чтобы продлить срок действия субъекта-службы на дополнительный период времени.

В следующем примере выполняются такие действия:

1. получение идентификатора субъекта-службы вашего кластера с помощью команды [az aks show](/cli/azure/aks#az-aks-show);
1. перечисление секретов клиента субъекта-службы с помощью команды [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list);
1. продление срока действия субъекта-службы на год с помощью команды [az ad sp credential-reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset). Для правильной работы секрет клиента субъекта-службы должен оставаться неизменным в кластере AKS.

```azurecli
# Get the service principal ID of your AKS cluster
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for 1 year
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
