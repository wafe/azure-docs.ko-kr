---
title: "기존 플레이어를 사용하여 콘텐츠 재생 - Azure | Microsoft Docs"
description: "이 항목에서는 콘텐츠를 재생하는 데 사용할 수 있는 기존 플레이어를 나열합니다."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
ms.openlocfilehash: 48f373b013b1192c353352b801876d706d91dd28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="playing-your-content-with-existing-players"></a>기존 플레이어를 사용하여 콘텐츠 재생
Azure Media Services는 부드러운 스트리밍, HTTP 라이브 스트리밍 및 Mpeg-dash와 같은 여러 인기 있는 스트리밍 형식을 지원합니다. 이 항목에는 스트림을 테스트하는 데 사용할 수 있는 기존 플레이어가 나와 있습니다.

### <a name="the-azure-portal-media-services-content-player"></a>Azure Portal Media Services 콘텐츠 플레이어
**Azure** 포털에서는 비디오를 테스트하는 데 사용할 수 있는 콘텐츠 플레이어를 제공합니다.

원하는 비디오( [게시된](media-services-portal-publish.md)것이어야 함)를 클릭하고 포털 맨 아래에 있는 **재생** 단추를 클릭합니다.

다음과 같은 몇 가지 고려 사항이 적용됩니다.

* **MEDIA SERVICES CONTENT PLAYER** 가 기본 스트리밍 끝점에서 재생됩니다. 기본이 아닌 스트리밍 끝점에서 재생하려면 다른 플레이어를 사용합니다. 예를 들어 [Azure 미디어 플레이어](http://amsplayer.azurewebsites.net/azuremediaplayer.html)를 사용합니다.

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure 미디어 플레이어
[Azure 미디어 플레이어](http://amsplayer.azurewebsites.net/azuremediaplayer.html) 를 사용하여 다음 형식 중 하나로 콘텐츠(일반 또는 보호됨)를 재생합니다.

* 부드러운 스트리밍
* MPEG DASH
* HLS
* 프로그레시브 MP4

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>AES 암호화 토큰
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Silverlight 플레이어
#### <a name="monitoring"></a>모니터링
[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>PlayReady 토큰
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH 플레이어
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>기타
다음을 이용하여 HLS URL을 테스트할 수도 있습니다.

* **Safari** 또는
* **3ivx HLS 플레이어** 

## <a name="developing-video-players"></a>비디오 플레이어 개발
사용자 고유의 플레이어를 개발하는 방법에 대한 자세한 내용은 [비디오 플레이어 개발](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
