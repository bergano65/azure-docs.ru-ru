---
title: Управление кластером Службы Azure Kubernetes с помощью веб-панели мониторинга
description: Узнайте, как использовать панель мониторинга пользовательского веб-интерфейса Kubernetes для управления кластером Службы Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: f150103c8e9534bfd1bb93d20e3d65d715767184
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996965"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Подключение веб-панели мониторинга Kubernetes в Службе Azure Kubernetes (AKS)

Служба Kubernetes включает в себя веб-панель мониторинга, которая может использоваться для базовых операций управления. Эта панель мониторинга позволяет просматривать базовое состояние работоспособности и метрики для приложений, создавать и развертывать службы, а также изменять существующие приложения. В этом документе рассматривается запуск панели мониторинга Kubernetes с помощью Azure CLI, а также некоторые основные операции с ней.

Дополнительные сведения о панели мониторинга Kubernetes см. в разделе [панель мониторинга пользовательского веб-интерфейса Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Перед началом работы

В действиях, описанных в этом документе, предполагается, что создан кластер AKS и с ним установлено подключение `kubectl`. Если необходимо создать кластер AKS, см. раздел [Краткое руководство по AKS][aks-quickstart].

Кроме того, нужно установить и настроить Azure CLI 2.0.46 или более поздней версии. Чтобы узнать версию, выполните команду  `az --version` . Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Запуск панели мониторинга Kubernetes

Чтобы запустить панель мониторинга Kubernetes, используйте команду [AZ AKS Browse][az-aks-browse] . В следующем примере создается панель мониторинга для кластера с именем *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Эта команда создает прокси-сервер между системой разработки и API Kubernetes и открывает панель мониторинга Kubernetes в веб-браузере. Если веб-браузер не открывается на панели мониторинга Kubernetes, скопируйте и вставьте URL-адрес, указанный в Azure CLI, обычно `http://127.0.0.1:8001`.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Если кластер AKS использует RBAC, необходимо создать *ClusterRoleBinding*(связи кластерных ролей) прежде чем правильно подключиться к панели мониторинга. По умолчанию панель мониторинга Kubernetes развертывается с минимальными правами доступа на чтение и отображает сообщения об ошибках доступа RBAC. Сейчас панель мониторинга Kubernetes не поддерживает пользовательские учетные данные для определения уровня доступа. Вместо этого она использует роли, предоставленные учетной записи службы. Администратор кластера может предоставить дополнительный доступ к учетной записи службы *kubernetes-dashboard*, однако это может послужить вектором для эскалации привилегий. Вы также можете интегрировать аутентификацию Azure Active Directory, чтобы предоставить более детальный уровень доступа.
> 
> Чтобы создать привязку, используйте команду [kubectl Create клустерролебиндинг][kubectl-create-clusterrolebinding] . В следующем примере показано, как создать пример привязки, однако этот пример привязки не применяет какие-либо дополнительные компоненты проверки подлинности и может привести к небезопасному использованию. Панель мониторинга Kubernetes доступна для любого пользователя, имеющего доступ к URL-адресу. Не предоставляйте публичный доступ к панели мониторинга Kubernetes.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Дополнительные сведения об использовании различных методов проверки подлинности см. на вики-сайте панели мониторинга Kubernetes в разделе [элементы управления доступом][dashboard-authentication].

![Страница "Обзор" веб-панели мониторинга Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Создание приложения

Давайте создадим приложение, чтобы увидеть, как панель мониторинга Kubernetes позволяет упростить задачи управления. Приложение можно создать на панели мониторинга Kubernetes, предоставив текстовые входные данные (файл YAML), или через мастер с графическим интерфейсом.

Чтобы создать приложение, выполните указанные ниже действия:

1. Нажмите кнопку **Создать** в правом верхнем углу окна.
1. Чтобы использовать мастер с графическим интерфейсом, выберите **Create an app** (Создать приложение).
1. Укажите имя для развертывания, например *nginx*.
1. Введите имя, которое будет использоваться для образа контейнера, например *nginx:1.15.5*.
1. Чтобы настроить порт 80 для веб-трафика, создайте службу Kubernetes. В разделе **Служба** выберите **External** (Внешняя), а затем введите **80** для порта и целевого порта.
1. Когда все будет готово, нажмите кнопку **Развернуть**, чтобы создать приложение.

![Развертывание приложения на веб-панели мониторинга Kubernetes](./media/kubernetes-dashboard/create-app.png)

Назначение общедоступного внешнего IP-адреса службе Kubernetes занимает пару минут. Слева в разделе **Discovery and Load Balancing** (Обнаружение и балансировка нагрузки) выберите **Службы**. Вы увидите службу приложения, а также *внешние конечные точки*, как показано в следующем примере:

![Просмотр списка служб и конечных точек](./media/kubernetes-dashboard/view-services.png)

Выберите адрес конечной точки, чтобы открыть окно веб-браузера на странице NGINX по умолчанию:

![Просмотр страницы NGINX по умолчанию развернутого приложения](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Просмотр сведений о pod

На панели мониторинга Kubernetes могут содержаться основные метрики мониторинга и сведения об устранении неполадок, в частности журналы.

Чтобы просмотреть дополнительные сведения о группах pod приложения, выберите **Pods** (Группы pod) в меню слева. Отобразится список доступных pod. Выберите свой pod *nginx*, чтобы просмотреть такие сведения, как потребление ресурсов:

![Просмотр сведений о pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Изменение приложения

Помимо создания и просмотра приложений панель мониторинга Kubernetes можно использовать для изменения и обновления развертываний приложений. Давайте увеличим число реплик NGINX, чтобы обеспечить дополнительную избыточность для приложения.

Чтобы изменить развертывание:

1. Щелкните **Развертывания** в меню слева, а затем выберите свое развертывание *nginx*.
1. Выберите **Изменить** на верхней правой панели навигации.
1. Найдите значение `spec.replica` в строке 20. Чтобы увеличить количество реплик для приложения, измените это значение с *1* на *3*.
1. Выберите **Обновить** по окончании.

![Изменение развертывания для обновления количества реплик](./media/kubernetes-dashboard/edit-deployment.png)

Создание новых pod в наборе реплик занимает несколько секунд. В меню слева выберите **Replica Sets** (Наборы реплик), а затем выберите набор реплик *nginx*. Список pod теперь отражает обновленное число реплик, как показано в следующем примере выходных данных:

![Просмотр сведений о наборе реплик](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о панели мониторинга Kubernetes см. на панели [мониторинга веб-интерфейса Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
