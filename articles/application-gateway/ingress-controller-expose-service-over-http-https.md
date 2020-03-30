---
title: Разоблачить службу AKS по HTTP или HTTPS с помощью шлюза приложений
description: В этой статье содержится информация о том, как разоблачить службу AKS по http или HTTPS с помощью шлюза приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795572"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Разоблачить службу AKS по HTTP или HTTPS с помощью шлюза приложений 

Эти учебники помогают проиллюстрировать использование [ресурсов Kubernetes Ingress для](https://kubernetes.io/docs/concepts/services-networking/ingress/) предоставления примера службы Kubernetes через [шлюз приложений Azure](https://azure.microsoft.com/services/application-gateway/) через HTTP или HTTPS.

## <a name="prerequisites"></a>Предварительные требования

- Установленная `ingress-azure` диаграмма руля.
  - [**Развертывание Greenfield**](ingress-controller-install-new.md): Если вы начинаете с нуля, обратитесь к этим инструкциям по установке, в которых излагаются шаги по развертыванию кластера AKS с помощью шлюза приложений и установки контроллера входа шлюза приложения в кластер AKS.
  - [**Развертывание Brownfield:**](ingress-controller-install-existing.md)Если у вас есть существующий кластер AKS и шлюз приложений, обратитесь к этим инструкциям по установке контроллера входа шлюза приложения в кластер AKS.
- Если вы хотите использовать HTTPS в этом приложении, вам понадобится сертификат x509 и его личный ключ.

## <a name="deploy-guestbook-application"></a>Развертывание `guestbook` приложения

Приложение гостевой книги является каноническим приложением Kubernetes, которое составляет фронтэнд web-пользователей, бэкэнд и базу данных Redis. По умолчанию, `guestbook` предоставляет свое приложение `frontend` через `80`службу с именем в порту . Без ресурса Kubernetes Ingress Service недоступна из-за пределов кластера AKS. Мы будем использовать приложение и настроить Ingress ресурсов для доступа к приложению через HTTP и HTTPS.

Следуйте инструкциям ниже, чтобы развернуть приложение гостевой книги.

1. `guestbook-all-in-one.yaml` Скачать [здесь](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Развертывание `guestbook-all-in-one.yaml` в кластере AKS путем запуска

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Теперь `guestbook` приложение развернуто.

## <a name="expose-services-over-http"></a>Экспонировать услуги по HTTP

Для того, чтобы разоблачить приложение гостевой книги, мы будем использовать следующий ресурс входа:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Этот вход будет `frontend` подвергать службу `guestbook-all-in-one` развертывания в качестве бэкэнда по умолчанию шлюза приложения.

Сохранить выше вход ресурса, как `ing-guestbook.yaml`.

1. Развертывание `ing-guestbook.yaml` путем запуска:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Проверьте журнал контроллера входа на состояние развертывания.

Теперь `guestbook` приложение должно быть доступно. Проверить это можно, посетив публичный адрес шлюза приложения.

## <a name="expose-services-over-https"></a>Экспонировать услуги по HTTPS

### <a name="without-specified-hostname"></a>Без указанного имени хоста

Без указания имени хоста служба гостевой книги будет доступна на всех именах хоста, указывающих на шлюз приложения.

1. Перед развертыванием входа необходимо создать секрет kubernetes для размещения сертификата и частного ключа. Вы можете создать секрет kubernetes, запустив

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Определите следующий вход. В входе укажите название секрета `secretName` в разделе.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Замените `<guestbook-secret-name>` в вышеуказанном Ресурсе Ingress с именем вашего секрета. Храните вышеупомянутый Ресурс Ingress в имени `ing-guestbook-tls.yaml`файла.

1. Развертывание ing-guestbook-tls.yaml путем запуска

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Проверьте журнал контроллера входа на состояние развертывания.

Теперь `guestbook` приложение будет доступно как на HTTP, так и на HTTPS.

### <a name="with-specified-hostname"></a>С указанным именем хоста

Вы также можете указать имя хоста на входе для того, чтобы мультиплекс TLS конфигураций и услуг.
При указании имени хоста служба гостевой книги будет доступна только на указанном хосте.

1. Определите следующий вход.
    В входе укажите название секрета `secretName` в разделе и замените `hosts` хост-имя в разделе соответственно.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Развертывание `ing-guestbook-tls-sni.yaml` путем выполнения

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Проверьте журнал контроллера входа на состояние развертывания.

Теперь `guestbook` приложение будет доступно как на HTTP, так`<guestbook.contoso.com>` и на HTTPS только на указанном хосте (в данном примере).

## <a name="integrate-with-other-services"></a>Интеграция с другими службами

Следующий вход позволит вам добавить дополнительные пути в этот вход и перенаправить эти пути на другие услуги:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
