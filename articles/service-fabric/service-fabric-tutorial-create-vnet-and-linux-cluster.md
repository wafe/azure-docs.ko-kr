---
title: "Azure에서 Linux Service Fabric 클러스터 만들기 | Microsoft Docs"
description: "Azure CLI를 사용하여 기존 Azure 가상 네트워크에 Linux Service Fabric 클러스터를 배포하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 84b219d31635af6fbdb6bd618e3a9bb4e4848809
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Azure 가상 네트워크에 Service Fabric Linux 클러스터 배포
이 자습서는 시리즈의 1부입니다. Azure CLI를 사용하여 기존 Azure VNET(가상 네트워크)에 Linux Service Fabric 클러스터를 배포하는 방법을 알아봅니다. 작업이 완료되면 응용 프로그램을 배포할 수 있는, 클라우드에서 실행되는 클러스터가 생깁니다. PowerShell을 사용하여 Windows 클러스터를 만들려면 [Azure에서 보안 Windows 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure에서 VNET 만들기
> * Azure CLI를 사용하여 Azure에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * Service Fabric CLI를 사용하여 클러스터에 연결
> * 클러스터 제거

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에서 보안 클러스터 만들기
> * [클러스터 규모 확장 또는 규모 감축](/service-fabric-tutorial-scale-cluster.md)
> * [Service Fabric을 사용하여 API Management 배포](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Service Fabric CLI](service-fabric-cli.md)를 설치합니다.
- [Azure CLI 2.0](/cli/azure/install-azure-cli)을 설치합니다.

다음 절차에서는 5노드 Service Fabric 클러스터를 만듭니다. Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure에 로그인 및 구독 선택
이 가이드에서는 Azure CLI를 사용합니다. 새 세션을 시작하는 경우 Azure 명령을 실행하기 전에 Azure 계정에 로그인하고 구독을 선택합니다.
 
다음 스크립트를 실행하여 Azure 계정에 로그인하고 구독을 선택합니다.

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
배포에 새 리소스 그룹을 만들고 이름과 위치를 지정합니다.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>네트워크 토폴로지를 배포합니다.
다음으로, API Management 및 Service Fabric 클러스터가 배포될 네트워크 토폴로지를 설정합니다. [network.json][network-arm] Resource Manager 템플릿은 VNET(가상 네트워크), Service Fabric에 대한 서브넷 및 NSG(네트워크 보안 그룹), API Management에 대한 서브넷 및 NSG를 만들도록 구성되었습니다. VNET, 서브넷 및 NSG에 대한 자세한 내용은 [여기](../virtual-network/virtual-networks-overview.md)를 참조하세요.

[network.parameters.json][network-parameters-arm] 매개 변수 파일에는 Service Fabric 및 API Management가 배포되는 서브넷 및 NSG의 이름이 포함되어 있습니다.  API Management는 [다음 자습서](service-fabric-tutorial-deploy-api-management.md)에서 배포됩니다. 이 가이드에서는 매개 변수 값을 변경할 필요가 없습니다. 이러한 값은 Service Fabric Resource Manager 템플릿에서 사용됩니다.  여기서 값은 수정하는 경우 이 자습서 및 [API Management 배포 자습서](service-fabric-tutorial-deploy-api-management.md)에서 사용된 다른 Resource Manager 템플릿에서 수정해야 합니다. 

다음 Resource Manager 템플릿 및 매개 변수 파일을 다운로드합니다.
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

다음 스크립트를 사용하여 네트워크 설정에 대해 Resource Manager 템플릿 및 매개 변수 파일을 배포합니다.

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Service Fabric 클러스터 배포
네트워크 리소스에서 배포가 완료되면 다음 단계는 Service Fabric 클러스터용으로 지정된 서브넷 및 NSG의 VNET에 Service Fabric 클러스터를 배포하는 것입니다. 이 문서의 앞부분에서 배포된 기존 VNET과 서브넷에 클러스터를 배포하려면 Resource Manager 템플릿이 필요합니다.  자세한 내용은 [Azure Resource Manager를 사용하여 클러스터 만드기](service-fabric-cluster-creation-via-arm.md)를 참조하세요. 이 자습서 시리즈에서는 이전 단계에서 설정한 VNET, 서브넷 및 NSG의 이름을 사용하도록 템플릿을 미리 구성했습니다.  

다음 Resource Manager 템플릿 및 매개 변수 파일을 다운로드합니다.
- [linuxcluster.json][cluster-arm]
- [linuxcluster.parameters.json][cluster-parameters-arm]

이 템플릿을 사용하여 보안 클러스터를 만듭니다.  클러스터 인증서는 노드 간 통신을 보호하고 관리 클라이언트에 클러스터 관리 끝점을 인증하는 데 사용되는 X.509 인증서입니다.  클러스터 인증서는 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer용 SSL도 제공합니다. Azure Key Vault는 Azure에서 서비스 패브릭 클러스터에 대한 인증서를 관리하는 데 사용됩니다.  클러스터를 Azure에 배포할 때 서비스 패브릭 클러스터 생성을 담당하는 Azure 리소스 공급자는 주요 자격 증명 모음에서 인증서를 가져와 클러스터 VM에 설치합니다. 

CA(인증 기관)의 인증서를 클러스터 인증서로 사용할 수도 있고 자체 서명된 인증서를 만들어서 테스트 용도로 사용할 수도 있습니다. 클러스터 인증서는 다음 조건을 충족해야 합니다.

- 개인 키를 포함해야 합니다.
- 키 교환을 위해 만들어야 합니다. 이 인증서는 개인 정보 교환(.pfx) 파일로 내보낼 수 있습니다.
- 인증서의 주체 이름이 Service Fabric 클러스터 액세스에 사용되는 도메인과 일치해야 합니다. 클러스터의 HTTPS 관리 끝점 및 Service Fabric Explorer에 대해 SSL을 제공하려면 이렇게 일치해야 합니다. .cloudapp.azure.com 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다.  클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

배포를 위해 *linuxcluster.parameters.json* 파일에 빈 매개 변수를 입력합니다.

|매개 변수|값|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|

자체 서명된 인증서를 만들려는 경우 **certificateThumbprint**, **certificateUrlValue** 및 **sourceVaultValue** 매개 변수를 비워 둡니다.  이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 해당 매개 변수 값을 입력합니다.

다음 스크립트는 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) 명령 및 템플릿을 사용하여 Azure에 새 클러스터를 배포합니다. 또한 이 cmdlet은 Azure에 새로운 키 자격 증명 모음을 만들고, 키 자격 증명 모음에 자체 서명된 인증서를 추가하고, 인증서 파일을 로컬로 다운로드합니다. [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create)의 다른 매개 변수를 사용하여 기존 인증서 및/또는 키 자격 증명 모음을 지정할 수 있습니다.

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>보안 클러스터에 연결
키를 사용하여 Service Fabric CLI `sfctl cluster select` 명령을 통해 클러스터에 연결합니다.  자체 서명된 인증서에만 **--no-verify** 옵션을 사용합니다.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

`sfctl cluster health` 명령을 사용하여 연결되어 있고 클러스터 상태가 정상인지 확인합니다.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>리소스 정리
이 자습서 시리즈의 다른 문서에서는 방금 만든 클러스터를 사용합니다. 다음 문서로 바로 이동하지 않는 경우 요금이 발생하지 않도록 클러스터를 삭제하는 것이 좋습니다. 클러스터 및 클러스터에서 사용하는 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다.

Azure에 로그인하고 클러스터를 제거할 구독 ID를 선택합니다.  [Azure Portal](http://portal.azure.com)에 로그인하여 구독 ID를 찾을 수 있습니다. [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) 명령을 사용하여 리소스 그룹 및 모든 클러스터 리소스를 삭제합니다.

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure에서 VNET 만들기
> * Azure CLI를 사용하여 Azure에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * Service Fabric CLI를 사용하여 클러스터에 연결
> * 클러스터 제거

이제 다음 자습서로 넘어가서 클러스터 규모를 조정하는 방법을 알아보겠습니다.
> [!div class="nextstepaction"]
> [클러스터 규모 조정](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
