---
title: Развертывание конфигураций с помощью GitOps в кластерах Kubernetes с поддержкой Arc (предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Использование Гитопс для настройки кластера Kubernetes с поддержкой Arc Azure (Предварительная версия)
keywords: Гитопс, Kubernetes, K8s, Azure, Arc, служба Kubernetes Azure, AKS, контейнеры
ms.openlocfilehash: 72dc42fffb3653de81477fa504c11b9b0328d2eb
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988701"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Развертывание конфигураций с помощью GitOps в кластерах Kubernetes с поддержкой Arc (предварительная версия)

Гитопс, так как он связан с Kubernetes, — это практика объявления требуемого состояния Kubernetes конфигурации (развертываний, пространств имен и т. д.) в репозитории Git, за которым следуют опрос и развертывание этих конфигураций в кластере с помощью оператора. В этом документе рассматривается настройка таких рабочих процессов в кластерах Kubernetes с поддержкой дуги Azure.

Подключение между кластером и репозиторием Git создается в Azure Resource Manager как `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ресурс расширения. Свойства ресурса `sourceControlConfiguration` представляют, где и как ресурсы Kubernetes должны передаваться из Git в кластер. `sourceControlConfiguration`Данные хранятся в зашифрованном виде в базе данных Azure Cosmos DB для обеспечения конфиденциальности данных.

`config-agent`Выполнение в кластере несет ответственность за наблюдение за новыми или обновленными `sourceControlConfiguration` ресурсами расширения в ресурсе Kubernetes с включенной службой "Дуга Azure" для развертывания оператора Flux для просмотра репозитория Git для каждого из них `sourceControlConfiguration` и применения любых обновлений, внесенных в любые `sourceControlConfiguration` . Можно создать несколько `sourceControlConfiguration` ресурсов в одном кластере Kubernetes с поддержкой дуги Azure для достижения нескольких целей. Можно создать каждый `sourceControlConfiguration` с другой `namespace` областью, чтобы ограничить развертывания в пределах соответствующих пространств имен.

Репозиторий Git может содержать манифесты в формате YAML, описывающие любые допустимые ресурсы Kubernetes, включая пространства имен, Конфигмапс, развертывания, Daemonset и т. д.  Он также может содержать Helm диаграммы для развертывания приложений. Типичный набор сценариев включает определение базовой конфигурации для Организации, которая может включать в себя общие роли и привязки Azure, агенты наблюдения или ведения журнала, а также службы на уровне кластера.

Один и тот же шаблон можно использовать для управления более крупной коллекцией кластеров, которая может быть развернута в разнородных средах. Например, у вас может быть один репозиторий, который определяет базовую конфигурацию для организации и одновременно применяет ее к десяткам кластеров Kubernetes. [Политика Azure позволяет автоматизировать](use-azure-policy.md) создание `sourceControlConfiguration` с помощью определенного набора параметров во всех ресурсах Kubernetes с поддержкой ARC в пределах области (подписки или группы ресурсов).

В этом руководстве по началу работы будет рассмотрено применение набора конфигураций с областью администратора кластера.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть подключенный кластер Kubernetes с поддержкой Azure Arc. Если вам нужно создать подключенный кластер, см. это [краткое руководство](./connect-cluster.md).

## <a name="create-a-configuration"></a>Создание конфигурации

[Пример репозитория](https://github.com/Azure/arc-k8s-demo) , используемый в этом документе, структурирован вокруг персонажа оператора кластера, который хочет предоставить несколько пространств имен, развернуть общую рабочую нагрузку и предоставить некоторую конфигурацию для конкретной группы. Этот репозиторий создает в кластере следующие ресурсы:

**Пространства имен:** `cluster-config`, `team-a`, `team-b`
**Развертывание:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

`config-agent`Выполняет опрос Azure на наличие новых или обновленных событий `sourceControlConfiguration` каждые 30 секунд, что является максимальным временем, затраченным `config-agent` на выбор новой или обновленной конфигурации.
Если вы связываете частный репозиторий с `sourceControlConfiguration` , убедитесь, что вы также выполняете действия, описанные в разделе [Применение конфигурации из закрытого репозитория Git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Использование Azure CLI

Используйте расширение Azure CLI для, `k8sconfiguration` чтобы связать подключенный кластер с [примером репозитория Git](https://github.com/Azure/arc-k8s-demo). Мы присвоим этой конфигурации имя `cluster-config`, укажем агенту развернуть оператор в пространстве имен `cluster-config` и предоставим оператору разрешения `cluster-admin`.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Выходные данные:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Pending",
    "lastConfigApplied": "0001-01-01T00:00:00",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": null,
  "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
  "resourceGroup": "MyRG",
  "sshKnownHostsContents": "",
  "systemData": {
    "createdAt": "2020-11-24T21:22:01.542801+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
  ```

#### <a name="use-a-public-git-repo"></a>Использование общедоступного репозитория Git

| Параметр | Формат |
| ------------- | ------------- |
| --репозиторий — URL-адрес | HTTP [s]://сервер/репо [. git] или Git://сервер/репо [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Использование закрытого репозитория Git с ключами, созданными с помощью SSH и Flux

| Параметр | Формат | Примечания
| ------------- | ------------- | ------------- |
| --репозиторий — URL-адрес | ssh://user@server/repo[. git] или user@server:repo [. git] | `git@` может заменить `user@`

> [!NOTE]
> Открытый ключ, созданный Flux, должен быть добавлен в учетную запись пользователя в поставщике службы Git. Если ключ добавляется в репозиторий вместо > учетной записи пользователя, используйте вместо `git@` `user@` в URL-адресе. [Просмотреть дополнительные сведения](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Использование закрытого репозитория Git с ключами SSH и пользователями

| Параметр | Формат | Примечания |
| ------------- | ------------- | ------------- |
| --репозиторий — URL-адрес  | ssh://user@server/repo[. git] или user@server:repo [. git] | `git@` может заменить `user@` |
| --SSH-закрытый-ключ | ключ в кодировке Base64 в [формате PEM](https://aka.ms/PEMformat) | Предоставить ключ напрямую |
| --SSH-частный-Key-file | полный путь к локальному файлу | Укажите полный путь к локальному файлу, содержащему ключ PEM-Format

> [!NOTE]
> Предоставьте собственный закрытый ключ непосредственно или в файле. Ключ должен быть в [формате PEM](https://aka.ms/PEMformat) и заканчиваться новой строкой (\n).  Связанный открытый ключ должен быть добавлен в учетную запись пользователя в поставщике службы Git. Если ключ добавляется в репозиторий, а не в учетную запись пользователя, используйте вместо `git@` `user@` . [Просмотреть дополнительные сведения](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Использование закрытого узла Git с известными узлами SSH и указанными пользователем

| Параметр | Формат | Примечания |
| ------------- | ------------- | ------------- |
| --репозиторий — URL-адрес  | ssh://user@server/repo[. git] или user@server:repo [. git] | `git@` может заменить `user@` |
| --SSH-known-hosts | в кодировке Base64 | известное содержимое узла, предоставленное напрямую |
| --SSH-known-hosts-File | полный путь к локальному файлу | содержимое узла «известные узлы», предоставленное в локальном файле

> [!NOTE]
> Оператор Flux поддерживает список общих узлов Git в известном файле hosts для проверки подлинности репозитория Git перед установкой SSH-подключения. Если вы используете нестандартный репозиторий Git или собственный узел Git, может потребоваться указать ключ узла, чтобы Flux мог опознать ваш репозиторий. Вы можете предоставить содержимое известного узла непосредственно или в файле. [Просмотр спецификации формата содержимого известных узлов](https://aka.ms/KnownHostsFormat).
> Его можно использовать в сочетании с одним из описанных выше сценариев ключей SSH.

#### <a name="use-a-private-git-repo-with-https"></a>Использование частного репозитория Git с HTTPS

| Параметр | Формат | Примечания |
| ------------- | ------------- | ------------- |
| --репозиторий — URL-адрес | https://server/repo[. git] | HTTPS с обычной проверкой подлинности |
| --HTTPS-пользователь | RAW или Base64-кодировка | Имя пользователя HTTPS |
| --HTTPS-ключ | RAW или Base64-кодировка | Личный маркер доступа или пароль HTTPS

> [!NOTE]
> Закрытая проверка подлинности HTTPS Helm поддерживается только с версией диаграммы оператора Helm >= 1.2.0.  Версия 1.2.0 используется по умолчанию.
> Частная проверка подлинности HTTPS Helm Release не поддерживается в настоящее время для управляемых кластеров Azure Kubernetes Services.
> Если вам требуется Flux для доступа к репозиторию Git через прокси-сервер, вам потребуется обновить агенты Arc Azure с помощью параметров прокси-сервера. [Дополнительные сведения](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>Дополнительные параметры

Чтобы настроить конфигурацию, можно использовать следующие параметры:

`--enable-helm-operator` : *Необязательный* параметр для включения поддержки развертываний диаграмм Helm.

`--helm-operator-params` : *Необязательные* значения диаграммы для оператора Helm (если включено).  Например, --set helm.versions=v3.

`--helm-operator-version` : *Необязательные* версии диаграммы для оператора Helm (если включено). Используйте "1.2.0" или выше. Значение по умолчанию: "1.2.0".

`--operator-namespace` : *Необязательное* имя для пространства имен оператора. Значение по умолчанию: "default". Максимум 23 символа.

`--operator-params` : *Необязательные* параметры для оператора. Должны быть заданы в одинарных кавычках. Например ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ```.

Параметры, поддерживаемые в параметре --operator-params

| Параметр | Описание |
| ------------- | ------------- |
| --git-branch  | Ветвь репозитория Git, используемая для манифестов Kubernetes. Значение по умолчанию — master. В новых репозиториях есть корневая ветвь с именем "Main". в этом случае необходимо задать параметр--git-branch = Main. |
| --git-path  | Относительный путь в репозитории Git для Flux для поиска манифестов Kubernetes. |
| --git-readonly | Репозиторий Git будет считаться доступным только для чтения. Flux не будет пытаться выполнить запись в него. |
| --manifest-generation  | Если параметр включен, Flux будет искать .flux.yaml и запустит Kustomize или другие генераторы манифестов. |
| --git-poll-interval  | Период опроса репозитория Git на наличие новых фиксаций. По умолчанию — 5m (5 минут). |
| --sync-garbage-collection  | Если этот флажок установлен, Flux удалит ресурсы, которые были созданы, но теперь отсутствуют в Git. |
| --git-label  | Метка для отслеживания хода выполнения синхронизации, используемая для добавления тега в ветвь Git.  По умолчанию — flux-sync. |
| --git-user  | Имя пользователя для фиксации Git. |
| --git-email  | Электронная почта, используемая для фиксации Git. |

* Если параметры'--git-user или --git-email не заданы (что означает, что Flux не должен записывать в репозиторий), то параметр --git-readonly будет автоматически установлен (если он еще не задан).

Дополнительные сведения см. в [документации по Flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> Вы можете создать Саурцеконтролконфигуратион в портал Azure на вкладке **гитопс** ресурса Kubernetes в службе Arc Azure.

## <a name="validate-the-sourcecontrolconfiguration"></a>Проверка sourceControlConfiguration

С помощью Azure CLI убедитесь, что `sourceControlConfiguration` успешно создан.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Обратите внимание, что в ресурс `sourceControlConfiguration` сохраняются состояния соответствия, сообщения и отладочная информация.

**Выходные данные:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
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
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Когда `sourceControlConfiguration` создается или обновляется объект, происходит несколько моментов:

1. Служба "Дуга Azure" `config-agent` отслеживает Azure Resource Manager новых или обновленных конфигураций ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) и обратит внимание на новую `Pending` конфигурацию.
1. `config-agent`Считывает свойства конфигурации и создает целевое пространство имен.
1. Дуга Azure `controller-manager` подготавливает учетную запись службы Kubernetes с соответствующим разрешением ( `cluster` или `namespace` областью), а затем развертывает экземпляр `flux` .
1. При использовании параметра SSH с ключами, созданными Flux, `flux` создает ключ SSH и записывает в журнал открытый ключ.
1. `config-agent`Возвращает отчет о состоянии в `sourceControlConfiguration` ресурс в Azure.

Во время процесса подготовки состояние `sourceControlConfiguration` будет несколько раз меняться. Отслеживайте ход выполнения с помощью приведенной выше команды `az k8sconfiguration show ...`:

1. `complianceStatus` -> `Pending`: представляет состояния начала и выполнения.
1. `complianceStatus` -> `Installed`: `config-agent` удалось успешно настроить кластер и развернуть `flux` без ошибок.
1. `complianceStatus` -> `Failed`: в `config-agent` возникла ошибка при развертывании `flux`, сведения должны быть доступны в тексте ответа `complianceStatus.message`.

## <a name="apply-configuration-from-a-private-git-repository"></a>Применение конфигурации из закрытого репозитория Git

Если вы используете частный репозиторий Git, необходимо настроить открытый ключ SSH в репозитории. Открытый ключ можно настроить в определенном репозитории Git или на пользователе Git, который имеет доступ к репозиторию. Открытый ключ SSH будет либо предоставлен вами, либо Flux, который создает.

**Получение собственного открытого ключа**

Если вы создали собственные ключи SSH, у вас уже есть закрытые и открытые ключи.

**Получение открытого ключа с помощью Azure CLI (полезно, если Flux создает ключи)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Получение открытого ключа из портал Azure (полезно, если Flux создает ключи)**

1. На портале Azure перейдите к подключенному кластерному ресурсу.
2. На странице ресурсов выберите "Гитопс" и просмотрите список конфигураций для этого кластера.
3. Выберите конфигурацию, которая использует частный репозиторий Git.
4. В открывшемся окне контекста в нижней части окна скопируйте **открытый ключ репозитория**.

Если вы используете GitHub, воспользуйтесь одним из следующих двух вариантов:

**Вариант 1. Добавьте открытый ключ в учетную запись пользователя (применяется ко всем репозиториев в вашей учетной записи).**

1. Откройте GitHub, щелкните значок профиля в правом верхнем углу страницы.
2. Щелкните **Параметры** .
3. Щелкните **ключи SSH и GPG** .
4. Щелкните **новый ключ SSH** .
5. Укажите заголовок.
6. Вставьте открытый ключ (без кавычек).
7. Щелкните " **Добавить ключ SSH** "

**Вариант 2. Добавьте открытый ключ как ключ развертывания в репозиторий Git (применяется только к этому репозиторию)**

1. Откройте GitHub, перейдите к репозиторию, щелкните " **Параметры**" и **разверните "ключи** ".
2. Щелкните " **Добавить ключ развертывания** ".
3. Укажите заголовок.
4. Поставьте флажок **Разрешить доступ на запись**.
5. Вставьте открытый ключ (без кавычек).
6. Щелкните " **Добавить ключ** ".

**Если вы используете репозиторий Azure DevOps, добавьте ключ в ключи SSH**:

1. В разделе **Параметры пользователя** в правом верхнем углу (рядом с изображением профиля) щелкните **Открытые ключи SSH**.
1. Выберите **+ Новый ключ**.
1. Укажите имя.
1. Вставьте открытый ключ без кавычек.
1. Нажмите кнопку **Добавить**

## <a name="validate-the-kubernetes-configuration"></a>Проверка конфигурации Kubernetes

После `config-agent` установки `flux` экземпляра ресурсы, удерживаемые в репозитории Git, должны начать передаваться в кластер. Убедитесь, что пространства имен, развертывания и ресурсы созданы:

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

Удалите объект `sourceControlConfiguration` с помощью Azure CLI или портал Azure.  После запуска команды DELETE `sourceControlConfiguration` ресурс будет удален немедленно в Azure, а полное удаление связанных объектов из кластера должно выполняться в течение 10 минут.  Если `sourceControlConfiguration` при удалении состояние находится в неисправном состоянии, полное удаление связанных объектов может занять до часа.

> [!NOTE]
> После создания Саурцеконтролконфигуратион с областью пространства имен пользователи с `edit` привязкой ролей в пространстве имен могут развертывать рабочие нагрузки в этом пространстве имен. При `sourceControlConfiguration` удалении этой области с пространством имен пространство имен остается неизменным и не будет удалено во избежание разрыва данных других рабочих нагрузок.  При необходимости можно удалить это пространство имен вручную с помощью kubectl.
> Любые изменения в кластере, которые были результатом развертывания из репозитория отслеживаний Git, не удаляются при `sourceControlConfiguration` удалении.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Выходные данные:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Дальнейшие действия

- [Использование HELM с конфигурацией системы управления версиями](./use-gitops-with-helm.md)
- [Использование Политики Azure для управления конфигураций кластера](./use-azure-policy.md)