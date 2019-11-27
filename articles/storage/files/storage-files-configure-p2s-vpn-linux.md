---
title: Настройка VPN-подключения "точка — сеть" (P2S) в Linux для использования с Файлами Azure | Документация Майкрософт
description: Сведения о настройке VPN-подключения "точка — сеть" (P2S) в Linux для использования с Файлами Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126470"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Настройка VPN-подключения "точка — сеть" (P2S) в Linux для использования с Файлами Azure
Вы можете использовать VPN-подключение "точка — сеть" (P2S) для подключения файловых ресурсов Azure по протоколу SMB вне Azure, не открывая порт 445. VPN-подключение "точка — сеть" — это VPN-подключение между Azure и отдельным клиентом. Чтобы использовать VPN-подключение P2S к службе "Файлы Azure", необходимо настроить это подключение для каждого клиента, которому требуется подключиться. Если у вас много клиентов, которым требуется подключение к общим файловым ресурсам Azure из локальной сети, вместо подключения "точка — сеть" для каждого клиента можно использовать VPN-подключение "сеть — сеть" (S2S). Дополнительные сведения см. в статье [Настройка VPN-подключения "сеть — сеть" для использования с Файлами Azure](storage-files-configure-s2s-vpn.md).

Мы настоятельно рекомендуем ознакомиться с [общими сведениями о службе "Файлы Azure"](storage-files-networking-overview.md) перед продолжением работы с этой статьей, чтобы подробно ознакомиться с возможностями сети, доступными для службы "Файлы Azure".

В статье подробно описано, как настроить VPN-подключение "точка — сеть" в Linux для подключения общих файловых ресурсов Azure локально. Если вы хотите направить трафик Синхронизации файлов Azure через VPN, см. статью [Параметры брандмауэра и прокси-сервера Синхронизации файлов Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Предварительные требования
- Последняя версия Azure CLI. Дополнительные сведения об установке Azure CLI см. в статье [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и выберите операционную систему. Если вы предпочитаете использовать Azure PowerShell в Linux, обратите внимание, что приведенные ниже инструкции представлены для Azure CLI.

- Общая папка Azure, которую вы хотите подключить локально. Вы можете использовать общую папку категории [Стандартный](storage-how-to-create-file-share.md) или [Премиум](storage-how-to-create-premium-fileshare.md) с VPN-подключением "точка — сеть".

## <a name="install-required-software"></a>Установка необходимого программного обеспечения
Шлюз виртуальной сети Azure может предоставлять VPN-подключения с помощью нескольких протоколов VPN, включая IPsec и OpenVPN. В этом руководстве показано, как использовать IPsec и пакет strongSwan для обеспечения поддержки в Linux. 

> Проверено на Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>развертывать виртуальную сеть; 
Чтобы получить доступ к общей папке Azure и другим ресурсам Azure из локальной среды через VPN-подключение "точка — сеть", необходимо создать виртуальную сеть или VNet. VPN-подключение P2S, которое будет создано автоматически, — это мост между локальным компьютером Linux и этой виртуальной сетью Azure.

В следующем примере скрипт создает виртуальную сеть Azure с тремя подсетями: одна для конечной точки службы учетной записи хранения, одна для частной конечной точки учетной записи хранения, которая необходима для доступа к учетной записи хранения в локальной среде без создания пользовательской маршрутизации для общедоступного IP-адреса учетной записи хранения, которая может измениться, и одна для шлюза виртуальной сети, предоставляющего службу VPN. 

Не забудьте заменить `<region>`, `<resource-group>` и `<desired-vnet-name>` соответствующими значениями из своей среды.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Ограничение учетной записи хранения виртуальной сетью
По умолчанию при создании учетной записи хранения доступ к ней можно получить из любой точки мира при наличии средств проверки подлинности запроса (например, с помощью идентификатора Active Directory или ключа учетной записи хранения). Чтобы ограничить доступ к этой учетной записи хранения только что созданной виртуальной сети, необходимо создать набор сетевых правил, который разрешает доступ в виртуальной сети и запрещает все остальные права доступа.

Для ограничения доступа к учетной записи хранения для виртуальной сети требуется использовать конечную точку службы. Конечная точка службы — это сетевая конструкция, с помощью которой можно настроить доступ к общедоступному DNS- или IP-адресу для работы только в пределах виртуальной сети. Так как не гарантировано, что общедоступный IP-адрес останется прежним, мы в конечном счете хотим использовать для учетной записи хранилища частную конечную точку, а не конечную точку службы, однако ограничить учетную запись хранилища можно только в том случае, если доступ к конечной точке службы также будет предоставлен.

Не забудьте заменить `<storage-account-name>` учетной записью хранения, к которой требуется получить доступ.

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>Создание частной конечной точки (предварительная версия)
При создании частной конечной точки для учетной записи хранения она получает IP-адрес в пространстве IP-адресов виртуальной сети. При подключении общей папки Azure из локальной среды с помощью этого частного IP-адреса правила маршрутизации, автоматически определяемые установкой VPN, будут маршрутизировать запрос на подключение к учетной записи хранения через VPN. 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>Создание сертификата для проверки подлинности VPN
Чтобы VPN-подключения с локальных компьютеров Linux прошли проверку подлинности для доступа к виртуальной сети, необходимо создать два сертификата: корневой сертификат, который будет предоставляться шлюзу виртуальной машины, и сертификат клиента, который будет подписываться с помощью корневого сертификата. Следующий скрипт создает необходимые сертификаты.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Развертывание шлюза виртуальной сети
Виртуальный сетевой шлюз Azure — это служба, к которой будут подключаться ваши локальные машины Linux. Развертывание этой службы требует двух основных компонентов: общедоступный IP-адрес, который будет определять шлюз для ваших клиентов, где бы они ни находились, и корневой сертификат, созданный вами ранее, который будет использоваться для проверки подлинности клиентов.

Не забудьте заменить `<desired-vpn-name-here>` именем, которое вы хотели бы использовать для этих ресурсов.

> [!Note]  
> Развертывание шлюза виртуальной сети Azure может длиться до 45 минут. При развертывании этого ресурса этот скрипт Bash будет заблокирован для завершения развертывания. Это ожидаемое поведение.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Настройка VPN-клиента
Шлюз виртуальной сети Azure создаст скачиваемый пакет с файлами конфигурации, необходимыми для инициализации VPN-подключения на локальном компьютере с Linux. Следующий скрипт поместит созданные сертификаты в нужном месте и настроит в файле `ipsec.conf` правильные значения из файла конфигурации в скачиваемом пакете.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Подключение файлового ресурса Azure
Теперь, когда вы настроили VPN-подключение "точка — сеть", можно подключить файловый ресурс Azure. В следующем примере файловый ресурс будет подключен непостоянно. Сведения о постоянном подключении см. в статье [Use Azure Files with Linux](storage-how-to-use-files-linux.md) (Использование Файлов Azure в Linux) 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>См. также
- [Рекомендации по работе с сетями службы "Файлы Azure"](storage-files-networking-overview.md)
- [Настройка VPN-подключения "точка — сеть" (P2S) в Windows для использования с Файлами Azure](storage-files-configure-p2s-vpn-windows.md)
- [Настройка VPN-подключения "сеть — сеть" (S2S) для использования с Файлами Azure](storage-files-configure-s2s-vpn.md)