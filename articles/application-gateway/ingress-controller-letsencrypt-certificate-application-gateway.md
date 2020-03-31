---
title: Используйте LetsEncrypt.org сертификаты с шлюзом приложений
description: В этой статье содержится информация о том, как получить сертификат от LetsEncrypt.org и использовать его на шлюзе приложения для кластеров AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957987"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Использование сертификатов с LetsEncrypt.org на шлюзе приложения для кластеров AKS

Этот раздел настраивает AKS на использование [LetsEncrypt.org](https://letsencrypt.org/) и автоматически получить сертификат TLS/SSL для вашего домена. Сертификат будет установлен на шлюзе приложения, который будет выполнять sSL/TLS прекращения для вашего кластера AKS. Описанная здесь установка использует надстройку Kubernetes, которая автоматизирует создание и управление сертификатами. [cert-manager](https://github.com/jetstack/cert-manager)

Выполните ниже шаги, чтобы установить [сертификат-менеджер](https://docs.cert-manager.io) в существующем кластере AKS.

1. Хелм Диаграмма

    Выполнить следующий скрипт для установки диаграммы `cert-manager` руля. В результате:

    - создать новое `cert-manager` пространство имен на вашем AKS
    - создать следующие CRD: Сертификат, Вызов, КластерЭмитент, Эмитент, Заказ
    - установить диаграмму сертификат-менеджера (от [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

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

2. Ресурс кластера

    Создайте `ClusterIssuer` ресурс. Он обязан `cert-manager` представлять сертификационный `Lets Encrypt` орган, в котором будут получены подписанные сертификаты.

    Используя ресурс, не связанный `ClusterIssuer` с именем, сертификат-менеджер выдает сертификаты, которые можно использовать из нескольких областей имен. `Let’s Encrypt`использует протокол ACME для проверки того, что вы контролируете данное доменное имя, и выдаете вам сертификат. Более подробная информация `ClusterIssuer` о настройке свойств [здесь](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer`будет `cert-manager` поручено выдавать сертификаты с использованием `Lets Encrypt` промежуточной среды, используемой для тестирования (корневой сертификат, не присутствующий в магазинах доверия браузера/клиента).

    Тип вызова по умолчанию в `http01`YAML ниже . Другие проблемы задокументированы на [letsencrypt.org - Типы вызовов](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Обновление `<YOUR.EMAIL@ADDRESS>` в YAML ниже

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

    Создайте ресурс Ingress `guestbook` для разоблачения приложения с помощью шлюза приложения с сертификатом Lets Encrypt.

    Убедитесь, что у gateway приложения есть общедоступная конфигурация `azure.com` Frontend IP `Azure DNS Zone` с именем DNS (либо с использованием домена по умолчанию, либо предоставление услуг, и назначить свой собственный пользовательский домен).
    Обратите внимание на `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`аннотацию, которая говорит сертификат-менеджер для обработки помеченных Ingress ресурса.

    > [!IMPORTANT] 
    > Обновление `<PLACEHOLDERS.COM>` в YAML ниже с вашим собственным доменом (или приложение шлюз один, например, "kh-aks-ingress.westeurope.cloudapp.azure.com")

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

    Через несколько секунд вы `guestbook` можете получить доступ к службе через URL-адрес application Gateway HTTPS, используя автоматически выданный **промежуточный** `Lets Encrypt` сертификат.
    Ваш браузер может предупредить вас о недействительным сертификате органа. Постановочный сертификат `CN=Fake LE Intermediate X1`выдается . Это свидетельствует о том, что система работала в штатном порядке, и вы готовы к сертификату на производство.

4. Производственный сертификат

    После успешной настройки постановочного сертификата можно переключиться на производственный сервер ACME:
    1. Замените постановочную аннотацию на ресурсе Ingress:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Удалите `ClusterIssuer` существующую постановку, созданную на предыдущем этапе, и создайте новую, заменив сервер ACME из ClusterIssuer YAML выше`https://acme-v02.api.letsencrypt.org/directory`

5. Срок действия сертификата и продление

    До `Lets Encrypt` истечения `cert-manager` срока действия сертификата автоматически обновляется сертификат в секретном магазине Kubernetes. В этот момент контроллер входа в приложение Gateway будет применять обновленный секрет, на который ссылается в ресурсах, которые он использует для настройки шлюза приложения.
