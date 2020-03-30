---
title: Безопасные веб-сервисы с помощью TLS
titleSuffix: Azure Machine Learning
description: Узнайте, как включить HTTPS, чтобы обеспечить безопасность веб-службы, развернутой в Azure Machine Learning. Azure Machine Learning использует версию TLS 1.2 для защиты моделей, развернутых в качестве веб-служб.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287345"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Используйте TLS для обеспечения безопасности веб-службы через Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье показано, как обеспечить безопасность веб-службы, развернутой с помощью Машинного обучения Azure.

Вы используете [HTTPS](https://en.wikipedia.org/wiki/HTTPS) для ограничения доступа к веб-службам и защиты данных, которые отправляют клиенты. HTTPS помогает обеспечить безопасность связи между клиентом и веб-службой, шифруя сообщения между ними. Шифрование использует [безопасность транспортного уровня (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS иногда все еще называют *Безопасный слой розеток* (SSL), который был предшественником TLS.

> [!TIP]
> В SDK Azure Machine Learning используется термин «SSL» для свойств, связанных с безопасной связью. Это не означает, что ваш веб-сервис не использует *TLS.* SSL является лишь более общепризнанным термином.
>
> В частности, веб-сервисы, развернутые через Azure Machine Learning, поддерживают только версию TLS 1.2.

TLS и SSL полагаются на *цифровые сертификаты,* которые помогают с шифрованием и проверкой личности. Для получения дополнительной информации о том, [Public key infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure)как работают цифровые сертификаты, см.

> [!WARNING]
> Если вы не используете HTTPS для веб-службы, данные, отправленные в службу и из нее, могут быть видны другим пользователям в Интернете.
>
> HTTPS также позволяет клиенту проверить подлинность сервера, к которого он подключается. Эта функция защищает клиентов от атак [«человек в середине».](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Это общий процесс для обеспечения веб-службы:

1. Получите имя домена.

2. Получить цифровой сертификат.

3. Развертывание или обновление веб-службы с включенным TLS.

4. Обновите имя DNS, чтобы оно указывало на веб-службу.

> [!IMPORTANT]
> При развертывании в службе Azure Kubernetes Service (AKS) вы можете приобрести собственный сертификат или использовать сертификат, предоставленный корпорацией Майкрософт. Если вы используете сертификат от корпорации Майкрософт, вам не нужно получать доменное имя или сертификат TLS/SSL. Для получения дополнительной информации ознакомьтесь [с разделом Включить TLS и развернуть](#enable) раздел этой статьи.

Есть небольшие различия, когда вы обеспечиваете s по всем [целям развертывания.](how-to-deploy-and-where.md)

## <a name="get-a-domain-name"></a>Получение доменного имени

Если у вас еще нет доменного имени, купите его у *регистратора доменных имен.* Процесс и цена различаются между регистраторами. Регистратор предоставляет инструменты для управления доменным именем. Эти инструменты используются для картирования полностью квалифицированного доменного\.имени (F-DN) (например, www contoso.com) на IP-адрес, на котором размещен ваш веб-сервис.

## <a name="get-a-tlsssl-certificate"></a>Получить сертификат TLS/SSL

Есть много способов получить сертификат TLS/SSL (цифровой сертификат). Наиболее распространенным является приобретение одного из *сертификатов органа* (CA). Независимо от того, где вы получаете сертификат, вам нужны следующие файлы:

* **Сертификат**. Сертификат должен содержать полную цепочку сертификатов, и он должен быть "PEM-кодированный".
* **Ключ**. Ключ также должен быть закодирован PEM.

При запросе сертификата необходимо предоставить адрес, который вы планируете использовать для веб-службы\.(например, www.contoso.com). Адрес, вкоторыйемый в сертификат, и адрес, который используют клиенты, сравниваются для проверки личности веб-сервиса. Если эти адреса не совпадают, клиент получает сообщение об ошибке.

> [!TIP]
> Если сертификат не может предоставить сертификат и ключ в виде закодированных файлов PEM, можно использовать утилиту, такую как [OpenSSL,](https://www.openssl.org/) чтобы изменить формат.

> [!WARNING]
> Используйте *самоподписанные* сертификаты только для разработки. Не используйте их в производственных средах. Самозаверяющие сертификаты могут вызвать проблемы в клиентских приложениях. Для получения дополнительной информации ознакомьтесь с документацией для сетевых библиотек, которые использует клиентское приложение.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>Включить TLS и развернуть

Для развертывания (или передислокации) службы с включенным TLS установите *параметр ssl_enabled* "True", где бы это ни было применимо. Установите *ssl_certificate* параметр к значению файла *сертификата.* Установите *ssl_key* значение *файла ключа.*

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Развертывание на AKS и поле-программируемых ворот массива (FPGA)

  > [!NOTE]
  > Информация в этом разделе также применяется при развертывании безопасного веб-сервиса для конструктора. Если вы не знакомы с использованием Python SDK, [см.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

При развертывании в AKS можно создать новый кластер AKS или прикрепить существующий кластер. Для получения дополнительной информации о создании или присоединении кластера см. [Развертывание модели в кластере службы Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md)
  
-  При создании нового кластера используется **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- При подключении существующего кластера используется **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Оба возвращают объект конфигурации с **enable_ssl** методом.

Метод **enable_ssl** может использовать сертификат, предоставленный корпорацией Майкрософт, или приобретенный сертификат.

  * При использовании сертификата от корпорации Майкрософт необходимо использовать *leaf_domain_label* параметр. Этот параметр генерирует имя DNS для службы. Например, значение "contoso" создает доменное имя "contoso\<шесть случайных символов>. \<azureregion>.cloudapp.azure.com", \<где лазуривный> является областью, содержащей службу. Дополнительно можно использовать параметр *overwrite_existing_domain* для перезаписи существующего *leaf_domain_label.*

    Для развертывания (или передислокации) службы с включенным TLS установите *параметр ssl_enabled* "True", где бы это ни было применимо. Установите *ssl_certificate* параметр к значению файла *сертификата.* Установите *ssl_key* значение *файла ключа.*

    > [!IMPORTANT]
    > При использовании сертификата от корпорации Майкрософт вам не нужно приобретать собственный сертификат или доменное имя.

    В следующем примере показано, как создать конфигурацию, позволяющую получить сертификат TLS/SSL от корпорации Майкрософт:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * При *использовании сертификата, который вы приобрели,* вы используете *ssl_cert_pem_file,* *ssl_key_pem_file*и *ssl_cname* параметров. Ниже приводится следующий пример, как использовать файлы *.pem* для создания конфигурации, используюейтой сертификатом TLS/SSL, который вы приобрели:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Для получения дополнительной информации о *enable_ssl*, см. [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) и [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Развертывание на экземплярах контейнеров Azure

При развертывании в azure Container Instances вы предоставляете значения для параметров, связанных с TLS, как показано на следующем фрагменте кода:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Для получения дополнительной информации см. [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Обновление DNS

Теперь необходимо обновить DNS, чтобы оно указывало на веб-службу.

+ **Для контейнерных инстанций:**

  Используйте инструменты от регистратора доменных имен для обновления записи DNS для вашего доменного имени. Запись должна указывать на IP-адрес службы.

  Там может быть задержка минут или часов, прежде чем клиенты могут решить доменное имя, в зависимости от регистратора и "время жить" (TTL), который настроен для доменного имени.

+ **Для АКС:**

  > [!WARNING]
  > Если вы использовали *leaf_domain_label* для создания службы с помощью сертификата от Microsoft, не обновляйте вручную значение DNS для кластера. Значение должно быть установлено автоматически.

  Обновление DNS публичного IP-адреса кластера AKS на вкладке **Конфигурация** под **настройками** в левом стеле. (См. следующее изображение.) Публичный IP-адрес — это тип ресурсов, созданный в группе ресурсов, содержащей узлы агента AKS и другие сетевые ресурсы.

  [![Машинное обучение Azure: обеспечение безопасности веб-сервисов с помощью TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Обновление сертификата TLS/SSL

Срок действия сертификатов TLS/SSL истекает и должен быть продлен. Обычно это происходит каждый год. Используйте информацию в следующих разделах для обновления и обновления сертификата для моделей, развернутых в службе Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Обновление созданного майкрософт сертификата

Если сертификат был первоначально создан корпорацией Майкрософт (при использовании *leaf_domain_label* для создания службы), используйте один из следующих примеров для обновления сертификата:

**Использование SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Использование интерфейса командной строки**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Для получения дополнительной информации см.

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [АксUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Обновление пользовательского сертификата

Если сертификат был первоначально создан органом сертификата, используйте следующие действия:

1. Для продления сертификата используйте документацию, предоставленную органом по сертификации. Этот процесс создает новые файлы сертификатов.

1. Используйте sDK или CLI для обновления службы с новым сертификатом:

    **Использование SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Использование интерфейса командной строки**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Для получения дополнительной информации см.

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [АксUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>Отключить TLS

Чтобы отключить TLS для модели, развернутой в службе Azure `SslConfiguration` `status="Disabled"`Kubernetes, создайте с помощью, а затем выполните обновление:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Дальнейшие действия
Вы узнаете, как выполнять следующие задачи:
+ [Использование модели Машинного обучения, развернутой в качестве веб-службы](how-to-consume-web-service.md)
+ [Безопасно еранжируйте эксперименты и выводы внутри виртуальной сети Azure](how-to-enable-virtual-network.md)
