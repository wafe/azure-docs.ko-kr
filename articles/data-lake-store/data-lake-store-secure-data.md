---
title: "Azure Data Lake Store에 저장된 데이터 보호 | Microsoft 문서"
description: "그룹 및 액세스 제어 목록을 사용하여 Azure 데이터 레이크 저장소의 데이터를 보호하는 방법에 대해 알아봅니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 7cadb45202c7221876d377546e7c17199ec15809
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2017
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Azure 데이터 레이크 저장소에 저장된 데이터 보호
Azure 데이터 레이크 저장소의 데이터를 보호하는 것은 3단계로 이루어진 방법입니다.

1. Azure Active Directory(AAD)에 보안 그룹을 만들어 시작합니다. 이러한 보안 그룹은 Azure Portal에서 RBAC(역할 기반 액세스 제어)를 구현하는데 사용됩니다. 자세한 내용은 [Microsoft Azure의 역할 기반 Access Control](../active-directory/role-based-access-control-configure.md)을 참조하세요.
2. AAD 보안 그룹을 Azure 데이터 레이크 저장소 계정에 할당합니다. 이는 포털에서 데이터 레이크 저장소 계정에 대한 액세스 및 포털 또는 API에서 포털 및 관리 작업을 제어합니다.
3. AAD 보안 그룹을 데이터 레이크 저장소 파일 시스템에 액세스 제어 목록(ACL)으로 할당합니다.
4. 또한 Data Lake 저장소의 데이터에 액세스할 수 있는 클라이언트에 대한 IP 주소 범위를 설정할 수도 있습니다.

