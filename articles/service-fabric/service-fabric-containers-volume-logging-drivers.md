---
title: Service Fabric Azure Files Volume Driver (GA) | Microsoft Docs
description: Service Fabric поддерживает использование службы файлов Azure для резервного копирования томов в контейнере.
services: service-fabric
author: athinanthny
manager: chackdan
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.topic: conceptual
ms.date: 6/10/2018
ms.author: atsenthi
ms.openlocfilehash: 1287df567c60b7ad851c94a8ba787270255d0f35
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422780"
---
# <a name="service-fabric-azure-files-volume-driver"></a>Service Fabric Azure Files Volume Driver
The Azure Files volume plugin, a [Docker volume plugin](https://docs.docker.com/engine/extend/plugins_volume/) that provides [Azure Files](/azure/storage/files/storage-files-introduction) based volumes for Docker containers is now **GA (Generally Available)** .

Этот подключаемый модуль тома Docker упакован в виде приложения Service Fabric, которое можно развернуть в кластерах Service Fabric. Он позволяет предоставить тома службы "Файлы Azure" для других контейнерных приложений Service Fabric, развернутых в кластере.

> [!NOTE]
> Version 6.5.661.9590 of the Azure Files volume plugin is a GA(Generally available) release. 
>

## <a name="prerequisites"></a>Технические условия
* Версия подключаемого модуля тома службы файлов Azure для Windows работает только в операционных системах [Windows Server версии 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 версии 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) или более поздних версий.

* Версия подключаемого модуля тома службы файлов Azure для Linux работает во всех версиях операционной системы, поддерживаемых Service Fabric.

* Подключаемый модуль тома службы файлов Azure работает с Service Fabric версии 6.2 и новее.

* Следуйте инструкциям в [документации по службе файлов Azure](/azure/storage/files/storage-how-to-create-file-share), чтобы создать файловый ресурс для контейнерного приложения Service Fabric, который будет использован в качестве тома.

* Вам потребуется установить [PowerShell с модулем Service Fabric](/azure/service-fabric/service-fabric-get-started) или [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli).

* If you are using Hyper-V containers, the following snippets need to be added in the ClusterManifest (local cluster) or fabricSettings section in your Azure Resource Manager template (Azure cluster) or ClusterConfig.json (standalone cluster).

В файле ClusterManifest в раздел Hosting нужно добавить приведенный ниже код. In this example, the volume name is **sfazurefile** and the port it listens to on the cluster is **19100**. Replace them with the correct values for your cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

In the fabricSettings section in your Azure Resource Manager template (for Azure deployments) or ClusterConfig.json (for standalone deployments), the following snippet needs to be added. Again, replace the volume name and port values with your own.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```


## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Deploy a sample application using Service Fabric Azure Files volume driver

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Using Azure Resource Manager via the provided Powershell script (recommended)

If your cluster is based in Azure, we recommend deploying applications to it using the Azure Resource Manager application resource model for ease of use and to help move towards the model of maintaining infrastructure as code. This approach eliminates the need to keep track of the app version for the Azure Files volume driver. It also enables you to maintain separate Azure Resource Manager templates for each supported OS. The script assumes you are deploying the latest version of the Azure Files application and takes parameters for OS type, cluster subscription ID, and resource group. You can download the script from the [Service Fabric download site](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Note that this automatically sets the ListenPort, which is the port on which the Azure Files volume plugin listens for requests from the Docker daemon, to 19100. You can change it by adding parameter named "listenPort". Ensure that the port does not conflict with any other port that the cluster or your applications uses.
 

Azure Resource Manager deployment command for Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Azure Resource Manager deployment command for Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Once you've successfully run the script, you can skip to the [configuring your application section.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Manual deployment for standalone clusters

The Service Fabric application that provides the volumes for your containers can be downloaded from the [Service Fabric download site](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip). Это приложение можно развернуть в кластере с помощью [PowerShell](./service-fabric-deploy-remove-applications.md), [интерфейса командной строки](./service-fabric-application-lifecycle-sfctl.md) или [интерфейсов API FabricClient](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Using the command line, change directory to the root directory of the downloaded application package.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Next, copy the application package to the image store  with the appropriate values for [ApplicationPackagePath] and [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. регистрация типа приложения;

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Create the application, paying close attention to the **ListenPort** application parameter value. This value is the port on which the Azure Files volume plugin listens for requests from the Docker daemon. Ensure that the port provided to the application matches the VolumePluginPorts in the ClusterManifest and does not conflict with any other port that the cluster or your applications uses.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter не поддерживает сопоставление подключений SMB с контейнерами ([поддерживается только в Windows Server версии 1709](/virtualization/windowscontainers/manage-containers/container-storage)). Это ограничение предотвращает сопоставление сетевых томов и использование драйверов томов службы файлов Azure в версиях, предшествующих версии 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Развертывание приложения в локальном кластере разработки
Follow steps 1-3 from the [above.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Число экземпляров службы по умолчанию для приложения подключаемого модуля тома службы файлов Azure равно –1. Это означает, что экземпляр службы развернут на каждом узле в кластере. Тем не менее при развертывании приложения подключаемого модуля тома службы файлов Azure в локальном кластере разработки нужно указать число экземпляров, равное 1. Это можно сделать с помощью параметра приложения **InstanceCount**. Therefore, the command for creating the Azure Files volume plugin application on a local development cluster is:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Настройка приложений для использования тома
The following snippet shows how an Azure Files based volume can be specified in the application manifest file of your application. Отдельного внимания заслуживает тег **Volume**.

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Имя драйвера для подключаемого модуля тома службы файлов Azure — **sfazurefile**. This value is set for the **Driver** attribute of the **Volume** tag element in the application manifest.

In the **Volume** tag in the snippet above, the Azure Files volume plugin requires the following attributes:
- **Source**. Это имя тома. Для тома можно выбрать любое имя.
- **Destination** - This attribute is the location that the volume is mapped to within the running container. Таким образом, конечной папкой не может быть существующее в контейнере расположение.

Как показано в элементах **DriverOption** в приведенном выше фрагменте кода, подключаемый модуль тома службы файлов Azure поддерживает следующие параметры драйвера.
- **shareName**. Имя файлового ресурса службы файлов Azure, предоставляющего том для контейнера.
- **storageAccountName**. Имя учетной записи хранения Azure, которая содержит файловый ресурс службы файлов Azure.
- **storageAccountKey**. Ключ доступа учетной записи хранения Azure, которая содержит файловый ресурс службы файлов Azure.
- **storageAccountFQDN** — доменное имя, связанное с учетной записью хранения. Если параметр storageAccountFQDN не указан, доменное имя будет сформировано с использованием суффикса по умолчанию (.file.core.windows.net) с именем storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Использование драйвера собственного тома или ведения журнала
Service Fabric дает возможность использовать драйверы пользовательских [томов](https://docs.docker.com/engine/extend/plugins_volume/) и драйверы [ведения журналов](https://docs.docker.com/engine/admin/logging/overview/). Если в кластере не установлен драйвер тома или ведения журнала Docker, его можно установить вручную с помощью протоколов RDP и SSH. Используя эти протоколы, можно выполнить установку с помощью [скрипта запуска масштабируемого набора виртуальных машин](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) или [скрипта SetupEntryPoint](/azure/service-fabric/service-fabric-application-model).

Пример скрипта установки [драйвера тома Docker для Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/):

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Чтобы использовать установленный драйвер тома или ведения журнала в своих приложениях, потребуется указать соответствующие значения в элементах **Volume** и **LogConfig** в разделе  **ContainerHostPolicies** в манифесте приложения.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

При указании подключаемого модуля тома Service Fabric автоматически создает том, используя заданные параметры. Тег **Source** в элементе **Volume** — это имя тома, а тег **Driver** — подключаемый модуль драйвера тома. Тег **Destination** указывает на расположение, к которому подключается исходная папка (**Source**) в работающем контейнере. Таким образом, конечной папкой не может быть существующее в контейнере расположение. Параметры можно указать с помощью тега **DriverOption** следующим образом:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Параметры приложения поддерживаются для томов, как показано в предыдущем фрагменте кода манифеста (пример использования можно найти по тексту `MyStorageVar`).

Если задан драйвер ведения журналов Docker, необходимо развернуть агенты (или контейнеры) для обработки журналов в кластере. Чтобы указать параметры драйвера ведения журналов можно использовать тег **DriverOption**.

## <a name="next-steps"></a>Дальнейшие действия
* Примеры контейнеров, включая драйвер тома, приведены в разделе [Примеры контейнеров Service Fabric](https://github.com/Azure-Samples/service-fabric-containers).
* Развертывание контейнеров в кластере Service Fabric описывается в разделе [Развертывание контейнера в Service Fabric](service-fabric-deploy-container.md).
