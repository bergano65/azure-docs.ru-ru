---
title: Развертывание самохотвожитого шлюза Управления API Azure в Кубернете (ru) Документы Майкрософт
description: Узнайте, как развернуть автономный шлюз управления API Azure в Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513839"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Развертывание самостоятельно госуправления API-извне ВКП в Кубернете

В этой статье представлены шаги по развертыванию самостоятельно размещенных шлюзов управления API Azure в кластер Kubernetes.

> [!NOTE]
> Функция самостоятельного входной шлюза находится в предварительном просмотре. Во время предварительного просмотра самоходной шлюз доступен только в уровнях разработчика и Premium без дополнительной платы. Уровень разработчика ограничен одним самоходном развертыванием шлюза.


## <a name="prerequisites"></a>Предварительные требования

- Завершите следующий быстрый запуск: [Создайте экземпляр управления API Azure](get-started-create-service-instance.md)
- Создайте кластер Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) является хорошим вариантом для разработки и оценки целей. Для производственных нагрузок можно использовать [службу Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) или кластер Kubernetes в иностранном облаке.
- [Предоставление ресурса шлюза в экземпляре Управления API.](api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-gateway-to-kubernetes"></a>Развертывание шлюза в Кубернете

1. Выберите **шлюзы** из-под **настроек.**
2. Выберите ресурс шлюза, который вы собираетесь развернуть.
3. Выберите **развертывание.**
4. Обратите внимание, что новый маркер в текстовом поле **Token** был автоматически генерирован для вас с помощью **значений истечения срока действия** по умолчанию и **секретных ключей.** Отрегулируйте один или оба при желании и выберите **Generate** для создания нового маркера.
5. Убедитесь, что **Kubernetes** выбран в соответствии со **скриптами развертывания.**
6. Выберите **<шлюз-имя>.yml** файл ссылку рядом с **развертыванием** для загрузки файла.
7. По мере необходимости отрегулируйте отображение портов и название контейнера в файле yml.
8. Выберите значок **копии,** расположенный в правом `kubectl` конце **текстового** окна Развертывание, чтобы сохранить команду буфера обмена. 
9. Вставьте команду в окно терминала (или команды). Обратите внимание, что команда ожидает, что загруженный файл среды будет присутствовать в текущем каталоге.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Выполните команду. Команда инструктирует кластер Kubernetes для запуска контейнера, используя самоходном изображении шлюза, загруженное из реестра контейнеров Майкрософт, и настроить контейнер для разоблачения портов HTTP (8080) и HTTPS (443).
11. Выполнить ниже команды, чтобы проверить шлюз стручка работает. Обратите внимание, что ваше имя стручка будет отличаться. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Выполнить ниже команды, чтобы проверить шлюз службы работает. Обратите внимание, что ваше имя службы будет отличаться. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Вернитесь на портал Azure и подтвердите, что только что развернутый узел шлюза сообщает о здоровом состоянии.

![Статус шлюза](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Используйте <code>kubectl logs <gateway-pod-name></code> команду для просмотра снимка самоходном журнала шлюзов.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать больше о самохлаженном шлюзе, смотрите [обзор самоходная сеть API Управления API Azure](self-hosted-gateway-overview.md)
* Узнайте больше о [сервисе Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)


