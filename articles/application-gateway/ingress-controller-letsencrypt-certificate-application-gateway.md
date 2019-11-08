---
title: Использование сертификатов LetsEncrypt.org с шлюзом приложений
description: В этой статье содержатся сведения о том, как получить сертификат от LetsEncrypt.org и использовать его в шлюзе приложений для кластеров AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2e91a888d0dc98a4f94b956e15336d75291f733e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795919"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Использование сертификатов с LetsEncrypt.org в шлюзе приложений для кластеров AKS

В этом разделе вы настраиваете AKS для использования [LetsEncrypt.org](https://letsencrypt.org/) и автоматически получаете сертификат TLS/SSL для вашего домена. Сертификат будет установлен в шлюзе приложений, который будет выполнять завершение SSL/TLS для кластера AKS. Описанная здесь программа установки использует надстройку [CERT-Manager](https://github.com/jetstack/cert-manager) Kubernetes, которая автоматизирует создание и управление сертификатами.

Выполните следующие действия, чтобы установить [Диспетчер сертификатов](https://docs.cert-manager.io) в имеющемся кластере AKS.

1. Диаграмма Helm

    Выполните следующий скрипт, чтобы установить `cert-manager` диаграмму Helm. В результате:

    - Создание нового пространства имен `cert-manager` на AKS
    - Создайте следующий КРДС: сертификат, запрос, Клустериссуер, издатель, заказ
    - Установка диаграммы диспетчера сертификатов (из [docs.CERT-Manager.IO)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Ресурс Клустериссуер

    Создайте ресурс `ClusterIssuer`. Он необходим `cert-manager` для представления центра сертификации `Lets Encrypt`, в котором будут получены подписанные сертификаты.

    При использовании ресурса `ClusterIssuer`, не относящегося к пространству имен, диспетчер сертификатов выдает сертификаты, которые можно использовать из нескольких пространств имен. `Let’s Encrypt` использует протокол ACME, чтобы убедиться, что вы управляете заданным доменным именем и выдаете сертификат. Дополнительные сведения о настройке свойств `ClusterIssuer` [.](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer` предложит `cert-manager` выдавать сертификаты с помощью `Lets Encrypt` промежуточной среды, используемой для тестирования (корневой сертификат отсутствует в магазинах доверия браузера или клиента).

    Тип запроса по умолчанию в YAML ниже `http01`. Другие проблемы задокументированы в [типах letsencrypt.org-Challenge](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Обновите `<YOUR.EMAIL@ADDRESS>` в YAML ниже

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Развертывание приложения

    Создайте ресурс входящего трафика, чтобы предоставить `guestbook`ное приложение с помощью шлюза приложений с сертификатом.

    Убедитесь, что шлюз приложений имеет общедоступную IP-конфигурацию с DNS-именем (либо с помощью домена по умолчанию `azure.com`, либо подготавливает `Azure DNS Zone`ную службу и назначьте собственный пользовательский домен).
    Обратите внимание на `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`заметок, которая сообщает менеджеру CERT о необходимости обрабатывать ресурс с тегами.

    > [!IMPORTANT] 
    > Обновите `<PLACEHOLDERS.COM>` в YAML ниже, используя собственный домен (или шлюз приложений, например "kh-aks-ingress.westeurope.cloudapp.azure.com").

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Через несколько секунд вы сможете получить доступ к службе `guestbook` через URL-адрес HTTPS шлюза приложений, используя автоматически выданный сертификат **промежуточного хранения** `Lets Encrypt`.
    Браузер может предупредить о недопустимом центре сертификации. Промежуточный сертификат выдается `CN=Fake LE Intermediate X1`. Это означает, что система работала должным образом и вы готовы к рабочему сертификату.

4. Рабочий сертификат после успешной установки промежуточного сертификата можно переключиться на рабочий сервер ACME:
    1. Замените промежуточную аннотацию для ресурса входящих данных на: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Удалите существующие промежуточные `ClusterIssuer`, созданные на предыдущем шаге, и создайте новую, заменив сервер ACME с Клустериссуер YAML выше на `https://acme-v02.api.letsencrypt.org/directory`

5. Срок действия сертификата и продление до истечения срока действия сертификата `Lets Encrypt` `cert-manager` автоматически обновит сертификат в хранилище секретов Kubernetes. На этом этапе контроллер входящего трафика шлюза приложений применит обновленный секрет, указанный в входящих ресурсах, который используется для настройки шлюза приложений.