이 문서에서는 Azure 포털을 사용하여 위의 작업을 수행하는 방법에 대한 지침을 제공합니다. Data Lake 저장소가 계정 및 데이터 수준에서 보안을 구현하는 방법에 대한 자세한 내용은 [Azure Data Lake 저장소의 보안](data-lake-store-security-overview.md)을 참조하세요. Azure Data Lake Store에서 ACL을 구현하는 방법에 대한 자세한 내용은 [Data Lake Store에서 Access Control 개요](data-lake-store-access-control.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure 데이터 레이크 저장소 계정**. 만드는 방법에 대한 지침은 [Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Azure Active Directory의 보안 그룹 만들기
AAD 보안 그룹을 만드는 방법 및 사용자를 그룹에 추가하는 방법에 대한 지침은 [Azure Active Directory의 보안 그룹 관리](../active-directory/active-directory-groups-create-azure-portal.md)를 참조하세요.

> [!NOTE] 
> Azure Portal을 사용하여 Azure AD에서 그룹에 사용자 및 다른 그룹을 추가할 수 있습니다. 그러나 서비스 사용자를 그룹에 추가하기 위해서는 [Azure AD PowerShell 모듈](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md)을 사용하세요.
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>사용자 또는 보안 그룹을 Azure 데이터 레이크 저장소 계정에 할당
사용자 또는 보안 그룹을 Azure 데이터 레이크 저장소 계정에 할당하는 경우 Azure 포털 및 Azure 리소스 관리자 API를 사용하여 계정의 관리 작업에 대한 액세스를 제어합니다. 

1. Azure 데이터 레이크 저장소 계정을 엽니다. 왼쪽 창에서 **찾아보기**, **Data Lake Store**를 차례로 클릭한 다음 Data Lake Store 블레이드에서 사용자 또는 보안 그룹을 할당하려는 계정 이름을 클릭합니다.

2. Data Lake Store 계정 설정 블레이드에서 **Access Control(IAM)**을 클릭합니다. 이 블레이드는 기본적으로 **구독 관리자** 그룹을 소유자로 표시합니다.
   
    ![Azure Data Lake Store 계정에 보안 그룹 할당](./media/data-lake-store-secure-data/adl.select.user.icon.png "Azure Data Lake Store 계정에 보안 그룹 할당")

    두 가지 방법으로 그룹을 추가하고 관련 역할을 할당할 수 있습니다.
   
    * 사용자/그룹을 계정에 추가한 다음 역할을 할당하거나
    * 역할을 추가한 다음 사용자/그룹을 역할에 할당합니다.
     
    이 섹션에서는 그룹을 추가한 다음 역할을 할당하는 첫 번째 방법을 살펴봅니다. 먼저 역할을 선택한 다음 그룹을 해당 역할에 할당하는 유사한 단계를 수행할 수 있습니다.
4. **사용자** 블레이드에서 **추가**를 클릭하여 **액세스 추가** 블레이드를 엽니다. **액세스 추가** 블레이드에서 **역할 선택**을 클릭한 다음 사용자/그룹에 대한 역할을 선택합니다.
   
    ![사용자에 대한 역할 추가](./media/data-lake-store-secure-data/adl.add.user.1.png "사용자에 대한 역할 추가")
   
    **소유자** 및 **참가자** 역할은 데이터 레이크 계정에 다양한 관리 기능에 대한 액세스를 제공합니다. Data Lake의 데이터와 상호 작용하는 사용자의 경우 **보기 권한자** 역할에 추가할 수 있습니다. 이러한 역할의 범위는 Azure 데이터 레이크 저장소 계정에 관련된 관리 작업으로 제한됩니다.
   
    데이터 작업의 경우 개별 파일 시스템 권한은 사용자가 수행할 수 있는 것을 정의합니다. 따라서 리더 역할을 가진 사용자는 계정에 연결된 관리 설정만 볼 수 있지만 잠재적으로 할당된 파일 시스템 권한을 기반으로 데이터를 읽고 쓸 수 있습니다. 데이터 레이크 저장소 파일 시스템 권한은 [ACL로 보안 그룹을 Azure 데이터 레이크 저장소 파일 시스템에 할당](#filepermissions)에 설명되어 있습니다.
5. **액세스 추가** 블레이드에서 **사용자 추가**를 클릭하여 **사용자 추가** 블레이드를 엽니다. 이 블레이드에 Azure Active Directory에서 이전에 만든 보안 그룹을 찾습니다. 검색할 그룹이 많을 경우 위쪽의 텍스트 상자를 사용하여 그룹 이름을 필터링합니다. **선택**을 클릭합니다.
   
    ![보안 그룹 추가](./media/data-lake-store-secure-data/adl.add.user.2.png "보안 그룹 추가")
   
    나열되지 않은 그룹/사용자를 추가하려는 경우 **초대** 아이콘을 사용하고 사용자/그룹에 대한 전자 메일 주소를 지정하여 초대할 수 있습니다.
6. **확인**을 클릭합니다. 아래와 같이 추가된 보안 그룹을 확인해야 합니다.
   
    ![추가된 보안 그룹](./media/data-lake-store-secure-data/adl.add.user.3.png "추가된 보안 그룹")

7. 이제 사용자/보안 그룹은 Azure 데이터 레이크 저장소 계정에 대한 액세스를 가집니다. 특정 사용자에게 액세스를 제공하려는 경우 보안 그룹에 추가할 수 있습니다. 마찬가지로 사용자에 대한 액세스를 취소하려는 경우 보안 그룹에서 제거할 수 있습니다. 또한 여러 보안 그룹을 계정에 할당할 수 있습니다. 

## <a name="filepermissions"></a>ACL로 사용자 또는 보안 그룹을 Azure 데이터 레이크 저장소 파일 시스템에 할당
사용자/보안 그룹을 Azure 데이터 레이크 파일 시스템에 할당하여 Azure 데이터 레이크 저장소에 저장된 데이터에 대한 액세스 제어를 설정합니다.

1. 데이터 레이크 저장소 계정 블레이드에서 **데이터 탐색기**를 클릭합니다.
   
    ![Data Lake Store 계정에 디렉터리 만들기](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Data Lake Store 계정에 디렉터리 만들기")
2. **데이터 탐색기** 블레이드에서 ACL을 구성할 파일이나 폴더를 클릭한 다음 **액세스**를 클릭합니다. 파일에 ACL을 할당하려면 **파일 미리 보기** 블레이드에서 **액세스**를 클릭해야 합니다.
   
    ![Data Lake 파일 시스템에 ACL 설정](./media/data-lake-store-secure-data/adl.acl.1.png "Data Lake 파일 시스템에 ACL 설정")
3. **액세스** 블레이드는 루트에 이미 할당된 표준 액세스 및 사용자 지정 액세스를 나열합니다. **추가** 아이콘을 클릭하여 사용자 지정 수준 ACL을 추가합니다.
   
    ![표준 및 사용자 지정 액세스 나열](./media/data-lake-store-secure-data/adl.acl.2.png "표준 및 사용자 지정 액세스 나열")
   
   * **표준 액세스** 는 세 가지 고유한 사용자 클래스(소유자, 그룹 및 기타)에 읽기, 쓰기 실행(rwx)을 지정하는 UNIX 스타일 액세스입니다.
   * **사용자 지정 액세스** 는 파일의 소유자 또는 그룹 뿐만 아니라 특정하게 명명된 사용자 또는 그룹에 대한 권한을 설정할 수 있도록 하는 POSIX ACL에 해당합니다. 
     
     자세한 내용은 [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)을 참조하세요. Data Lake Store에서 ACL이 구현되는 방법에 대한 자세한 내용은 [Data Lake Store의 Access Control](data-lake-store-access-control.md)을 참조하세요.
4. **추가** 아이콘을 클릭하여 **사용자 지정 액세스 추가** 블레이드를 엽니다. 이 블레이드에서 **사용자 또는 그룹 선택**을 클릭한 다음 **사용자 또는 그룹 선택** 블레이드에서 Azure Active Directory에서 이전에 만든 보안 그룹을 검색합니다. 검색할 그룹이 많을 경우 위쪽의 텍스트 상자를 사용하여 그룹 이름을 필터링합니다. 추가하려는 그룹을 클릭한 다음 **선택**을 클릭합니다.
   
    ![그룹 추가](./media/data-lake-store-secure-data/adl.acl.3.png "그룹 추가")
5. **사용 권한 선택**을 클릭하고 사용 권한 및 이러한 권한을 기본 ACL로 할당할지, 액세스 ALC로 할당할지 또는 둘 다로 할당할지 선택합니다. **확인**을 클릭합니다.
   
    ![그룹에 권한 할당](./media/data-lake-store-secure-data/adl.acl.4.png "그룹에 권한 할당")
   
    Data Lake Store의 사용 권한 및 기본/액세스 ACL에 대한 자세한 내용은 [Data Lake Store에서 Access Control](data-lake-store-access-control.md)을 참조하세요.
6. **사용자 지정 액세스 추가** 블레이드에서 **확인**을 클릭합니다. 이제 연결된 권한으로 새롭게 추가된 그룹이 **액세스** 블레이드에 나열됩니다.
   
    ![그룹에 권한 할당](./media/data-lake-store-secure-data/adl.acl.5.png "그룹에 권한 할당")
   
   > [!IMPORTANT]
   > 현재 릴리스에서 **사용자 지정 액세스**에 9개의 항목만이 있습니다. 9개 이상의 사용자를 추가하려는 경우 보안 그룹을 만들고 사용자를 보안 그룹에 추가하며 데이터 레이크 저장소 계정에 대한 해당 보안 그룹에 액세스를 제공해야 합니다.
   > 
   > 
7. 필요한 경우 그룹을 추가한 후에 액세스 권한을 수정할 수도 있습니다. 보안 그룹에 대한 해당 권한을 제거하거나 할당할지 여부에 따라 각 권한 형식(읽기, 쓰기, 실행)에 대한 확인란을 취소하거나 선택합니다. **저장**을 클릭하여 변경 내용을 저장하거나 **취소**를 클릭하여 변경 내용을 취소합니다.

## <a name="set-ip-address-range-for-data-access"></a>데이터 액세스에 대한 IP 주소 범위를 설정합니다.
Azure Data Lake 저장소를 사용하면 데이터 저장소에 대한 액세스를 네트워크 수준에서 추가로 잠글 수 있습니다. 방화벽을 설정하고 IP 주소를 지정하며 신뢰할 수 있는 클라이언트에 대한 범위를 정의할 수 있습니다. 사용하도록 설정되면 정의된 범위 내에 있는 IP 주소를 보유한 클라이언트만 저장소에 연결할 수 있습니다.

![방화벽 설정 및 IP 액세스](./media/data-lake-store-secure-data/firewall-ip-access.png "방화벽 설정 및 IP 주소")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정에 대한 보안 그룹 제거
Azure 데이터 레이크 저장소 계정에서 보안 그룹을 제거하는 경우 Azure Portal 및 Azure Resource Manager API를 사용하여 계정의 관리 작업에 대한 액세스를 변경합니다.

1. Data Lake Store 계정 블레이드에서 **설정**을 클릭합니다. **설정** 블레이드에서 **사용자**를 클릭합니다.
   
    ![Azure Data Lake 계정에 보안 그룹 할당](./media/data-lake-store-secure-data/adl.select.user.icon.png "Azure Data Lake 계정에 보안 그룹 할당")
2. **사용자** 블레이드에서 제거하려는 보안 그룹을 클릭합니다.
   
    ![제거할 보안 그룹](./media/data-lake-store-secure-data/adl.add.user.3.png "제거할 보안 그룹")
3. 보안 그룹에 대한 블레이드에서 **제거**를 클릭합니다.
   
    ![제거된 보안 그룹](./media/data-lake-store-secure-data/adl.remove.group.png "제거된 보안 그룹")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Azure 데이터 레이크 저장소 파일 시스템에서 보안 그룹 ACL 제거
Azure Data Lake Store 파일 시스템에서 보안 그룹 ACL을 제거하면 Data Lake Store의 데이터에 대한 액세스가 변경됩니다.

1. 데이터 레이크 저장소 계정 블레이드에서 **데이터 탐색기**를 클릭합니다.
   
    ![Data Lake 계정에 디렉터리 만들기](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Data Lake 계정에 디렉터리 만들기")
2. **데이터 탐색기** 블레이드에서 ACL을 제거할 파일이나 폴더를 클릭한 다음 사용자 계정 블레이드에서 **액세스**를 클릭합니다. 파일에 대한 ACL을 제거하려면 **파일 미리 보기** 블레이드에서 **액세스**를 클릭해야 합니다.
   
    ![Data Lake 파일 시스템에 ACL 설정](./media/data-lake-store-secure-data/adl.acl.1.png "Data Lake 파일 시스템에 ACL 설정")
3. **액세스** 블레이드의 **사용자 지정 액세스** 섹션에서 제거하려는 보안 그룹을 클릭합니다. **사용자 지정 액세스** 블레이드에서 **제거**를 클릭한 다음 **확인**을 클릭합니다.
   
    ![그룹에 권한 할당](./media/data-lake-store-secure-data/adl.remove.acl.png "그룹에 권한 할당")

## <a name="see-also"></a>참고 항목
* [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)
* [Azure Storage Blob에서 데이터 레이크 저장소로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [데이터 레이크 저장소와 함께 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
* [PowerShell을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-powershell.md)
* [.NET SDK를 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store에 대한 진단 로그 액세스](data-lake-store-diagnostic-logs.md)

