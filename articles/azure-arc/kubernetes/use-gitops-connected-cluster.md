---
title: Развертывание конфигураций с помощью Гитопс в кластере Kubernetes с поддержкой ARC (Предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Применение GitOps для настройки кластера с поддержкой Azure Arc (предварительная версия)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, служба Azure Kubernetes, контейнеры
ms.openlocfilehash: e25fdf3a51b3e9264c85707df31d3a4d107b25ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049980"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Развертывание конфигураций с помощью Гитопс в кластере Kubernetes с поддержкой ARC (Предварительная версия)

Гитопс — это практика объявления требуемого состояния конфигурации Kubernetes (развертываний, пространств имен и т. д.) в репозитории Git, за которым следуют опрос и развертывание этих конфигураций в кластере с помощью оператора. В этом документе рассматривается настройка таких рабочих процессов в кластерах Kubernetes с поддержкой дуги Azure.

Подключение между кластером и одним или несколькими репозиториями Git осуществляется в Azure Resource Manager как ресурс расширения `sourceControlConfiguration`. Свойства ресурса `sourceControlConfiguration` представляют, где и как ресурсы Kubernetes должны передаваться из Git в кластер. `sourceControlConfiguration`Данные хранятся в зашифрованном виде в базе данных Azure Cosmos DB для обеспечения конфиденциальности данных.

`config-agent`Выполнение в кластере несет ответственность за наблюдение за новыми или обновленными `sourceControlConfiguration` ресурсами расширения в ресурсе Kubernetes с включенной службой "Дуга Azure", развертывание оператора Flux для просмотра репозитория Git и распространение обновлений, внесенных в `sourceControlConfiguration` . Можно даже создать несколько `sourceControlConfiguration` ресурсов с `namespace` областью действия в одном кластере Kubernetes с поддержкой дуги Azure для достижения многопользовательской аренды. В этом случае каждый оператор может развертывать конфигурации только в соответствующее пространство имен.

Репозиторий Git может содержать любые допустимые ресурсы Kubernetes, включая пространства имен, ConfigMap, развертывания, Daemonset и т. д.  Он также может содержать диаграммы Helm для развертывания приложений. Типичный набор сценариев включает определение базовой конфигурации для организации, которая может включать общие роли и привязки RBAC, агенты наблюдения или ведения журнала, а также службы на уровне кластера.

Один и тот же шаблон можно использовать для управления более крупной коллекцией кластеров, которая может быть развернута в разнородных средах. Например, у вас может быть один репозиторий, который определяет базовую конфигурацию для организации и одновременно применяет ее к десяткам кластеров Kubernetes. [Политика Azure позволяет автоматизировать](use-azure-policy.md) создание `sourceControlConfiguration` с помощью определенного набора параметров во всех ресурсах Kubernetes с поддержкой ARC в пределах области (подписки или группы ресурсов).

В этом руководстве по началу работы будет рассмотрено применение набора конфигураций с областью администратора кластера.

## <a name="create-a-configuration"></a>Создание конфигурации

- Пример репозитория: <https://github.com/Azure/arc-k8s-demo>

Пример репозитория основан на операторе кластера, который хочет предоставить несколько пространств имен, развернуть общую рабочую нагрузку и предоставить некоторую конфигурацию для конкретной группы. Этот репозиторий создает в кластере следующие ресурсы:

**Пространства имен:** `cluster-config`, `team-a`, `team-b`
**Развертывание:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

`config-agent`Выполняет опрос Azure на наличие новых или обновленных событий `sourceControlConfiguration` каждые 30 секунд, что является максимальным временем, затраченным `config-agent` на выбор новой или обновленной конфигурации.
Если вы связываете частный репозиторий с `sourceControlConfiguration` , убедитесь, что вы также выполняете действия, описанные в разделе [Применение конфигурации из закрытого репозитория Git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Использование Azure CLI

Используя расширение Azure CLI для `k8sconfiguration` , свяжите подключенный кластер с [примером репозитория Git](https://github.com/Azure/arc-k8s-demo). Мы присвоим этой конфигурации имя `cluster-config`, укажем агенту развернуть оператор в пространстве имен `cluster-config` и предоставим оператору разрешения `cluster-admin`.

```console
az k8sconfiguration create \
    --name cluster-config \
    --cluster-name AzureArcTest1 --resource-group AzureArcTest \
    --operator-instance-name cluster-config --operator-namespace cluster-config \
    --repository-url https://github.com/Azure/arc-k8s-demo \
    --scope cluster --cluster-type connectedClusters
```

**Выходные данные:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>Параметр repository-url

Ниже приведены поддерживаемые сценарии для значения параметра --repository-url.

| Сценарий | Формат | Описание |
| ------------- | ------------- | ------------- |
| Частный репозиторий GitHub — SSH | git@github.com:username/repo | Пара ключей SSH, созданная Flux.  Пользователь должен добавить открытый ключ в учетную запись GitHub в качестве ключа развертывания. |
| Общедоступный репозиторий GitHub | `http://github.com/username/repo` или git://github.com/username/repo   | Общедоступный репозиторий Git  |

Эти сценарии поддерживаются Flux, но не sourceControlConfiguration. 

| Сценарий | Формат | Описание |
| ------------- | ------------- | ------------- |
| Частный репозиторий GitHub — HTTPS | `https://github.com/username/repo` | Flux не создает пару ключей SSH.  [Инструкции](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| Частный узел Git | user@githost:path/to/repo | [Инструкции](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| Частный репозиторий GitHub — SSH (с собственным ключом) | git@github.com:username/repo | [Использование собственной пары ключей SSH](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### <a name="additional-parameters"></a>Дополнительные параметры

Чтобы настроить создание конфигурации, изучите дополнительные параметры:

`--enable-helm-operator` : *Необязательный* параметр для включения поддержки развертываний диаграмм Helm.

`--helm-operator-chart-values` : *Необязательные* значения диаграммы для оператора Helm (если включено).  Например, --set helm.versions=v3.

`--helm-operator-chart-version` : *Необязательные* версии диаграммы для оператора Helm (если включено). Значение по умолчанию: 0.6.0.

`--operator-namespace` : *Необязательное* имя для пространства имен оператора. По умолчанию: default

`--operator-params` : *Необязательные* параметры для оператора. Должны быть заданы в одинарных кавычках. Например ```--operator-params='--git-readonly --git-path=releases' ```.

Параметры, поддерживаемые в параметре --operator-params

| Параметр | Описание |
| ------------- | ------------- |
| --git-branch  | Ветвь репозитория Git, используемая для манифестов Kubernetes. Значение по умолчанию — master. |
| --git-path  | Относительный путь в репозитории Git для Flux для поиска манифестов Kubernetes. |
| --git-readonly | Репозиторий Git будет считаться доступным только для чтения. Flux не будет пытаться выполнить запись в него. |
| --manifest-generation  | Если параметр включен, Flux будет искать .flux.yaml и запустит Kustomize или другие генераторы манифестов. |
| --git-poll-interval  | Период опроса репозитория Git на наличие новых фиксаций. По умолчанию — 5m (5 минут). |
| --sync-garbage-collection  | Если этот флажок установлен, Flux удалит ресурсы, которые были созданы, но теперь отсутствуют в Git. |
| --git-label  | Метка для отслеживания хода выполнения синхронизации, используемая для добавления тега в ветвь Git.  По умолчанию — flux-sync. |
| --git-user  | Имя пользователя для фиксации Git. |
| --git-email  | Электронная почта, используемая для фиксации Git. |

* Если параметры'--git-user или --git-email не заданы (что означает, что Flux не должен записывать в репозиторий), то параметр --git-readonly будет автоматически установлен (если он еще не задан).

* Если параметр enableHelmOperator имеет значение true, то длина строк operatorInstanceName и operatorNamespace не может превышать 47 символов.  Если не соблюдать это ограничение, возникнет следующая ошибка:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Дополнительные сведения см. в [документации по Flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> Вы можете создать Саурцеконтролконфигуратион на портал Azure, а также на вкладке **конфигурации** в колонке ресурсов Kubernetes в службе "Дуга Azure".

## <a name="validate-the-sourcecontrolconfiguration"></a>Проверка sourceControlConfiguration

С помощью Azure CLI убедитесь, что `sourceControlConfiguration` успешно создан.

```console
az k8sconfiguration show --resource-group AzureArcTest --name cluster-config --cluster-name AzureArcTest1 --cluster-type connectedClusters
```

Обратите внимание, что в ресурс `sourceControlConfiguration` сохраняются состояния соответствия, сообщения и отладочная информация.

**Выходные данные:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

При создании `sourceControlConfiguration` происходят следующие действия.

1. Azure Arc `config-agent` отслеживает Azure Resource Manager на наличие новых или обновленных конфигураций (`Microsoft.KubernetesConfiguration/sourceControlConfiguration`).
1. `config-agent` замечает новую конфигурацию `Pending`.
1. `config-agent` считывает свойства конфигурации и готовится к развертыванию управляемого экземпляра `flux`.
    * `config-agent` создает целевое пространство имен.
    * `config-agent` подготавливает учетную запись службы Kubernetes с соответствующим разрешением (область `cluster` или `namespace`).
    * `config-agent` развертывает экземпляр `flux`.
    * `flux`Создание ключа SSH и запись в журнал открытого ключа
1. `config-agent` сообщает о состоянии `sourceControlConfiguration`.

Во время процесса подготовки состояние `sourceControlConfiguration` будет несколько раз меняться. Отслеживайте ход выполнения с помощью приведенной выше команды `az k8sconfiguration show ...`:

1. `complianceStatus` -> `Pending`: представляет состояния начала и выполнения.
1. `complianceStatus` -> `Installed`: `config-agent` удалось успешно настроить кластер и развернуть `flux` без ошибок.
1. `complianceStatus` -> `Failed`: в `config-agent` возникла ошибка при развертывании `flux`, сведения должны быть доступны в тексте ответа `complianceStatus.message`.

## <a name="apply-configuration-from-a-private-git-repository"></a>Применение конфигурации из частного репозитория Git

Если вы используете закрытый репозиторий Git, необходимо выполнить еще одну задачу, чтобы закрыть цикл: Добавьте открытый ключ, созданный в `flux` качестве **ключа развертывания** в репозитории.

**Получение открытого ключа с помощью Azure CLI**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Получение открытого ключа на портале Azure**

1. На портале Azure перейдите к подключенному кластерному ресурсу.
2. На странице ресурсов выберите "Конфигурации" и просмотрите список конфигураций для этого кластера.
3. Выберите конфигурацию, которая использует частный репозиторий Git.
4. В открывшемся окне контекста в нижней части окна скопируйте **открытый ключ репозитория**.

**Добавление открытого ключа в репозиторий Git в качестве ключа развертывания**:

1. Откройте GitHub, перейдите к вилке, к разделу **Параметры**, а затем — **Ключи развертывания**.
2. Щелкните **Добавить ключ развертывания**.
3. Укажите заголовок.
4. Поставьте флажок **Разрешить доступ на запись**.
5. Вставьте открытый ключ (без кавычек).
6. Щелкните **Добавить ключ**.

Дополнительные сведения об управлении этими ключами см. в документации GitHub.

**Если вы используете репозиторий Azure DevOps, добавьте ключ в ключи SSH**:

1. В разделе **Параметры пользователя** в правом верхнем углу (рядом с изображением профиля) щелкните **Открытые ключи SSH**.
1. Выберите **+ Новый ключ**.
1. Укажите имя.
1. Вставьте открытый ключ без кавычек.
1. Нажмите кнопку **Добавить**

## <a name="validate-the-kubernetes-configuration"></a>Проверка конфигурации Kubernetes

Когда `config-agent` установит экземпляр `flux`, ресурсы в репозитории Git должны начать поступать в кластер. Убедитесь, что пространства имен, развертывания и ресурсы созданы:

```console
kubectl get ns --show-labels
```

**Выходные данные:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Мы видим, что пространства имен `team-a`, `team-b`, `itops` и `cluster-config` созданы.

Оператор `flux` был развернут в пространстве имен `cluster-config`, как указано в `sourceControlConfig`:

```console
kubectl -n cluster-config get deploy  -o wide
```

**Выходные данные:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Дальнейшее изучение

Вы можете изучить другие ресурсы, развернутые в рамках репозитория конфигурации:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Удаление конфигурации

Удалите объект `sourceControlConfiguration` с помощью Azure CLI или портал Azure.  После запуска команды DELETE `sourceControlConfiguration` ресурс будет удален немедленно в Azure, но для полного удаления связанных объектов из кластера может потребоваться до 1 часа (у нас есть элемент невыполненной работы, чтобы сократить эту задержку).

> [!NOTE]
> После создания Саурцеконтролконфигуратион с областью пространства имен пользователи с `edit` привязкой ролей в пространстве имен могут развертывать рабочие нагрузки в этом пространстве имен. При `sourceControlConfiguration` удалении этой области с пространством имен пространство имен остается неизменным и не будет удалено во избежание разрыва данных других рабочих нагрузок.
> Любые изменения в кластере, которые были результатом развертывания из репозитория отслеживаний Git, не удаляются при `sourceControlConfiguration` удалении.

```console
az k8sconfiguration delete --name '<config name>' -g '<resource group name>' --cluster-name '<cluster name>' --cluster-type connectedClusters
```

**Выходные данные:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Дальнейшие действия

- [Использование HELM с конфигурацией системы управления версиями](./use-gitops-with-helm.md)
- [Использование Политики Azure для управления конфигураций кластера](./use-azure-policy.md)
