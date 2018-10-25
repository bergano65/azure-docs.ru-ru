---
title: Защита веб-служб Машинного обучения Azure с помощью SSL
description: Узнайте, как защитить веб-службы, развернутые с помощью службы Машинного обучения Azure. Вы можете ограничить доступ к веб-службам и защитить данные, отправленные клиентами, с помощью SSL и проверки подлинности на основе ключа.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801018"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>Защита веб-служб Машинного обучения Azure с помощью SSL

В этой статье вы узнаете, как защитить веб-службы, развернутые с помощью службы Машинного обучения Azure. Вы можете ограничить доступ к веб-службам и защитить данные, отправленные клиентами, с помощью SSL и проверки подлинности на основе ключа.

> [!Warning]
> Если вы не включите SSL, любой пользователь в Интернете сможет совершать вызовы к веб-службе.

SSL шифрует данные, отправляемые между клиентом и веб-службой. Он также используется клиентом для проверки удостоверения сервера. Аутентификация разрешена только для служб, которые предоставили SSL-сертификат и ключ.  Если вы включите SSL, при доступе к веб-службе потребуется ключ проверки подлинности.

При развертывании веб-службы, для которой включен протокол SSL, или при включении SSL для существующей развернутой веб службы действия будут одинаковы:

1. Получите имя домена.

2. Получите SSL-сертификат.

3. Разверните или обновите веб-службу со включенным параметром SSL.

4. Обновите имя DNS, чтобы оно указывало на веб-службу.

При защите веб-служб в разных [целевых объектах развертывания](how-to-deploy-and-where.md) есть небольшие отличия. 

## <a name="get-a-domain-name"></a>Получение доменного имени

Если у вас еще нет имени домена, вы можете приобрести его у __регистратора доменных имен__. Процесс различается у разных регистраторов, как и стоимость. Регистратор также предоставляет вам инструменты для управления доменным именем. Эти инструменты используются для сопоставления полного доменного имени (например, www.contoso.com) с IP-адресом, по которому размещается ваша веб-служба.

## <a name="get-an-ssl-certificate"></a>Получите SSL-сертификат.

Существует множество способов получить SSL-сертификат. Наиболее распространенным является покупка в одном из __центров сертификации__. Независимо от того, где вы получаете сертификат, вам нужны следующие файлы:

* __Сертификат__. Сертификат должен содержать полную цепочку сертификатов и должен быть в кодировке PEM.
* __Ключ__. Ключ должен быть в кодировке PEM.

При запросе сертификата вы должны предоставить полное доменное имя (FQDN) адреса, который вы планируете использовать для этой веб-службы. Например, www.contoso.com. Адрес, указанный в сертификате, и адрес, используемый клиентами, сравниваются при проверке удостоверения веб-службы. Если адреса не совпадают, клиенты получат сообщение об ошибке. 

