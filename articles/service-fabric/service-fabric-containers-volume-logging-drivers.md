---
title: "Azure Service Fabric Docker Compose 미리 보기 | Microsoft Docs"
description: "Azure Service Fabric은 Docker Compose 형식을 수락하여 Service Fabric을 통해 기존 컨테이너를 보다 쉽게 조정할 수 있도록 합니다. 이 지원은 현재 미리 보기로 제공되고 있습니다."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 955f84e5656bbf568234cbaf69faa4dd0a741206
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>컨테이너의 볼륨 플러그 인 및 로깅 드라이버 사용
Service Fabric은 컨테이너 서비스에 대한 [Docker 볼륨 플러그 인](https://docs.docker.com/engine/extend/plugins_volume/) 및 [Docker 로깅 드라이버](https://docs.docker.com/engine/admin/logging/overview/) 지정을 지원합니다.  이로 인해 컨테이너를 이동하거나 다른 호스트에서 다시 시작한 경우에도 [Azure Files](https://azure.microsoft.com/en-us/services/storage/files/)에 데이터를 유지할 수 있습니다.

현재는 아래에 표시된 것처럼 Linux 컨테이너용 볼륨 드라이버만 있습니다.  Windows 컨테이너를 사용하는 경우, Windows Server의 최신 1709 버전을 사용하면 볼륨 드라이버 없이 Azure Files [SMB3 공유](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/)에 볼륨을 매핑할 수 있습니다. 이 경우 클러스터의 Virtual Machines를 Windows Server 1709 버전으로 업데이트해야 합니다.


## <a name="install-volumelogging-driver"></a>볼륨/로깅 드라이버 설치

Docker 볼륨/로깅 드라이버가 컴퓨터에 설치되어 있지 않으면 RDP/SSH-ing를 통해 컴퓨터에 수동으로 설치하거나 [VMSS 시작 스크립트](https://azure.microsoft.com/en-us/resources/templates/201-vmss-custom-script-windows/)를 통해 또는 [SetupEntryPoint](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-model#describe-a-service) 스크립트를 사용하여 설치합니다. 언급된 방법 중 하나를 선택하여 [Azure용 Docker 볼륨 드라이버](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)를 설치하는 스크립트를 작성합니다.


```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>매니페스트에 플러그 인 또는 드라이버 지정
플러그 인은 다음 매니페스트에 표시된 대로 응용 프로그램 매니페스트에서 지정됩니다.

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
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

앞의 예제에서 `Volume`에 대한 `Source` 태그는 원본 폴더를 나타냅니다. 원본 폴더는 컨테이너 또는 영구 원격 저장소를 호스트하는 VM의 폴더일 수 있습니다. `Destination` 태그는 실행 중인 컨테이너에서 `Source`가 매핑되는 위치입니다.  따라서 대상은 컨테이너 내의 기존 위치가 될 수 없습니다.

볼륨 플러그 인을 지정할 때 Service Fabric은 지정된 매개 변수를 사용하여 볼륨을 자동으로 만듭니다. `Source` 태그는 볼륨의 이름이며 `Driver` 태그는 볼륨 드라이버 플러그 인을 지정합니다. 다음 코드 조각에 나와 있는 대로 `DriverOption` 태그를 사용하여 옵션을 지정할 수 있습니다.

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Docker 로그 드라이버가 지정된 경우 클러스터의 로그를 처리할 에이전트(또는 컨테이너)를 배포해야 합니다.  `DriverOption` 태그를 사용하여 로그 드라이버 옵션을 지정할 수 있습니다.

Service Fabric 클러스터에 컨테이너를 배포하려면 다음 문서를 참조하세요.


[Service Fabric에 컨테이너 배포](service-fabric-deploy-container.md)

