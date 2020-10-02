---
title: Защита веб-служб с помощью TLS
titleSuffix: Azure Machine Learning
description: Узнайте, как включить протокол HTTPS для защиты веб-службы, развернутой с помощью Машинное обучение Azure. Машинное обучение Azure использует TLS версии 1,2 для защиты моделей, развернутых как веб-службы.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5d0a86a966cacfdeac291c66fa245a613b383a85
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629529"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Использование TLS для защиты веб-службы с помощью Машинного обучения Azure.


В этой статье показано, как защитить веб-службу, развернутую с помощью Машинное обучение Azure.

[Протокол HTTPS](https://en.wikipedia.org/wiki/HTTPS) используется для ограничения доступа к веб-службам и защиты данных, отправляемых клиентами. Протокол HTTPS обеспечивает безопасность обмена данными между клиентом и веб-службой путем шифрования обмена данными между ними. Шифрование использует [протокол TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). Протокол TLS иногда по-прежнему называется *SSL* (SSL), который был предшественником TLS.

> [!TIP]
> В пакете SDK для Машинное обучение Azure используется термин "SSL" для свойств, связанных с безопасной связью. Это не означает, что веб-служба не использует *TLS*. SSL — это просто более широко распознанный термин.
>
> В частности, веб-службы, развернутые с помощью Машинное обучение Azure, поддерживают только TLS версии 1,1 для ACI и TLS версии 1,2 для AKS.

Протоколы TLS и SSL используют *цифровые сертификаты*, которые помогают в шифровании и проверке личности. Дополнительные сведения о работе цифровых сертификатов см. в статье об [инфраструктуре открытых ключей](https://en.wikipedia.org/wiki/Public_key_infrastructure)в разделе Википедии.

> [!WARNING]
> Если для веб-службы не используется протокол HTTPS, данные, отправляемые в службу и из нее, могут быть видны другим пользователям в Интернете.
>
> HTTPS также позволяет клиенту проверять подлинность сервера, к которому он подключается. Эта функция защищает клиентов от атак типа ["злоумышленник в середине"](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Это общий процесс защиты веб-службы:

1. Получите имя домена.

2. Получение цифрового сертификата.

3. Развертывание или обновление веб-службы с включенным TLS.

4. Обновите имя DNS, чтобы оно указывало на веб-службу.

> [!IMPORTANT]
> При развертывании в службе Azure Kubernetes Service (AKS) можно приобрести собственный сертификат или использовать сертификат, предоставляемый корпорацией Майкрософт. Если вы используете сертификат от корпорации Майкрософт, вам не нужно получать имя домена или TLS/SSL-сертификат. Дополнительные сведения см. в разделе [Включение TLS и развертывание](#enable) этой статьи.

Существуют небольшие отличия при защите между [целями развертывания](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Получение доменного имени

Если вы еще не владеете доменным именем, приобретите его у *регистратора доменных имен*. Процесс и цена отличаются между регистраторами. Регистратор предоставляет средства для управления доменным именем. Эти средства используются для преобразования полного доменного имени (например, www \. contoso.com) в IP-адрес, на котором размещена веб-служба.

## <a name="get-a-tlsssl-certificate"></a>Получение сертификата TLS/SSL

Существует много способов получить сертификат TLS/SSL (цифровой сертификат). Наиболее распространенным является приобретение одного из *центра сертификации* (ЦС). Независимо от того, где вы получаете сертификат, вам потребуются следующие файлы:

* **Сертификат**. Сертификат должен содержать всю цепочку сертификатов и должен иметь значение "PEM-Encoded".
* **Ключ**. Ключ также должен быть закодирован в виде PEM.

При запросе сертификата необходимо указать полное доменное имя адреса, который планируется использовать для веб-службы (например, www \. contoso.com). Адрес, который был отмечен в сертификате, и адрес, используемый клиентами, сравнивается с целью проверки удостоверения веб-службы. Если эти адреса не совпадают, клиент получает сообщение об ошибке.

> [!TIP]
> Если центр сертификации не может предоставить сертификат и ключ в виде файлов в кодировке PEM, для изменения формата можно использовать служебную программу, например [OpenSSL](https://www.openssl.org/) .

> [!WARNING]
> Используйте *самозаверяющие* сертификаты только для разработки. Не используйте их в рабочих средах. Самозаверяющие сертификаты могут вызвать проблемы в клиентских приложениях. Дополнительные сведения см. в документации по сетевым библиотекам, используемым клиентским приложением.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Включение TLS и развертывание

Чтобы развернуть (или повторно развернуть) службу с включенным протоколом TLS, задайте для параметра *Ssl_enabled* значение "true" везде, где это применимо. Присвойте параметру *ssl_certificate* значение файла *сертификата* . Задайте *ssl_key* в качестве значения файла *ключа* .

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Развертывание в AKS и программируемом массиве Gates (FPGA)

  > [!NOTE]
  > Сведения в этом разделе также применяются при развертывании безопасной веб-службы для конструктора. Если вы не знакомы с использованием пакета SDK для Python, см. статью [что такое пакет SDK для машинное обучение Azure для Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

При развертывании в AKS можно создать новый кластер AKS или подключить существующий. Дополнительные сведения о создании или присоединении кластера см. в статье [развертывание модели в кластере службы Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).
  
-  При создании нового кластера используется **[акскомпуте. provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- При присоединении существующего кластера используется **[акскомпуте. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Оба возвращают объект конфигурации, имеющий метод **enable_ssl** .

Метод **enable_ssl** может использовать сертификат, предоставляемый корпорацией Майкрософт или сертификатом, который вы приобрели.

  * При использовании сертификата от корпорации Майкрософт необходимо использовать параметр *leaf_domain_label* . Этот параметр создает DNS-имя для службы. Например, значение Contoso создает доменное имя Contoso \<six-random-characters> . \<azureregion> cloudapp.azure.com ", где \<azureregion> — регион, содержащий службу. При необходимости можно использовать параметр *overwrite_existing_domain* для перезаписи существующего *leaf_domain_label*.

    Чтобы развернуть (или повторно развернуть) службу с включенным протоколом TLS, задайте для параметра *Ssl_enabled* значение "true" везде, где это применимо. Присвойте параметру *ssl_certificate* значение файла *сертификата* . Задайте *ssl_key* в качестве значения файла *ключа* .

    > [!IMPORTANT]
    > При использовании сертификата от корпорации Майкрософт вам не нужно приобретать собственный сертификат или имя домена.

    В следующем примере показано, как создать конфигурацию, которая включает сертификат TLS/SSL от Майкрософт:

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

  * При использовании *приобретенного сертификата*вы используете параметры *ssl_cert_pem_file*, *ssl_key_pem_file*и *ssl_cname* . В следующем примере показано, как использовать *PEM* -файлы для создания конфигурации, использующей приобретенный сертификат TLS/SSL.

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

Дополнительные сведения о *enable_ssl*см. в разделе [акспровисионингконфигуратион. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) и [аксаттачконфигуратион. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Развертывание в службе "экземпляры контейнеров Azure"

При развертывании в службе "экземпляры контейнеров Azure" вы предоставляете значения для параметров, связанных с TLS, как показано в следующем фрагменте кода:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Дополнительные сведения см. в разделе [аЦивебсервице. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Обновление DNS

Теперь необходимо обновить DNS, чтобы оно указывало на веб-службу.

+ **Для экземпляров контейнеров:**

  Используйте средства регистратора доменных имен, чтобы обновить запись DNS для доменного имени. Запись должна указывать на IP-адрес службы.

  Может быть задержка в минутах или часах, прежде чем клиенты смогут разрешить доменное имя в зависимости от регистратора и срока жизни (TTL), настроенного для доменного имени.

+ **Для AKS:**

  > [!WARNING]
  > Если вы использовали *leaf_domain_label* для создания службы с помощью сертификата от Майкрософт, не обновляйте вручную значение DNS для кластера. Значение должно быть задано автоматически.

  Обновите DNS общедоступного IP-адреса кластера AKS на вкладке **Конфигурация** в разделе **Параметры** в левой области. (См. следующее изображение.) Общедоступный IP-адрес — это тип ресурса, который создается в группе ресурсов, содержащей узлы агента AKS и другие сетевые ресурсы.

  [![Машинное обучение Azure: Защита веб-служб с помощью TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Обновление сертификата TLS/SSL

Срок действия сертификатов TLS/SSL истекает и их необходимо обновить. Обычно это происходит каждый год. Используйте сведения в следующих разделах, чтобы обновить и продлить сертификат для моделей, развернутых в службе Kubernetes Azure:

### <a name="update-a-microsoft-generated-certificate"></a>Обновление сертификата, созданного корпорацией Майкрософт

Если сертификат изначально был создан корпорацией Майкрософт (при использовании *leaf_domain_label* для создания службы), используйте один из следующих примеров для обновления сертификата.

> [!IMPORTANT]
> * Если существующий сертификат по-прежнему действителен, используйте `renew=True` (пакет SDK) или `--ssl-renew` (CLI), чтобы принудительно обновить конфигурацию. Например, если существующий сертификат по-прежнему действителен в течение 10 дней и не используется `renew=True` , то сертификат может быть не продлен.
> * При первоначальном развертывании службы `leaf_domain_label` используется для создания DNS-имени с помощью шаблона `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` . Чтобы сохранить существующее имя (включая 6 созданных ранее цифр), используйте исходное `leaf_domain_label` значение. Не включайте созданные 6 цифр.

**Использование пакета SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Использование интерфейса командной строки**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Дополнительные сведения см. в следующих справочных документах:

* [сслконфигуратион](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [аксупдатеконфигуратион](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

### <a name="update-custom-certificate"></a>Обновить пользовательский сертификат

Если сертификат был изначально создан центром сертификации, выполните следующие действия.

1. Чтобы продлить сертификат, используйте документацию, предоставленную центром сертификации. Этот процесс создает новые файлы сертификатов.

1. Используйте пакет SDK или CLI для обновления службы с помощью нового сертификата:

    **Использование пакета SDK**

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

Дополнительные сведения см. в следующих справочных документах:

* [сслконфигуратион](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [аксупдатеконфигуратион](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

## <a name="disable-tls"></a>Отключить TLS

Чтобы отключить TLS для модели, развернутой в службе Kubernetes Azure, создайте `SslConfiguration` с помощью `status="Disabled"` , а затем выполните обновление.

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
+ [Использование модели машинного обучения, развернутой в качестве веб-службы](how-to-consume-web-service.md)
+ [Общие сведения о изоляции и конфиденциальности виртуальной сети](how-to-network-security-overview.md)
