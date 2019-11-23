---
title: Устранение неисправностей
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Быстрая разработка Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: 'Docker, Kubernetes, Azure, служба контейнеров Azure, служба Azure Kubernetes, контейнеры, Helm, сетка службы, сетка службы маршрутизации, kubectl, k8s '
ms.openlocfilehash: f1d3c0aa9827582f0c928c4700ebef935ae2d1c3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424061"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces troubleshooting

Это руководство содержит сведения о распространенных проблемах, которые могут возникнуть при использовании Azure Dev Spaces.

If you have a problem when using Azure Dev Spaces, create an [issue in the Azure Dev Spaces GitHub repository](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Перед началом работы

Чтобы более эффективно устранять неполадки, можно создать более подробные журналы для проверки.

Для расширения Visual Studio задайте переменной среды `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` значение 1. Обязательно перезапустите Visual Studio, чтобы изменения переменной среды вступили в силу. После включения подробные журналы будут записаны в каталог `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

В CLI вы можете выводить больше сведений во время выполнения команды, используя параметр `--verbose`. Вы также можете просмотреть более подробные журналы в `%TEMP%\Azure Dev Spaces`. На компьютере Mac каталог TEMP можно найти, выполнив `echo $TMPDIR` в окне терминала. На компьютере с ОС Linux каталог TEMP обычно называется `/tmp`.

Azure Dev Spaces also works best when debugging a single instance, or pod. The `azds.yaml` file contains a setting, *replicaCount*, that indicates the number of pods that Kubernetes runs for your service. If you change the *replicaCount* to configure your application to run multiple pods for a given service, the debugger attaches to the first pod, when listed alphabetically. Отладчик присоединяется к другому модулю pod при перезапуске оригинального модуля pod, что может привести к непредвиденному поведению.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Common issues when enabling Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Error "Failed to create Azure Dev Spaces controller"

Вы можете увидеть эту ошибку, если что-то пойдет не так с созданием контроллера. Если это временная ошибка, ее можно устранить, удалив контроллер, а затем повторно создав его.

You can also try deleting the controller:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Use the Azure Dev Spaces CLI to delete a controller. It’s not possible to delete a controller from Visual Studio. You also can't install the Azure Dev Spaces CLI in the Azure Cloud Shell so you can't delete a controller from the Azure Cloud Shell.

If you don't have the Azure Dev Spaces CLI installed, you can first install it using the following command then delete your controller:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Чтобы повторно создать контроллер, можно использовать CLI или Visual Studio. See the [Team development](quickstart-team-development.md) or [Develop with .NET Core](quickstart-netcore-visualstudio.md) quickstarts for examples.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Controller create failing because of controller name length

An Azure Dev Spaces controller's name can't be longer than 31 characters. If your controller's name exceeds 31 characters when you enable Dev Spaces on an AKS cluster or create a controller, you'll receive an error. Пример.

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

To fix this issue, create a controller with an alternate name. Пример.

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Enabling Dev Spaces failing when Windows node pools are added to an AKS cluster

Currently, Azure Dev Spaces is intended to run on Linux pods and nodes only. When you have an AKS cluster with a Windows node pool, you must ensure that Azure Dev Spaces pods are only scheduled on Linux nodes. If an Azure Dev Spaces pod is scheduled to run on a Windows node, that pod won't start and enabling Dev Spaces will fail.

To fix this issue, [add a taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) to your AKS cluster to ensure Linux pods aren't scheduled to run on a Windows node.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Error "Found no untainted Linux nodes in Ready state on the cluster. There needs to be at least one untainted Linux node in Ready state to deploy pods in 'azds' namespace."

Azure Dev Spaces couldn't create a controller on your AKS cluster because it couldn't find an untainted node in a *Ready* state to schedule pods on. Azure Dev Spaces requires at least one Linux node in a *Ready* state that allows for scheduling pods without specifying tolerations.

To fix this issue, [update your taint configuration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) on your AKS cluster to ensure at least one Linux node allows for scheduling pods without specifying tolerations. Also, ensure that at least one Linux node that allows scheduling pods without specifying tolerations is in the *Ready* state. If your node is taking a long time to reach the *Ready* state, you can try restarting your node.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Error "Azure Dev Spaces CLI not installed properly" when running `az aks use-dev-spaces`

An update to the Azure Dev Spaces CLI changed its installation path. If you're using a version of the Azure CLI earlier than 2.0.63, you may see this error. To display your version of the Azure CLI, use `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Despite the error message when running `az aks use-dev-spaces` with a version of the Azure CLI before 2.0.63, the installation does succeed. You can continue to use `azds` without any issues.

To fix this issue, update your installation of the [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) to 2.0.63 or later. This update will resolve the error message you receive when running `az aks use-dev-spaces`. Alternatively, you can continue to use your current version of the Azure CLI and the Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Error "Unable to reach kube-apiserver"

You might see this error when Azure Dev Spaces is unable to connect to your AKS cluster's API server. 

If access to your AKS cluster API server is locked down or if you have [API server authorized IP address ranges](../aks/api-server-authorized-ip-ranges.md) enabled for your AKS cluster, you must also [create](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) or [update](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) your cluster to [allow additional ranges based on your region](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Ensure that the API server is available by running kubectl commands. If the API server is unavailable, please contact AKS support and try again when the API server is working.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Common issues when preparing your project for Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Warning "Dockerfile could not be generated due to unsupported language"
Azure Dev Spaces предоставляет встроенную поддержку C# и Node.js. When you run `azds prep` in a directory with code written in one of these languages, Azure Dev Spaces automatically creates an appropriate Dockerfile for you.

You can still use Azure Dev Spaces with code written in other languages, but you need to manually create the Dockerfile before running `azds up` for the first time.

If your application is written in a language that Azure Dev Spaces doesn't natively support, you need to provide an appropriate Dockerfile to build a container image running your code. Необходимые сведения для написания файла Docker, который отвечает вашим потребностям, см. в статье с [рекомендациями по написанию файлов Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) и справочнике по [файлам Docker](https://docs.docker.com/engine/reference/builder/).

Once you have an appropriate Dockerfile in place, you run `azds up` to run your application in Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Common issues when starting or stopping services with Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Error "Config file not found:"

When running `azds up`, you may see this error. Both `azds up` and `azds prep` must be run from the root directory of the project you want to run in your dev space.

Чтобы устранить эту проблему:
1. Измените текущий каталог на корневую папку, содержащую код службы. 
1. If you don't have a _azds.yaml_ file in the code folder, run `azds prep` to generate Docker, Kubernetes, and Azure Dev Spaces assets.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout at "Waiting for container image build..." step with AKS virtual nodes

This timeout occurs when you attempt to use Dev Spaces to run a service that is configured to run on an [AKS virtual node](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev Spaces doesn't currently support building or debugging services on virtual nodes.

При запуске `azds up` с параметром `--verbose` или включении подробного ведения журнала в Visual Studio можно просмотреть дополнительные сведения:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

The above command shows that the service's pod was assigned to *virtual-node-aci-linux*, which is a virtual node.

To fix this issue, update the Helm chart for the service to remove any *nodeSelector* or *tolerations* values that allow the service to run on a virtual node. Обычно эти значения определяются в файле `values.yaml` диаграммы.

You can still use an AKS cluster that has the virtual nodes feature enabled, if the service you wish to build or debug via Dev Spaces runs on a VM node. Running a service with Dev Spaces on a VM node is the default configuration.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Error "could not find a ready tiller pod" when launching Dev Spaces

Эта ошибка возникает, если клиент Helm больше не может обратиться к модулю pod Tiller, который выполняется в кластере.

To fix this issue, restart the agent nodes in your cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Error "release azds-\<identifier\>-\<spacename\>-\<servicename\> failed: services '\<servicename\>' already exists" or "Pull access denied for \<servicename\>, repository does not exist or may require 'docker login'"

These errors can occur if you mix running direct Helm commands (such as `helm install`, `helm upgrade`, or `helm delete`) with Dev Spaces commands (such as `azds up` and `azds down`) inside the same dev space. They occur because Dev Spaces has its own Tiller instance, which conflicts with your own Tiller instance running in the same dev space.

It's fine to use both Helm commands and Dev Spaces commands against the same AKS cluster, but each Dev Spaces-enabled namespace should use either one or the other.

For example, suppose you use a Helm command to run your entire application in a parent dev space. You can create child dev spaces off that parent, use Dev Spaces to run individual services inside the child dev spaces, and test the services together. When you're ready to check in your changes, use a Helm command to deploy the updated code to the parent dev space. Don't use `azds up` to run the updated service in the parent dev space, because it will conflict with the service initially run using Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Existing Dockerfile not used to build a container

Настройте службу Azure Dev Spaces так, чтобы она указывала на конкретный файл _Dockerfile_ в вашем проекте. Если Azure Dev Spaces не использует файл _Dockerfile_, который вы хотели использовать для сборки своих контейнеров, то можно явно указать Azure Dev Spaces, какой файл Dockerfile нужно использовать. 

To fix this issue, open the _azds.yaml_ file that Azure Dev Spaces generated in your project. Update *configurations: develop: build: dockerfile* to point to the Dockerfile you want to use. Пример.

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Error "unauthorized: authentication required" when trying to use a Docker image from a private registry

You're using a Docker image from a private registry that requires authentication.

To fix this issue, you can allow Dev Spaces to authenticate and pull images from this private registry using [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). To use imagePullSecrets, [create a Kubernetes secret](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) in the namespace where you're using the image. Then provide the secret as an imagePullSecret in `azds.yaml`.

Below is an example of a specifying imagePullSecrets in `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Setting imagePullSecrets in `azds.yaml` will override imagePullSecrets specified in the `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Error "Service cannot be started."

Эта ошибка может возникать, когда не удается запустить код службы. Чаще всего причина в пользовательском коде. To get more diagnostic information, enable more detailed logging when starting your service.

From the command line, use the `--verbose` to enable more detailed logging. You can also specify an output format using `--output`. Пример.

```cmd
azds up --verbose --output json
```

В Visual Studio:

1. Откройте меню **Сервис > Параметры** и в разделе **Проекты и решения** выберите **Сборка и запуск**.
2. Измените значение параметра **Степень подробности сообщений при сборке проекта MSBuild** на **Подробно** или **Диагностика**.

    ![Снимок экрана диалогового окна "Сервис > Параметры"](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Повторный запуск службы после повторного создания контроллера

Ошибка *Не удается запустить службу* может появиться при попытке перезапустить службу после удаления и повторного создания контроллера Azure Dev Spaces, связанного с этим кластером. В этом случае подробный вывод содержит следующий текст:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

This error occurs because removing the Dev Spaces controller doesn't remove services previously installed by that controller. Воссоздание контроллера и последующая попытка запустить службы с использованием нового контроллера завершаются неудачно, поскольку старые службы все еще находятся на месте.

Чтобы решить эту проблему, используйте команду `kubectl delete`, чтобы вручную удалить старые службы из кластера, а затем повторно запустите Dev Spaces для установки новых служб.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Error "Service cannot be started." when using multi-stage Dockerfiles

Вы получите сообщение об ошибке *Не удается запустить службу* при использовании многоэтапного Dockerfile. В этом случае подробный вывод содержит следующий текст:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

This error occurs because AKS nodes run an older version of Docker that doesn't support multi-stage builds. Чтобы избежать многоэтапных сборок, повторно создайте Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Network traffic is not forwarded to your AKS cluster when connecting your development machine

When using [Azure Dev Spaces to connect your AKS cluster to your development machine](how-to/connect.md), you may encounter an issue where network traffic is not forwarded between your development machine and your AKS cluster.

When connecting your development machine to your AKS cluster, Azure Dev Spaces forwards network traffic between your AKS cluster and your development machine by modifying your development machine's `hosts` file. Azure Dev Spaces creates an entry in the `hosts` with the address of the Kubernetes service you are replacing as a host name. This entry is used with port forwarding to direct network traffic between your development machine and the AKS cluster. If a service on your development machine conflicts with the port of the Kubernetes service you are replacing, Azure Dev Spaces cannot forward network traffic for the Kubernetes service. For example, the *Windows BranchCache* service is usually bound to *0.0.0.0:80*, which conflicts will cause a conflict for port 80 on all local IPs.

To fix this issue, you need to stop any services or processes that conflict with port of the Kubernetes service you are trying to replace. You can use tools, such as *netstat*, to inspect what services or processes on your development machine are in conflict.

For example, to stop and disable the *Windows BranchCache* service:
* Run `services.msc` from the command prompt.
* Right click on *BranchCache* and select *Properties*.
* Click *Stop*.
* Optionally, you can disable it by setting *Startup type* to *Disabled*.
* Последовательно выберите *ОК*.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Common issues using Visual Studio and Visual Studio Code with Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Error "Required tools and configurations are missing"

Эта ошибка может возникать при запуске VS Code: "[Azure Dev Spaces] Отсутствуют необходимые средства и конфигурации для сборки и отладки "[имя проекта]"".
Ошибка означает, что это средство azds.exe не находится в переменной среды PATH, как показано в VS Code.

Try launching VS Code from a command prompt where the PATH environment variable is set properly.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Ошибка "Required tools to build and debug 'projectname' are out of date " ("Необходимые инструменты для сборки и отладки "projectname" устарели")

Вы увидите эту ошибку в Visual Studio Code, если у вас есть более новая версия расширения VS Code для Azure Dev Spaces, но более старая версия Azure Dev Spaces CLI.

Try downloading and installing the latest version of the Azure Dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Error: "Failed to find debugger extension for type:coreclr"

You may see this error when running the Visual Studio Code debugger. You might not have the VS Code extension for C# installed on your development machine. The C# extension includes debugging support for .NET Core (CoreCLR).

To fix this issue, install the [VS Code extension for C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Error "Configured debug type 'coreclr' is not supported"

You may see this error when running the Visual Studio Code debugger. You might not have the VS Code extension for Azure Dev Spaces installed on your development machine.

To fix this issue, install the [VS Code extension for Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Error "Invalid 'cwd' value '/src'. The system cannot find the file specified" ("Недопустимое значение "/src" каталога "cwd". Системе не удается найти указанный файл") или "launch: program '/src/[path to project binary]' does not exist" ("запуск: программа "/src/[путь к двоичному файлу проекта]" не существует")

You may see this error when running the Visual Studio Code debugger. По умолчанию расширение VS Code использует `src` в качестве рабочего каталога для проекта в контейнере. Эта ошибка может возникать, если вы обновили свой `Dockerfile`, указав другой рабочий каталог.

To fix this issue, update the `launch.json` file under the `.vscode` subdirectory of your project folder. Измените каталог `configurations->cwd`, чтобы он указывал на тот же каталог, что и `WORKDIR`, определенный в `Dockerfile` проекта. Возможно, вам также потребуется обновить директиву `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Error "The pipe program 'azds' exited unexpectedly with code 126."

You may see this error when running the Visual Studio Code debugger.

To fix this issue, close and reopen Visual Studio Code. Restart the debugger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Error "Internal watch failed: watch ENOSPC" when attaching debugging to a Node.js application

This error occurs when the node running the pod with the Node.js application you're trying to attach to with a debugger has exceeded the *fs.inotify.max_user_watches* value. In some cases, [the default value of *fs.inotify.max_user_watches* may be too small to handle attaching a debugger directly to a pod](https://github.com/Azure/AKS/issues/772).

A temporary workaround for this issue is to increase the value of *fs.inotify.max_user_watches* on each node in the cluster and restart that node for the changes to take effect.

## <a name="other-common-issues"></a>Other common issues

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Error "azds" is not recognized as an internal or external command, operable program, or batch file

This error can happen if `azds.exe` is not installed or configured correctly.

Чтобы устранить эту проблему:

1. Check the location %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI for `azds.exe`. Если файл есть в этой папке, добавьте это расположение в переменную среды PATH.
2. If `azds.exe` isn't installed, run the following command:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Authorization error "Microsoft.DevSpaces/register/action"

Вам потребуется доступ *владельца* или *участника* в подписке Azure, чтобы управлять Azure Dev Spaces. If you're trying to manage Dev Spaces and you don't have *Owner* or *Contributor* access to the associated Azure subscription, you may see an authorization error. Пример.

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

To fix this issue, using an account with *Owner* or *Contributor* access to the Azure subscription, manually register the `Microsoft.DevSpaces` namespace:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>New pods aren't starting

The Kubernetes initializer can't apply the PodSpec for new pods due to RBAC permission changes to the *cluster-admin* role in the cluster. The new pod may also have an invalid PodSpec, for example the service account associated with the pod no longer exists. To see the pods that are in a *Pending* state due to the initializer issue, use the `kubectl get pods` command:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

This issue can impact pods in *all namespaces* in the cluster including namespaces where Azure Dev Spaces is not enabled.

To fix this issue, [update the Dev Spaces CLI to the latest version](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) and then deleting the *azds InitializerConfiguration* from the Azure Dev Spaces controller:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Once you have removed the *azds InitializerConfiguration* from the Azure Dev Spaces controller, use `kubectl delete` to remove any pods in a *Pending* state. After all pending pods have been removed, redeploy your pods.

If new pods are still stuck in a *Pending* state after a redeployment, use `kubectl delete` to remove any pods in a *Pending* state. After all pending pods have been removed, delete the controller from the cluster and reinstall it:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

After your controller is reinstalled, redeploy your pods.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Incorrect RBAC permissions for calling Dev Spaces controller and APIs

The user accessing the Azure Dev Spaces controller must have access to read the admin *kubeconfig* on the AKS cluster. For example, this permission is available in the [built-in Azure Kubernetes Service Cluster Admin Role](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). The user accessing the Azure Dev Spaces controller must also have the *Contributor* or *Owner* RBAC role for the controller. More details on updating a user's permissions for an AKS cluster are available [here](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

To update the user's RBAC role for the controller:

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Navigate to the Resource Group containing the controller, which is usually the same as your AKS cluster.
1. Enable the *Show hidden types* checkbox.
1. Click on the controller.
1. Open the *Access Control (IAM)* pane.
1. Click on the *Role Assignments* tab.
1. Click *Add* then *Add role assignment*.
    * For *Role*, select either *Contributor* or *Owner*.
    * В поле *Назначение доступа к* выберите *Пользователь, группа или субъект-служба Azure AD*.
    * For *Select*, search for the user you want to give permissions.
1. В нижней части страницы нажмите кнопку *Save*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Сбой разрешения DNS-имен для общедоступных URL-адресов, связанных со службой Dev Spaces

Вы можете настроить общедоступную конечную точку URL-адреса для службы, указав параметр `--public` в команде `azds prep` или установив флажок `Publicly Accessible` в Visual Studio. Общедоступное DNS-имя автоматически регистрируется при запуске службы в Dev Spaces. Если DNS-имя не зарегистрировано, в веб-браузере во время подключения к общедоступному URL-адресу вы увидите ошибки *Не удается отобразить страницу* или *Этот сайт недоступен*.

Чтобы устранить эту проблему:

* Check the status of all URLs associated with your Dev Spaces services:

  ```console
  azds list-uris
  ```

* If a URL is in the *Pending* state, Dev Spaces is still waiting for DNS registration to complete. Иногда для выполнения регистрации требуется несколько минут. Для каждой службы Dev Spaces также открывается туннель localhost, который можно использовать во время ожидания регистрации DNS.
* Если URL-адрес остается в состоянии *Ожидание* более 5 минут, это может указывать на проблему с внешним модулем DNS pod, который создает общедоступную конечную точку или контроллер nginx-входа, который приобретает общедоступную конечную точку. Use the following commands to delete these pods and allow AKS to automatically recreate them:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Error "upstream connect error or disconnect/reset before headers"

Вы можете увидеть эту ошибку при попытке получить доступ к службе. Например, при переходе по URL-адресу службы в браузере. This error means the container port isn't available. This can for the follow reasons:

* Процесс сборки или развертывания контейнера еще не завершен. Это может случиться, если вы запускаете `azds up` или отладчик, а затем пытаетесь получить доступ к контейнеру до его успешного развертывания.
* Конфигурация порта не согласуется в _Dockerfile_, диаграмме Helm и серверном коде, который открывает порт.

Чтобы устранить эту проблему:

1. Если процесс сборки или развертывания еще не завершен, вы можете подождать 2–3 секунды и повторить попытку доступа к службе. 
1. Проверьте конфигурацию порта. Указанные номера портов должны **совпадать** во всех следующих ресурсах:
    * **Dockerfile:** задается инструкцией `EXPOSE`.
    * **[Диаграмма Helm](https://docs.helm.sh):** задается значениями `externalPort` и `internalPort` для службы (часто находится в файле `values.yml`).
    * Все порты, открытые в коде приложения, например в Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>The type or namespace name "MyLibrary" couldn't be found

A library project you're using can't be found. With Dev Spaces, the build context is at the project/service level by default.  

Чтобы устранить эту проблему:

1. Modify the `azds.yaml` file to set the build context to the solution level.
2. Modify the `Dockerfile` and `Dockerfile.develop` files to refer to the project files, for example `.csproj`, correctly relative to the new build context.
3. Add a `.dockerignore` in the same directory as the `.sln` file.
4. Update the `.dockerignore` with additional entries as needed.

You can find an example at [here](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horizontal pod autoscaling not working in a dev space

When you run a service in a dev space, that service's pod is [injected with additional containers for instrumentation](how-dev-spaces-works.md#prepare-your-aks-cluster) and all the containers in a pod need to have resource limits and requests set for Horizontal Pod Autoscaling.

To fix this issue, apply a resource request and limit to the injected Dev Spaces containers. Resource requests and limits can be applied for the injected container (devspaces-proxy) by adding the `azds.io/proxy-resources` annotation to your pod spec. The value should be set to a JSON object representing the resources section of the container spec for the proxy.

Below is an example of a proxy-resources annotation that is to be applied to your pod spec.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Enable Azure Dev Spaces on an existing namespace with running pods

You may have an existing AKS cluster and namespace with running pods where you want to enable Azure Dev Spaces.

To enable Azure Dev Spaces on an existing namespace in an AKS cluster, run `use-dev-spaces` and use `kubectl` to restart all pods in that namespace.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

After your pods have restarted, you can begin using your existing namespace with Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Enable Azure Dev Spaces on AKS cluster with restricted egress traffic for cluster nodes

To enable Azure Dev Spaces on an AKS cluster for which the egress traffic from cluster nodes is restricted, you will have to allow following FQDNs:

| FQDN                                    | Port      | Использование      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | To pull linux alpine and other Azure Dev Spaces images |
| gcr.io | HTTP:443 | To pull helm/tiller images|
| storage.googleapis.com | HTTP:443 | To pull helm/tiller images|