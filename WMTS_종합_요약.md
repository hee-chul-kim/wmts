# WMTS (Web Map Tile Service) 표준 종합 요약

## 1. 개요

WMTS(Web Map Tile Service)는 사전 정의된 이미지 타일을 사용하여 디지털 지도를 제공하는 표준 기반 솔루션입니다. 이 표준은 성능과 확장성에 중점을 두고 설계되었습니다.

### 1.1 WMS와의 차이점

#### WMS (Web Map Service)

- 유연한 요청 방식 사용
- 임의의 지리적 범위, 축척, 좌표계 지정 가능
- 서버 부하가 크고 캐시 활용이 어려움

#### WMTS의 장점

- 이미지 오버레이를 클라이언트가 수행
- 사전 정의된 고정 이미지 타일 사용
- 서버 확장성 향상
- 효율적인 캐시 활용 가능
- 네트워크 지연 시간 감소 및 대역폭 절감

## 2. 아키텍처

WMTS는 두 가지 아키텍처 스타일을 지원합니다:

### 2.1 절차 지향적 아키텍처

- GetCapabilities (필수)
- GetTile (필수)
- GetFeatureInfo (선택)
- KVP, XML, SOAP 인코딩 지원

### 2.2 리소스 지향적 아키텍처 (RESTful)

- ServiceMetadata 리소스
- Tile 리소스
- FeatureInfo 리소스
- URL 템플릿 기반 접근

## 3. 핵심 구성 요소

### 3.1 ServiceMetadata 문서

1. ServiceIdentification - 서버 정보
2. ServiceProvider - 운영 조직 정보
3. OperationsMetadata - 작업 메타데이터
4. Contents - 제공 데이터 정보
5. Themes - 레이어 주제별 구성 (선택)

### 3.2 타일 매트릭스 세트

- 축척 (Scale Denominator)
- 타일의 픽셀 크기
- 좌상단 좌표
- 타일 매트릭스 크기
- 좌표 참조 체계(CRS) 정보

### 3.3 레이어 구성

- 식별자, 제목, 설명
- 지원 포맷
- 경계 상자
- 스타일 정보
- TileMatrixSet 참조
- 차원 정보 (선택)

## 4. 주요 기능

### 4.1 타일 서비스

- 직사각형 이미지 형태
- 완전한 단일 타일 전체 반환
- 배경 투명 처리 지원
- 사전 정의된 포맷으로 제공

### 4.2 FeatureInfo 서비스

- 타일의 특정 픽셀 위치 정보 제공
- 지형지물의 주제 속성 포함
- GML Simple Features Profile 지원
- HTML 형식 지원 권장

## 5. 성능 최적화

### 5.1 캐시 전략

- HTTP 1.0: Expires 헤더
- HTTP 1.1: Cache-Control 헤더
- 정적 파일 시스템 활용
- 중간 캐시 활용 가능

### 5.2 권장 사항

- 공통 TileMatrixSet 사용
- 표준 스케일 세트 활용
- 제한된 포맷 목록 사용
- 효율적인 캐시 메커니즘 구현

## 6. 구현 고려사항

### 6.1 서버 구현

- 미리 렌더링된 타일 저장 가능
- 동적 타일 생성 및 캐싱 가능
- 비동기적 타일 접근 지원
- 표준화된 에러 처리

### 6.2 클라이언트 구현

- 여러 타일 동시 요청 처리
- 타일 오버레이 및 모자이크 처리
- 다중 레이어 지원
- 캐시 활용 최적화

## 7. 상호운용성

### 7.1 필수 지원 사항

- 클라이언트: KVP와 RESTful 인터페이스
- 서버: KVP 또는 RESTful 중 하나 이상

### 7.2 권장 이미지 포맷

- PNG: 투명도 필요 시
- JPEG: 이미지 기반 지도

## 8. 보안 및 확장성

### 8.1 보안 고려사항

- API 키 관리
- 접근 제어
- 요청 제한

### 8.2 확장성 지원

- 다중 스케일 레벨
- 다양한 좌표계
- 사용자 정의 차원
- 테마 기반 구성

## 9. 타일 매트릭스 세트 상세

### 9.1 기본 개념

- 타일로 구성된 지도 레이어의 공간 표현을 정의
- 이산적(discrete) 파라미터로 제한된 공간 구조
- 각 좌표 참조계(CRS)별로 사용 가능한 타일 정의
- 하나 이상의 타일 매트릭스로 구성

### 9.2 타일 매트릭스 구성 요소

#### 9.2.1 축척 (Scale Denominator)

- 기준 픽셀 크기: 0.28mm × 0.28mm
- WMS 1.3.0 및 Symbology Encoding 1.1.0과 동일한 정의
- 실제 디스플레이 픽셀 크기와 독립적

#### 9.2.2 타일 정의

- 타일의 픽셀 단위 너비와 높이
- 비정방형 픽셀(non-square pixels) 지원 안 함
- 완전한 단일 타일 형태로 제공

#### 9.2.3 공간 참조

- 타일 매트릭스의 좌상단 좌표 (TopLeftCorner)
- 좌표 참조계(CRS) 기준의 위치
- 가장 왼쪽 상단 타일의 가장 왼쪽 상단 픽셀 좌표

#### 9.2.4 매트릭스 크기

- 타일 매트릭스의 가로 × 세로 타일 수
- matrixWidth × matrixHeight로 표현
- 전체 타일 수 계산 가능

### 9.3 타일 인덱싱

#### 9.3.1 기본 구조

- TileCol(열)과 TileRow(행) 인덱스 사용
- 원점 (0,0)은 좌측 상단
- 열은 오른쪽으로, 행은 아래쪽으로 증가

#### 9.3.2 경계 상자 계산

```
pixelSpan = scaleDenominator × 0.28 × 10⁻³ / metersPerUnit
tileSpanX = tileWidth × pixelSpan
tileSpanY = tileHeight × pixelSpan
tileMatrixMaxX = tileMatrixMinX + (tileSpanX × matrixWidth)
tileMatrixMinY = tileMatrixMaxY - (tileSpanY × matrixHeight)
```

### 9.4 타일 매트릭스 세트 제한 (TileMatrixSetLimits)

#### 9.4.1 목적

- 레이어의 실제 데이터 영역 정의
- 불필요한 타일 요청 방지
- 캐시 효율성 향상

#### 9.4.2 구성 요소

- minTileRow, maxTileRow: 유효한 타일 행 범위
- minTileCol, maxTileCol: 유효한 타일 열 범위
- 각 타일 매트릭스별로 정의 가능

### 9.5 Well-known Scale Sets

#### 9.5.1 특징

- 미리 정의된 표준 축척 집합
- 서버 간 상호운용성 향상
- 클라이언트 호환성 검증 용이

#### 9.5.2 장점

- 다른 WMTS 서버와의 호환성 향상
- 클라이언트의 빠른 호환성 확인 가능
- 여러 서비스의 조합(mash-up) 용이

#### 9.5.3 구현 고려사항

- 공통 좌표계 사용
- 표준화된 스케일 레벨
- 일관된 타일 크기
- 정렬된 타일 경계
