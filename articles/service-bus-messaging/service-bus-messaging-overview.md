---
title: "Azure Service Bus 메시지 개요 | Microsoft Docs"
description: "Service Bus 메시지 및 Azure Relay에 대한 설명"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: e299ccfe587d37757cd67cb4367f019b21a09b4a
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Service Bus 메시징: 클라우드에서 유연한 데이터 배달

Microsoft Azure Service Bus는 안정적인 정보 배달 서비스로, 보다 손쉬운 통신을 위해 제공됩니다. 둘 이상의 당사자가 정보를 교환하려면 통신 촉진자가 필요합니다. Service Bus는 조정된 또는 타사 통신 메커니즘으로, 실제 세상에서의 우편 서비스와 비슷합니다. 우편 서비스를 사용하면 원하는 배달 보장 수준을 선택하여 다양한 종류의 서신과 소포를 전 세계 어디로나 매우 쉽게 보낼 수 있습니다.

이처럼 편지를 배달하는 우편 서비스와 마찬가지로 Service Bus는 보낸 사람과 받는 사람이 모두 정보를 유동적으로 배달할 수 있도록 합니다. 메시징 서비스를 사용하면 두 당사자가 동시에 온라인 상태가 아니거나 정확히 동일한 시간에 대화가 가능하지 않더라도 정보를 배달할 수 있습니다. 위에서 설명한 것처럼 이러한 메시징 방식은 편지를 보내는 것과 비슷한 반면 조정되지 않은 통신은 기존의 전화를 거는 것(또는 조정된 메시징과 훨씬 비슷한, 통화 대기 및 발신자 번호 기능이 있기 전의 예전 전화 걸기 방식)과 비슷합니다.

메시지 보낸 사람은 트랜잭션, 중복 항목 검색, 시간 기반 만료, 일괄 처리 등의 여러 배달 특성을 필수 옵션으로 지정할 수도 있습니다. 이러한 패턴 역시 우편 서비스의 반복 배달, 서명 요청, 주소 변경, 회수 등과 비슷합니다.

Service Bus는 *Azure Relay*와 *Service Bus 메시지*의 두 가지 메시지 패턴을 지원합니다.

## <a name="azure-relay"></a>Azure Relay

Azure Relay의 [WCF 릴레이](../service-bus-relay/relay-what-is-it.md) 구성 요소는 다양한 전송 프로토콜 및 웹 서비스 표준을 지원하는 중앙 집중식(이지만 부하가 잘 분산된) 서비스입니다. 여기에는 SOAP, WS-* 및 REST가 포함됩니다. [릴레이 서비스](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)는 다양한 릴레이 연결 옵션을 제공하며 가능한 경우 직접 피어 간 연결을 협상하는 데 도움이 될 수 있습니다. Service Bus는 성능 및 유용성 둘 다에서 WCF(Windows Communication Foundation)를 사용하는 .NET 개발자에게 최적화되어 있으며 SOAP 및 REST 인터페이스를 통해 릴레이 서비스에 대한 모든 권한을 제공합니다. 이로 인해 모든 SOAP 또는 REST 프로그래밍 환경을 Service Bus와 통합할 수 있습니다.

릴레이 서비스는 기존의 단방향 메시징, 요청/응답 메시징 및 피어투피어 메시징을 지원합니다. 또한 인터넷 범위에서 이벤트 배포를 지원하여 향상된 지점 간 효율성을 위한 양방향 소켓 통신과 게시-구독 시나리오를 가능하게 합니다. 릴레이된 메시징 패턴에서는 온-프레미스 서비스가 아웃바운드 포트를 통해 릴레이 서비스에 연결하고 특정 랑데부 주소와 연결된 통신에 사용할 양방향 소켓을 만듭니다. 그러면 클라이언트는 랑데부 주소를 대상으로 하는 릴레이 서비스에 메시지를 전송하여 온-프레미스 서비스와 통신할 수 있습니다. 그 후 릴레이 서비스는 이미 존재하는 양방향 소켓을 통해 온-프레미스 서비스에 메시지를 “릴레이”합니다. 클라이언트는 온-프레미스 서비스에 대한 직접 연결이 필요 없고 서비스가 상주하는 위치를 알 필요도 없으며, 온-프레미스 서비스는 방화벽에 인바운드 포트가 열려 있지 않아도 됩니다.

WCF "릴레이" 바인딩 모음을 사용하여 온-프레미스 서비스와 릴레이 서비스 사이의 연결을 시작합니다. 내부적으로, 릴레이 바인딩은 클라우드에서 Service Bus와 통합하는 WCF 채널 구성 요소를 생성하도록 설계된 전송 바인딩 요소에 매핑합니다.

WCF Relay는 다양한 이점을 제공하지만 메시지를 보내고 받기 위해 서버와 클라이언트가 동시에 온라인 상태여야 합니다. 이는 일반적으로 요청의 수명이 짧은 HTTP 스타일 통신이나 브라우저, 모바일 응용 프로그램 등과 같이 가끔씩만 연결하는 클라이언트에는 적합하지 않습니다. 조정된 메시징은 분리된 통신을 지원하며, 클라이언트와 서버가 필요할 때 연결하고 비동기 방식으로 작업을 수행할 수 있는 고유한 이점이 있습니다.

## <a name="brokered-messaging"></a>조정된 메시징

릴레이 스키마와는 달리 [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)이 있는 Service Bus 메시지는 비동기 또는 "일시적 분리"로 간주할 수 있습니다. 생산자(발신자)와 소비자(수신자)가 동시에 온라인 상태가 아니어도 됩니다. 소비하는 주체에서 메시지를 받을 준비가 될 때까지 메시지 인프라에서 메시지를 "broker"(예: 큐)에 안정적으로 저장합니다. 이렇게 하면 전체 시스템에 영향을 주지 않고 자발적으로(예: 유지 관리의 경우) 또는 구성 요소 크래시로 인해 분산 응용 프로그램 구성 요소의 연결을 끊을 수 있습니다. 또한 업무 종료 시에만 실행해야 하는 재고 관리 시스템과 같이 수신 응용 프로그램이 하루 중 특정 시간에만 온라인 상태여야 하는 경우도 있습니다.

Service Bus 메시지 인프라의 핵심 구성 요소는 큐, 토픽 및 구독입니다. 두 엔터티의 가장 큰 차이점은, 토픽의 경우 여러 받는 사람에게 보내는 기능을 비롯하여 복잡한 콘텐츠 기반 라우팅 및 배달 논리에 사용할 수 있는 게시/구독 기능을 지원한다는 것입니다. 이러한 구성 요소는 임시 분리, 게시/구독 및 부하 분산과 같은 새로운 비동기 메시징 시나리오를 가능하게 합니다. 이러한 메시징 엔터티에 대한 자세한 내용은 [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)을 참조하세요.

WCF 릴레이 인프라와 마찬가지로 조정된 메시징 기능은 WCF 및 .NET Framework 프로그래머를 위해 REST를 통해 제공됩니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

