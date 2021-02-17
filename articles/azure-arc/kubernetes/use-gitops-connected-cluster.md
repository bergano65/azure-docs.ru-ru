---
title: Развертывание конфигураций с помощью GitOps в кластерах Kubernetes с поддержкой Arc (предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Использование Гитопс для настройки кластера Kubernetes с поддержкой дуги Azure (Предварительная версия)
keywords: Гитопс, Kubernetes, K8s, Azure, Arc, служба Kubernetes Azure, AKS, контейнеры
ms.openlocfilehash: 3cadcdf80abd997ec10aeb9521680944d455898f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560167"
---
# <a name="deploy-configurations-using-gitops-on-an-arc-enabled-kubernetes-cluster-preview"></a>Развертывание конфигураций с помощью Гитопс на кластере Kubernetes с поддержкой ARC (Предварительная версия)

В этой статье демонстрируется применение конфигураций в кластере Kubernetes с включенной службой "Дуга Azure". Концептуальный обзор такого же можно найти [здесь](./conceptual-configurations.md).

## <a name="before-you-begin"></a>Подготовка к работе

* Убедитесь, что у вас уже есть подключенный кластер Kubernetes с включенной службой "Дуга Azure". Если вам нужен подключенный кластер, ознакомьтесь с кратким руководством по подключению [кластера Azure ARC с поддержкой Kubernetes](./connect-cluster.md).

* Ознакомьтесь со [статьей конфигурации и гитопс с Arc для Kubernetes](./conceptual-configurations.md) , чтобы понять преимущества и архитектуру этой функции.

## <a name="create-a-configuration"></a>Создание конфигурации

[Пример репозитория](https://github.com/Azure/arc-k8s-demo) , используемый в этой статье, структурирован вокруг персонажа оператора кластера, который хотел бы предоставить несколько пространств имен, развернуть общую рабочую нагрузку и предоставить некоторую конфигурацию для конкретной группы. Этот репозиторий создает в кластере следующие ресурсы:

* **Пространства имен:** `cluster-config` , `team-a` , `team-b`
* **Развертывание:**`cluster-config/azure-vote`
* **ConfigMap:**`team-a/endpoints`

Выполняет `config-agent` опрос Azure на предмет новых или обновленных `sourceControlConfiguration` . Эта задача займет до 30 секунд.

Если вы связываете частный репозиторий с `sourceControlConfiguration` , выполните действия, описанные в разделе [Применение конфигурации из закрытого репозитория Git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Использование Azure CLI

Используйте расширение Azure CLI для, `k8sconfiguration` чтобы связать подключенный кластер с [примером репозитория Git](https://github.com/Azure/arc-k8s-demo). 
1. Назовите эту конфигурацию `cluster-config` .
1. Попросите агента развернуть оператор в `cluster-config` пространстве имен.
1. Присвойте оператору `cluster-admin` разрешения.

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
| `--repository-url` | HTTP [s]://сервер/репо [. git] или Git://сервер/репо [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Использование закрытого репозитория Git с ключами, созданными с помощью SSH и Flux

| Параметр | Формат | Примечания
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] или user@server:repo [. git] | `git@` может заменить `user@`

> [!NOTE]
> Открытый ключ, созданный Flux, должен быть добавлен в учетную запись пользователя в поставщике службы Git. Если ключ добавляется в репозиторий, а не в учетную запись пользователя, используйте вместо `git@` `user@` в URL-адресе. Для получения дополнительных сведений перейдите к разделу [Применение конфигурации из закрытого репозитория Git](#apply-configuration-from-a-private-git-repository) .

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Использование закрытого репозитория Git с ключами SSH и пользователями

| Параметр | Формат | Примечания |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] или user@server:repo [. git] | `git@` может заменить `user@` |
| `--ssh-private-key` | ключ в кодировке Base64 в [формате PEM](https://aka.ms/PEMformat) | Предоставить ключ напрямую |
| `--ssh-private-key-file` | полный путь к локальному файлу | Укажите полный путь к локальному файлу, содержащему ключ PEM-Format

> [!NOTE]
> Предоставьте собственный закрытый ключ непосредственно или в файле. Ключ должен быть в [формате PEM](https://aka.ms/PEMformat) и заканчиваться новой строкой (\n).  Связанный открытый ключ должен быть добавлен в учетную запись пользователя в поставщике службы Git. Если ключ добавляется в репозиторий, а не в учетную запись пользователя, используйте вместо `git@` `user@` . Для получения дополнительных сведений перейдите к разделу [Применение конфигурации из закрытого репозитория Git](#apply-configuration-from-a-private-git-repository) .

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Использование закрытого узла Git с известными узлами SSH и указанными пользователем

| Параметр | Формат | Примечания |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] или user@server:repo [. git] | `git@` может заменить `user@` |
| `--ssh-known-hosts` | в кодировке Base64 | Непосредственное предоставление содержимого известного узла |
| `--ssh-known-hosts-file` | полный путь к локальному файлу | Предоставление содержимого известных узлов в локальном файле |

> [!NOTE]
> Для проверки подлинности репозитория Git перед установкой SSH-подключения оператор Flux сохраняет список общих узлов Git в известном файле hosts. Если вы используете нестандартный репозиторий Git или собственный узел Git, может потребоваться указать ключ узла, чтобы Flux мог опознать ваш репозиторий. Вы можете предоставить содержимое known_hosts непосредственно или в файле. Используйте [спецификации формата содержимого known_hosts](https://aka.ms/KnownHostsFormat) в сочетании с одним из описанных выше сценариев использования ключей SSH при предоставлении собственного содержимого.

#### <a name="use-a-private-git-repo-with-https"></a>Использование частного репозитория Git с HTTPS

| Параметр | Формат | Примечания |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS с обычной проверкой подлинности |
| `--https-user` | RAW или Base64-кодировка | Имя пользователя HTTPS |
| `--https-key` | RAW или Base64-кодировка | Личный маркер доступа или пароль HTTPS

> [!NOTE]
> Закрытая проверка подлинности HTTPS Helm поддерживается только с помощью Helm оператора версии 1.2.0 + (по умолчанию).
> Частная проверка подлинности HTTPS Helm Release не поддерживается в настоящее время для кластеров, управляемых в Azure Kubernetes Services.
> Если вам требуется Flux для доступа к репозиторию Git через прокси-сервер, вам потребуется обновить агенты Arc Azure с помощью параметров прокси-сервера. Дополнительные сведения см. в разделе [подключение с использованием исходящего прокси-сервера](./connect-cluster.md#connect-using-an-outbound-proxy-server).

#### <a name="additional-parameters"></a>Дополнительные параметры

Настройте конфигурацию со следующими необязательными параметрами:

| Параметр | Описание |
| ------------- | ------------- |
| `--enable-helm-operator`| Переключитесь, чтобы включить поддержку для развертываний диаграмм Helm. |
| `--helm-operator-params` | Значения диаграммы для оператора Helm (если включено). Например, `--set helm.versions=v3`. |
| `--helm-operator-version` | Версия диаграммы для оператора Helm (если включено). Используйте версию 1.2.0 +. Значение по умолчанию: "1.2.0". |
| `--operator-namespace` | Имя для пространства имен оператора. Значение по умолчанию: "default". Максимум: 23 символа. |
| `--operator-params` | Параметры для оператора. Должны быть заданы в одинарных кавычках. Например ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ```. 

##### <a name="options-supported-in----operator-params"></a>Поддерживаемые параметры в  `--operator-params` :

| Параметр | Описание |
| ------------- | ------------- |
| `--git-branch`  | Ветвь репозитория Git, используемая для манифестов Kubernetes. Значение по умолчанию — master. В новых репозиториях есть корневая ветвь с именем `main` . в этом случае необходимо задать `--git-branch=main` . |
| `--git-path`  | Относительный путь в репозитории Git для Flux для поиска манифестов Kubernetes. |
| `--git-readonly` | Репозиторий Git будет считаться доступным только для чтения. Flux не будет пытаться выполнить запись в него. |
| `--manifest-generation`  | Если параметр включен, Flux будет искать .flux.yaml и запустит Kustomize или другие генераторы манифестов. |
| `--git-poll-interval`  | Период опроса репозитория Git на наличие новых фиксаций. Значение по умолчанию — `5m` (5 минут). |
| `--sync-garbage-collection`  | Если этот флажок установлен, Flux удалит ресурсы, которые были созданы, но теперь отсутствуют в Git. |
| `--git-label`  | Метка для отслеживания хода выполнения синхронизации. Используется для добавления тега в ветвь Git.  Значение по умолчанию — `flux-sync`. |
| `--git-user`  | Имя пользователя для фиксации Git. |
| `--git-email`  | Электронная почта, используемая для фиксации Git. 

Если вы не хотите, чтобы Flux записи в репозиторий `--git-user` и `--git-email` не были заданы, `--git-readonly` будет автоматически задано значение.

Дополнительные сведения см. в [документации по Flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> Вы можете создать на `sourceControlConfiguration` портал Azure на вкладке **Гитопс** ресурса Kubernetes в службе Arc Azure.

## <a name="validate-the-sourcecontrolconfiguration"></a>Проверка sourceControlConfiguration

Используйте Azure CLI, чтобы проверить `sourceControlConfiguration` успешность создания.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`Ресурс будет обновлен с учетом состояния соответствия, сообщений и отладочной информации.

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

1. В службе "Дуга Azure" `config-agent` отслеживаются Azure Resource Manager для новых или обновленных конфигураций ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) и Заметится Новая `Pending` Конфигурация.
1. `config-agent`Считывает свойства конфигурации и создает целевое пространство имен.
1. Дуга Azure `controller-manager` подготавливает учетную запись службы Kubernetes с соответствующим разрешением ( `cluster` или `namespace` областью), а затем развертывает экземпляр `flux` .
1. При использовании параметра SSH с ключами, созданными Flux, `flux` создает ключ SSH и записывает в журнал открытый ключ.
1. `config-agent`Возвращает отчет о состоянии в `sourceControlConfiguration` ресурс в Azure.

Во время процесса подготовки состояние `sourceControlConfiguration` будет несколько раз меняться. Отслеживайте ход выполнения с помощью приведенной выше команды `az k8sconfiguration show ...`:

| Изменение этапа | Описание |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Представляет начальное и выполняющееся состояния. |
| `complianceStatus` -> `Installed`  | `config-agent` удалось успешно настроить кластер и выполнить развертывание `flux` без ошибок. |
| `complianceStatus` -> `Failed` | `config-agent` обнаружена ошибка при развертывании `flux` , сведения должны быть доступны в `complianceStatus.message` тексте ответа. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Применение конфигурации из закрытого репозитория Git

Если вы используете частный репозиторий Git, необходимо настроить открытый ключ SSH в репозитории. В качестве открытого ключа SSH будет использоваться либо тот, который Flux создает, либо предоставленный вами. Открытый ключ можно настроить в определенном репозитории Git или на пользователе Git, который имеет доступ к репозиторию. 

### <a name="get-your-own-public-key"></a>Получение собственного открытого ключа

Если вы создали собственные ключи SSH, у вас уже есть закрытые и открытые ключи.

#### <a name="get-the-public-key-using-azure-cli"></a>Получение открытого ключа с помощью Azure CLI 

Следующий пример полезен, если Flux создает ключи.

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Получение открытого ключа на портале Azure

Следующий пример полезен, если Flux создает ключи.

1. На портале Azure перейдите к подключенному кластерному ресурсу.
2. На странице ресурсов выберите "Гитопс" и просмотрите список конфигураций для этого кластера.
3. Выберите конфигурацию, которая использует частный репозиторий Git.
4. В открывшемся окне контекста в нижней части окна скопируйте **открытый ключ репозитория**.

#### <a name="add-public-key-using-github"></a>Добавление открытого ключа с помощью GitHub

Используйте один из следующих методов.

* Вариант 1. Добавьте открытый ключ в учетную запись пользователя (применяется ко всем репозиториев в вашей учетной записи):  
    1. Откройте GitHub и щелкните значок профиля в правом верхнем углу страницы.
    2. Щелкните **Параметры**.
    3. Щелкните **ключи SSH и GPG**.
    4. Щелкните **новый ключ SSH**.
    5. Укажите заголовок.
    6. Вставьте открытый ключ без окружающих кавычек.
    7. Щелкните **Добавить ключ SSH**.

* Вариант 2. Добавьте открытый ключ как ключ развертывания в репозиторий Git (применяется только к этому репозиторию):  
    1. Откройте GitHub и перейдите в свой репозиторий.
    1. Щелкните **Параметры**.
    1. Щелкните **развернуть ключи**.
    1. Щелкните **Добавить ключ развертывания**.
    1. Укажите заголовок.
    1. Установите флажок **Разрешить доступ на запись**.
    1. Вставьте открытый ключ без окружающих кавычек.
    1. Нажмите кнопку **Добавить раздел**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Добавление открытого ключа с помощью репозитория Azure DevOps

Чтобы добавить ключ в ключи SSH, выполните следующие действия.

1. В разделе **Параметры пользователя** в правом верхнем углу (рядом с изображением профиля) щелкните **открытые ключи SSH**.
1. Выберите  **+ новый ключ**.
1. Укажите имя.
1. Вставьте открытый ключ без окружающих кавычек.
1. Нажмите кнопку **Добавить**.

## <a name="validate-the-kubernetes-configuration"></a>Проверка конфигурации Kubernetes

После `config-agent` установки `flux` экземпляра ресурсы, удерживаемые в репозитории Git, должны начать передаваться в кластер. Убедитесь, что пространства имен, развертывания и ресурсы созданы с помощью следующей команды:

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

Другие ресурсы, развернутые в составе репозитория конфигурации, можно исследовать с помощью:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Удаление конфигурации

Удалите объект `sourceControlConfiguration` с помощью Azure CLI или портал Azure.  После запуска команды DELETE `sourceControlConfiguration` ресурс будет немедленно удален в Azure. Полное удаление связанных объектов из кластера должно выполняться в течение 10 минут. Если `sourceControlConfiguration` при удалении состояние находится в неисправном состоянии, полное удаление связанных объектов может занять до часа.

> [!NOTE]
> После `sourceControlConfiguration` `namespace` создания области с областью действия пользователи с `edit` привязкой ролей в пространстве имен могут развертывать рабочие нагрузки в этом пространстве имен. При `sourceControlConfiguration` удалении этой области с пространством имен пространство имен остается неизменным и не будет удалено во избежание разрыва данных других рабочих нагрузок. При необходимости это пространство имен можно удалить вручную с помощью `kubectl` .  
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
