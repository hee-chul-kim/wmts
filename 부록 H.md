(참고용)  
**의사코드 (Pseudocode)**

이 참고용 부록은 바운딩 박스(Bounding Box) 사각형을 커버하는 타일을 가져오는 방법과, 특정 타일을 경계짓는 좌표 참조 시스템(CRS) 좌표를 가져오는 방법을 설명하는 의사코드를 제공합니다.

---

#### H.1 바운딩 박스(BBOX)에서 타일 인덱스로

다음 의사코드는 주어진 바운딩 박스  
`(bBoxMinX, bBoxMinY, bBoxMaxX, bBoxMaxY)`  
를 CRS 좌표계에서 타일 세트 인덱스 범위로 변환하는 데 사용할 수 있습니다.

이 의사코드는 6.1절에서 사용된 표기법과 동일한 표기법을 사용합니다. 여기서 `bBoxMinX`, `bBoxMinY`, `bBoxMaxX`, `bBoxMaxY`, `tileMatrixMinX`, `tileMatrixMinY`, `tileMatrixMaxY`, `tileSpanX`, `tileSpanY`는 모두 부동소수점 변수(IEEE-754)이며, 표현의 유한 정밀도 때문에 정확도 문제가 발생할 수 있습니다.

이러한 정확도 문제는 `floor()` 함수와 같은 내림 함수에서 ±1 정도의 오차로 증폭될 수 있습니다. 이를 보완하기 위해, 아래의 코드에서는 CRS 좌표 정밀도에 영향을 주지 않는 방식으로 소량의 값 `epsilon`을 더하거나 빼는 방식으로 처리합니다.

```
// 부동소수점 연산의 부정확성을 보정하기 위해
epsilon = 1e-6
tileMinCol = floor((bBoxMinX - tileMatrixMinX) / tileSpanX + epsilon)
tileMaxCol = floor((bBoxMaxX - tileMatrixMinX) / tileSpanX - epsilon)
tileMinRow = floor((tileMatrixMaxY - bBoxMaxY) / tileSpanY + epsilon)
tileMaxRow = floor((tileMatrixMaxY - bBoxMinY) / tileSpanY - epsilon)

// 범위를 벗어나는 타일 요청 방지
if (tileMinCol < 0) tileMinCol = 0
if (tileMaxCol >= matrixWidth) tileMaxCol = matrixWidth - 1
if (tileMinRow < 0) tileMinRow = 0
if (tileMaxRow >= matrixHeight) tileMaxRow = matrixHeight - 1
```

해당 바운딩 박스를 커버하는 모든 타일을 가져오기 위해, 클라이언트는  
`tileMinCol`에서 `tileMaxCol`까지, 그리고  
`tileMinRow`에서 `tileMaxRow`까지 반복하면 됩니다.  
총 요청 타일 수는:  
`(tileMaxCol - tileMinCol + 1) × (tileMaxRow - tileMinRow + 1)` 입니다.

---

#### H.2 타일 인덱스에서 바운딩 박스(BBOX)로

다음 의사코드는 타일 인덱스 `(tileCol, tileRow)` 쌍으로부터 해당 타일의 바운딩 박스 (CRS 좌표계 기준)를 계산하는 방법을 보여줍니다.

**타일의 왼쪽 위 꼭짓점 좌표 (`leftX`, `upperY`)**:

```
leftX = tileCol * tileSpanX + tileMatrixMinX
upperY = tileMatrixMaxY - tileRow * tileSpanY
```

**타일의 오른쪽 아래 꼭짓점 좌표 (`rightX`, `lowerY`)**:

```
rightX = (tileCol + 1) * tileSpanX + tileMatrixMinX
lowerY = tileMatrixMaxY - (tileRow + 1) * tileSpanY
```

---

필요하시면 위 내용을 Mermaid 다이어그램 형식으로 정리해드릴 수도 있습니다.
