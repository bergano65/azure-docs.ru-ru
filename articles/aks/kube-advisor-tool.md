---
title: Проверка развертываний Kubernetes в Azure для реализации рекомендаций
description: Сведений о проверке применения рекомендаций в развертываниях в Службе Azure Kubernetes с помощью kube-advisor
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 03c5eb2e32a0a8ec51844511276d9efba5651068
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "65073760"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Проверка применения рекомендаций Kubernetes в кластере

Чтобы обеспечить максимальную производительность и устойчивость приложений, следует соблюдать ряд рекомендаций в отношении развертываний Kubernetes. С помощью средства kube-advisor можно определить развертывания, которые не соответствуют этим рекомендациям.

## <a name="about-kube-advisor"></a>Сведения о kube-advisor

[Средство kube-advisor][kube-advisor-github] представляет собой контейнер, предназначенный для выполнения в кластере. Он запрашивает с сервера API Kubernetes сведения о ваших развертываниях и возвращает набор предложений по оптимизации.

Kube-помощник может отчетов на запросом ресурсов и ограничения, отсутствующие в PodSpecs для Windows-приложений, а также приложений Linux, а само средство kube помощника должно быть назначено на Linux pod. Вы можете запланировать pod для запуска в пуле узлов с определенной операционной системы с помощью [селектор узла] [ k8s-node-selector] в конфигурации pod.

> [!NOTE]
> Средство kube-advisor поддерживается корпорацией Майкрософт по мере возможности. Проблемы и предложения следует отправлять в GitHub.

## <a name="running-kube-advisor"></a>Запуск kube-advisor

Чтобы запустить средство в кластере, для которого настроено [управление доступом на основе ролей (RBAC)](azure-ad-integration.md), используйте приведенные ниже команды. Первая команда создает учетную запись службы Kubernetes. Вторая команда запускает средство в pod с помощью этой учетной записи службы и настраивает удаление pod после выхода. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Если вы не используете RBAC, то можете выполнить команду следующим образом:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Через несколько секунд должна появиться таблица с описанием возможных улучшений развертываний.

![Выходные данные kube-advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Выполняемые проверки

Средство проверяет выполнение ряда рекомендаций Kubernetes, предлагая для каждого собственный способ исправления.

### <a name="resource-requests-and-limits"></a>Запросы ресурсов и ограничения

Kubernetes поддерживает определение [запросов ресурсов и ограничений в спецификациях pod][kube-cpumem]. Запрос определяет минимальные ресурсы ЦП и памяти, необходимые для выполнения контейнера. Ограничение определяет максимальные допустимые ресурсы ЦП и памяти.

По умолчанию запросы и ограничения в спецификациях pod не заданы. Это может привести к чрезмерной загрузке узлов и нехватке ресурсов для контейнеров. Средство kube-advisor выявляет группы pod, для которых не заданы запросы и ограничения.

## <a name="cleaning-up"></a>Очистка.

Если для кластера включено управление RBAC, вы можете очистить `ClusterRoleBinding` после запуска средства с помощью следующей команды:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Если средство выполняется применительно к кластеру, для которого не включено управление RBAC, очистка не требуется.

## <a name="next-steps"></a>Дальнейшие действия

- [Устранение неполадок со службой Azure Kubernetes](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors