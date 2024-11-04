# javascript-lotto-precourse

## 01. 프로젝트 설명

<aside>

간단한 **로또 발매기**입니다😀

로또를 구입할 **금액**과, **당첨 번호 & 보너스 번호**를 입력하면, **결과**와 **수익률**을 출력합니다.

</aside>

### 기능 요구사항

1. **로또의 금액**
   - 한 장에 1000원이며, **1000원 단위**로 입력해야 합니다. (ERROR)
2. **로또 당첨 번호 & 보너스 번호**
   - 당첨 번호는 6개, 보너스 번호는 1개입니다.
   - 1-45이며, 중복된 숫자는 없어야 합니다. (ERROR)
3. **로또 발행**
   - 로또의 개수대로 중복되지 않은 6개의 숫자를 랜덤으로 발행한다.
   - 6개의 숫자를 오름차순으로 리스트에 담아 출력한다.
4. **당첨 기준과 금액**
   - 1등: 6개 번호 일치 / 2,000,000,000원
   - 2등: 5개 번호 + 보너스 번호 일치 / 30,000,000원
   - 3등: 5개 번호 일치 / 1,500,000원
   - 4등: 4개 번호 일치 / 50,000원
   - 5등: 3개 번호 일치 / 5,000원
5. **수익률**
   - 당첨 금액 / 로또 구입 금액 \* 100하여 출력한다.
6. **실행 결과 예시**

   ```prolog
   구입금액을 입력해 주세요.
   8000

   8개를 구매했습니다.
   [8, 21, 23, 41, 42, 43]
   [3, 5, 11, 16, 32, 38]
   [7, 11, 16, 35, 36, 44]
   [1, 8, 11, 31, 41, 42]
   [13, 14, 16, 38, 42, 45]
   [7, 11, 30, 40, 42, 43]
   [2, 13, 22, 32, 38, 45]
   [1, 3, 5, 14, 22, 45]

   당첨 번호를 입력해 주세요.
   1,2,3,4,5,6

   보너스 번호를 입력해 주세요.
   7

   당첨 통계
   ---
   3개 일치 (5,000원) - 1개
   4개 일치 (50,000원) - 0개
   5개 일치 (1,500,000원) - 0개
   5개 일치, 보너스 볼 일치 (30,000,000원) - 0개
   6개 일치 (2,000,000,000원) - 0개
   총 수익률은 62.5%입니다.
   ```

## 02. 요구사항 분석

### 요구사항 정리

1. **로또의 금액 - lottoPrice**
   - 금액을 입력받는다.
     - 1000원으로 나눠지는지 에러를 체크한다.
2. **로또의 개수 - lottoCount**
   - 1000원으로 나눠 발행할 로또 개수를 알아낸다.
3. **로또 - lotto**

   - 로또 개수만큼 6개의 숫자를 랜덤으로 출력한다.
   - 오름차순으로 리스트에 담는다.
   - 6개의 리스트를 출력한다.

4. **로또 당첨 번호 - targetNumber**
   - 당첨 번호는 6개를 입력받는다. (쉼표로 구분)
   - 쉼표로 구분하여 리스트에 저장한다.
     - 6개로 구분되는지 체크한다.
     - 모두 숫자인지 체크한다.
     - 중복되는 숫자가 있는지 에러를 체크한다.
5. **보너스 번호 - bonusNumber**
   - 보너스번호를 입력받는다.
     - 숫자인지 체크한다.
     - 당첨번호와 중복되는지 체크한다.
6. **당첨 기준 - LOTTO_STANDARD**

```jsx
const LOTTO_STANDARD = [
  { rank: 5, targetCount: 3, prize: 5000 },
  { rank: 4, targetCount: 4, prize: 50000 },
  { rank: 3, targetCount: 5, prize: 1500000 },
  { rank: 2, targetCount: 5, hasBonus: true, prize: 30000000 },
  { rank: 1, targetCount: 6, prize: 2000000000 },
];
```

1. **숫자 일치하는지 계산 - LottoMatcher**

   - 순차적으로 당첨번호와 얼마나 일치하는지 확인한다.
     - 5등: 3개 번호 일치
     - 4등: 4개 번호 일치
     - 3등: 5개 번호 일치
     - 2등: 5개 번호 + 보너스 번호 일치
     - 1등: 6개 번호 일치
   - 랭킹(식별자)을 담음 리스트를 반환한다.
   - 만약 아무 번호도 일치하지 않으면 0을 반환한다.

2. **당첨 결과 - LottoResult**

   ```jsx
   const lottoResult = [
     { rank: 5, count: 0 },
     { rank: 4, count: 0 },
     { rank: 3, count: 0 },
     { rank: 2, count: 0 },
     { rank: 1, count: 0 },
   ];
   ```

   - 랭킹 리스트를 받아 count한다.

3. **로또 출력 형식 - LottoFormatter**

   ```jsx
   const lottoFormat = [
     "3개 일치 (5,000원) - 1개",
     "4개 일치 (50,000원) - 0개",
     "5개 일치 (1,500,000원) - 0개",
     "5개 일치, 보너스 볼 일치 (30,000,000원) - 0개",
     "6개 일치 (2,000,000,000원) - 0개",
   ];
   ```

   - 로또 출력 형식을 저장한다.

4. **로또 수익률** - **ProfitCalculator**

   - 상금을 구한다.
   - 당첨 금액 / 로또 구입 금액 \* 100하여 출력한다.
   - 소수점 둘째 자리에서 반올림한다.
