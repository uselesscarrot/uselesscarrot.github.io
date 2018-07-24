---
title: AWS Media Services 정리
updated: 2018-01-26
---

# AWS Media Services 정리
>https://qiita.com/A_K_Turry/items/035f763122793c304fd3 의 번역본입니다

## AWS Elemental MediaLive

고품질의 브로드캐스트나 스트리밍이 가능한 실시간 인코딩 서비스로 비디오(영상 컨텐츠)를 입력하여 라이브 출력으로 변환.
부하와 채널 수에 따라 유연하게 확장 가능한 리소스로서, 라이브 채널을 수 분만에 전개가능
채널 기반의 Pay-as-you-go 서비스 모니터링 및 복구 기능이 포함되어 있으며, 운영을 간소화하고 비용 효율성을 향상
인코딩이나 매개변수를 완벽하게 제어하고 유연한 연중무휴 라이브 비디오 워크 플로우 및 이벤트 기반의 라이브 스트림을 전달하며, 업계 표준의 비디오 플레이어 및 다양한 CDN을 지원. 베스트 오브 브리드로 구축 가능

## AWS Elemental MediaPackage

단일 소재로부터 여러 종류의 디바이스에 보안성이 확보된 스트리밍을 내보낼 수 있는 Just In Time Pacakge 서비스.
Input 형식은 Apple HLS 만 가능
Output 형식은 Apple HLS, DASH-ISO, Microsoft Smooth로 지정 가능
워크 플로우의 복잡성을 줄이고 Origin의 가용성을 향상시켜, 인프라 리소스의 과부족 리스크 없이 멀티 스크린 컨텐츠를 보호
다양한 디바이스에 비디오를 효율적으로 전송
HLS, DASH 및 MSS 출력 Just In Time Packaging
Rich Contents 보호 기능

## AWS Elemental MediaConvert

고품질의 비디오 트랜스 코딩과 방송 수준의 기능을 제공하는 비디오 온디맨드 컨텐츠를 간단히 만들 수 있는 파일 베이스의 동영상 변환 서비스.
입력 비디오 볼륨의 변화에 따라 자동으로 확대/축소
Job의 처리 능력과 순서를 관리