> [!TIP]
> Если центр сертификации не может предоставить сертификат и ключ в виде файлов в кодировке PEM, вы можете использовать такую служебную программу, как [OpenSSL](https://www.openssl.org/), чтобы изменить формат.

> [!IMPORTANT]
> Эти самозаверяющие сертификаты следует использовать только для разработки. Они не должны использоваться в рабочей среде. Если вы используете самозаверяющий сертификат, см. инструкции в разделе [Использование веб-служб с самозаверяющими сертификатами](#self-signed).


## <a name="enable-ssl-and-deploy"></a>Включение SSL и развертывание службы

Чтобы развернуть (или развернуть повторно) службу со включенным SSL, установите для параметра `ssl_enabled` значение `True` везде, где это применимо. Установите для параметра `ssl_certificate` значение файла __сертификата__, а для параметра `ssl_key` значение __ключа__. 

+ **Развертывание в Службе Azure Kubernetes (AKS)**
  
  При подготовке кластера AKS укажите значения для параметров, связанных с SSL, как показано во фрагменте кода:

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Развертывание в Экземплярах контейнеров Azure (ACI)**
 
  При развертывании в ACI укажите значения для параметров, связанных с SSL, как показано во фрагменте кода:

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Развертывание в программируемых пользователем вентильных матрицах (FPGA)**

  Ответ операции `create_service` содержит IP-адрес службы. IP-адрес используется при сопоставлении имени DNS с IP-адресом службы. Ответ также содержит __первичный__ и __вторичный ключ__, используемые для применения службы. Укажите значения для параметров, связанных с SSL, как показано во фрагменте кода:

    ```python
    from amlrealtimeai import DeploymentClient
    
    subscription_id = "<Your Azure Subscription ID>"
    resource_group = "<Your Azure Resource Group Name>"
    model_management_account = "<Your AzureML Model Management Account Name>"
    location = "eastus2"
    
    model_name = "resnet50-model"
    service_name = "quickstart-service"
    
    deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)
    
    with open('cert.pem','r') as cert_file:
        with open('key.pem','r') as key_file:
            cert = cert_file.read()
            key = key_file.read()
            service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
    ```

## <a name="update-your-dns"></a>Обновление DNS

Теперь необходимо обновить DNS, чтобы оно указывало на веб-службу.

+ **Для ACI и FPGA**:  

  Используйте инструменты, предоставленные вашим регистратором доменных имен, чтобы обновить запись DNS для вашего доменного имени. Запись должна указывать на IP-адрес службы.  

  В зависимости от регистратора и срока жизни, настроенного для имени домена, может потребоваться от нескольких минут до нескольких часов, прежде чем клиенты смогут разрешить имя домена.

+ **Для AKS**: 

  Обновите имя DNS на вкладке "Конфигурация" окна "Общедоступный IP-адрес" кластера AKS, как показано на изображении. Общедоступный IP-адрес можно найти как один из типов ресурсов в группе ресурсов, содержащей узлы агента AKS и другие сетевые ресурсы.

  ![Служба Машинного обучение Azure. Защита веб-служб с помощью SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>Использование аутентифицированных служб

### <a name="how-to-consume"></a>Как использовать 
+ **Для ACI и AKS**: 

  Узнайте, как использовать веб-службы для ACI и AKS, в следующих статьях:
  + [Развертывание веб-служб в Экземплярах контейнеров Azure](how-to-deploy-to-aci.md)

  + [Развертывание моделей из Службы машинного обучения Azure в Службе Azure Kubernetes](how-to-deploy-to-aks.md)

+ **Для FPGA**:  

  В следующих примерах показано, как использовать аутентифицированную службу FPGA с помощью Python и C#:
  Замените `authkey` первичным или вторичным ключом, возвращаемым после развертывания службы.

  Пример на Python:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  Пример на C#:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>Установка заголовка авторизации
Другие клиенты gRPC могут аутентифицировать запросы, установив заголовок авторизации. Общий подход заключается в создании объекта `ChannelCredentials`, который объединяет `SslCredentials` с `CallCredentials`. Это добавляется в заголовок авторизации запроса. Дополнительные сведения о реализации поддержки конкретных заголовков см. на странице [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

В следующих примерах показано, как установить заголовок для C# и Go:

+ Задайте заголовок на C#:
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Задайте заголовок, используя Go:
    ```go
    conn, err := grpc.Dial(serverAddr, 
        grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
        grpc.WithPerRPCCredentials(&authCreds{
        Key: "authKey"}))
    
    type authCreds struct {
        Key string
    }
    
    func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
        return map[string]string{
            "authorization": c.Key,
        }, nil
    }
    
    func (c *authCreds) RequireTransportSecurity() bool {
        return true
    }
    ```

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>Использование служб с самозаверяющими сертификатами

Существует два способа, позволяющих клиенту выполнить аутентификацию на сервере, защищенном с использованием самозаверяющего сертификата:

* В системе клиента установите переменную среды `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH`, чтобы указать файл сертификата.

* При создании объекта `SslCredentials` передайте содержимое файла сертификата в конструктор.

Использование любого из методов приведет к тому, что gRPC будет использовать сертификат в качестве корневого сертификата.

> [!IMPORTANT]
> gRPC не принимает ненадежные сертификаты. Использование ненадежного сертификата завершится ошибкой с кодом состояния `Unavailable`. Подробные сведения об ошибке будут содержать сообщение `Connection Failed`.
