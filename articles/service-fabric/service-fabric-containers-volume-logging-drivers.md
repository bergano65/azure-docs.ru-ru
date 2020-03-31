---
title: Драйвер громкости файлов Azure для сервисной ткани
description: Service Fabric поддерживает использование службы файлов Azure для резервного копирования томов в контейнере.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750046"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Драйвер громкости файлов Azure для сервисной ткани

Драйвер громкости Azure Files — это [плагин громкости Docker,](https://docs.docker.com/engine/extend/plugins_volume/) который предоставляет объемы на основе [файлов Azure](/azure/storage/files/storage-files-introduction) для контейнеров Docker. Он упакован в приложение Service Fabric, которое может быть развернуто в кластере Service Fabric для предоставления объемов для других контейнерных приложений Service Fabric в кластере.

> [!NOTE]
> Версия 6.5.661.9590 плагина объема Azure Files была выпущена для общей доступности.
>

## <a name="prerequisites"></a>Предварительные требования
* Версия подключаемого модуля тома службы файлов Azure для Windows работает только в операционных системах [Windows Server версии 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 версии 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) или более поздних версий.

* Версия подключаемого модуля тома службы файлов Azure для Linux работает во всех версиях операционной системы, поддерживаемых Service Fabric.

* Подключаемый модуль тома службы файлов Azure работает с Service Fabric версии 6.2 и новее.

* Следуйте инструкциям в [документации по службе файлов Azure](/azure/storage/files/storage-how-to-create-file-share), чтобы создать файловый ресурс для контейнерного приложения Service Fabric, который будет использован в качестве тома.

* Вам потребуется установить [PowerShell с модулем Service Fabric](/azure/service-fabric/service-fabric-get-started) или [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli).

* Если вы используете контейнеры Hyper-V, следующие фрагменты должны быть добавлены в раздел ClusterManifest (местный кластер) или fabricSettings в шаблоне Azure Resource Manager (Azure cluster) или ClusterConfig.json (автономный кластер).

В файле ClusterManifest в раздел Hosting нужно добавить приведенный ниже код. В этом примере имя громкости **sfazurefile** и порт он слушает на кластере **19100**. Замените их правильными значениями для кластера.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

В разделе fabricSettings в шаблоне Azure Resource Manager (для развертывания Azure) или ClusterConfig.json (для автономных развертываний) необходимо добавить следующий фрагмент. Опять же, замените имя громкости и значения порта своими собственными.

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

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Развертывание примера приложения с помощью драйвера громкости файлов Service Fabric Azure

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Использование менеджера ресурсов Azure через предоставленный скрипт Powershell (рекомендуется)

Если кластер базируется в Azure, мы рекомендуем развертывать приложения для него с помощью модели ресурсов ресурсов Ресурсов Azure Resource Manager для удобства использования и помочь перейти к модели поддержания инфраструктуры в качестве кода. Этот подход устраняет необходимость отслеживания версии приложения для драйвера громкости Azure Files. Он также позволяет поддерживать отдельные шаблоны менеджер ресурсов Azure для каждой поддерживаемой ОС. Сценарий предполагает развертывание последней версии приложения Azure Files и использует параметры для типа ОС, идентификатора подписки кластера и группы ресурсов. Вы можете скачать сценарий с [сайта загрузки Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Обратите внимание, что это автоматически устанавливает ListenPort, который является портом, на котором плагин громкости Azure Files слушает запросы от Docker daemon, до 19100. Вы можете изменить его, добавив параметр под названием "listenPort". Убедитесь, что порт не противоречит любому другому порту, который использует кластер или приложения.
 

Команда развертывания ресурсов Azure для Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Команда развертывания ресурсов Azure Resource Manager для Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

После успешного запуска скрипта можно перейти к [настройке раздела приложения.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Ручное развертывание для автономных кластеров

Приложение Service Fabric, которое предоставляет объемы для контейнеров, можно загрузить с [сайта загрузки Service Fabric.](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip) Это приложение можно развернуть в кластере с помощью [PowerShell](./service-fabric-deploy-remove-applications.md), [интерфейса командной строки](./service-fabric-application-lifecycle-sfctl.md) или [интерфейсов API FabricClient](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Используя командную строку, измените каталог в корневой каталог загруженного пакета приложений.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Затем скопируйте пакет приложения в хранилище изображений с соответствующими значениями для «ApplicationPackagePath» и «ImageStoreConnectionString»:

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

4. Создайте приложение, уделяя пристальное внимание значению параметра **приложения ListenPort.** Это значение представляет собой порт, на котором плагин громкости Azure Files прослушивает запросы от Docker daemon. Убедитесь, что порт, предоставленный приложению, соответствует VolumePluginPorts в ClusterManifest и не противоречит ни какому другому порту, который использует кластер или приложения.

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
Следуйте шагам 1-3 из [вышеперечисленного.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Число экземпляров службы по умолчанию для приложения подключаемого модуля тома службы файлов Azure равно –1. Это означает, что экземпляр службы развернут на каждом узле в кластере. Тем не менее при развертывании приложения подключаемого модуля тома службы файлов Azure в локальном кластере разработки нужно указать число экземпляров, равное 1. Это можно сделать с помощью параметра приложения **InstanceCount**. Таким образом, команда для создания приложения плагина громкости Azure Files в локальном кластере разработки:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Настройка приложений для использования тома
Следующий фрагмент показывает, как объем на основе файлов Azure может быть указан в файле приложения манифест акрет вашего приложения. Отдельного внимания заслуживает тег **Volume**.

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

Имя драйвера для плагина громкости Azure Files — **sfazurefile.** Это значение устанавливается для атрибута **драйвера** элемента тега **тома** в манифесте приложения.

В теге **объема** в фрагменте выше, плагин громкости Файлов Azure требует следующих атрибутов:
- **Source**. Это имя тома. Для тома можно выбрать любое имя.
- **Направление** - Этот атрибут — это место, в которое отображается объем в бегущей таре. Таким образом, конечной папкой не может быть существующее в контейнере расположение.

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

Чтобы использовать установленный драйвер тома или ведения журнала в своих приложениях, потребуется указать соответствующие значения в элементах **Volume** и **LogConfig** в разделе ** ContainerHostPolicies** в манифесте приложения.

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
* Для развертывания контейнеров в кластер Service Fabric [перевязайте](service-fabric-deploy-container.md) статью Развертывание контейнера на Service Fabric
