---
title: "파이썬 주식 분석 (5) - 크레온 API 이용해서 종목 정보 구하기 (CpUtil.CpCodeMgr, CpSysDib.StockChart)"
categories:
  - INVESTING
tags:
  - 파이썬
  - python
  - CREON API
  - 크레온 API
  - 대신증권
  - CpUtil
  - CpUtil.CpCodeMgr
toc: true
toc_sticky: true
---

## 파이썬 주식 분석 (5) - 크레온 API 이용해서 종목 정보 구하기 (CpUtil.CpCodeMgr, CpSysDib.StockChart)

이번 포스팅에서는 **`대신증권`** 에서 제공되는 크레온 (CREON) API 를 활용해서 종목 정보를 구하는 코드를 소개한다. 구체적으로는 원하는 유형의 종목 정보를 불러온 뒤, 해당 종목의 **`OHLC`** 데이터를 추출하는 코드를 정리해두려고 한다.

>예전에 **`파이썬 증권 데이터 분석`** 책을 보고 이것저것 테스트를 해봤는데, 그때 테스트 했던 코드를 블로그에 제대로 적어두지 않아서 다시 필요한 정보를 찾으려고 하다보니 꽤나 애를 먹고 있다.

**참고:** 제공되는 API 에 대한 정보는 크레온 API (**`대신증권`**) 홈페이지의 **자료실** 혹은 **도움말** 페이지에서 얻을 수 있다.
{: .notice}

### 5.1 종목 리스트 호출: `CpUtil.CpCodeMgr`

아래에서 다루는 파이썬 코드들은 <span style="color:#A03090"><b>크레온 플러스</b></span> 에 로그인 되어 있다는 가정하에 작성된 코드이며, `증권사 API` 를 사용하기 위해서는 **`32-bit`** 환경에서 코드를 실행해야 한다. 추후에 다시 정리하긴 하겠지만, 초기 환경 설정 관련해서는 이전 포스팅에 참고할만한 링크를 정리해두었으니 아래의 링크를 참고하길 바란다.

▶ **[파이썬 주식 분석 (4) - 캔들차트 (candlestick chart) 직접 그리기](https://enidanny.github.io/investing/invest-python-candlestick/#42-ohlc-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%B6%94%EC%B6%9C)** ◀

```python
import win32com.client

objCpCybos = win32com.client.Dispatch("CpUtil.CpCybos")
isConnect = objCpCybos.IsConnect
if (isConnect == 0):
    print("CREON PLUS 접속 오류")
    exit()

objCpCodeMgr = win32com.client.Dispatch("CpUtil.CpCodeMgr")
codeList = objCpCodeMgr.GetStockListByMarket(1) #거래소

print("거래소 종목코드", len(codeList))
for i, code in enumerate(codeList):
    secondCode = objCpCodeMgr.GetStockSectionKind(code)
    name = objCpCodeMgr.CodeToName(code)
    stdPrice = objCpCodeMgr.GetStockStdPrice(code)
    print(i, code, secondCode, stdPrice, name)
```

위 코드는 크레온에서 제공해주는 예제 코드를 기반으로 이루어져 있다.

#### 5.1.1 크레온 접속 확인

다음의 부분은 크레온 플러스에 정상적으로 접속이 되었는지 여부를 확인하는 부분이다. 앞서 언급했듯이, 이번 포스팅에서는 <span style="color:#A0a020"><b>로그인이 되었다고 가정하고</b></span> 있으므로, 사실 전체 코드에서 크게 중요한 부분은 아니다.

```python
import win32com.client

objCpCybos = win32com.client.Dispatch("CpUtil.CpCybos")
isConnect = objCpCybos.IsConnect
if (isConnect == 0):
    print("CREON PLUS 접속 오류")
    exit()
```

#### 5.1.2 거래소 종목 호출

이어서 오는 아래의 코드는 거래소에 상장된 종목 정보를 불러오기 위한 코드이다. 종목 (기업) 이름, 종목 코드, 구분 코드 등의 정보를 불러올 수 있는데, `.GetStockListByMarket(1)` 부분에서 숫자 `1` 은 코스피에 상장된 종목 정보를 불러올 때 사용하면 된다.

>아래 코드에서 objCpCodeMgr.GetStockListByMarket(2) 를 적용할 경우 코스닥 종목 정보를 가져올 수 있다.

```python
objCpCodeMgr = win32com.client.Dispatch("CpUtil.CpCodeMgr")
codeList = objCpCodeMgr.GetStockListByMarket(1) #거래소
```

#### 5.1.3 종목 정보 호출

마지막은 종목 정보를 호출하기 위해 사용된 부분이다. 아래 코드를 보면 알 수 있겠지만 관련 **`API`** 를 이용하면 거래소에 상장된 종목들의 이름 (`e.g. 삼성전자`), 종목 코드 (`e.g. A005930`), 가격 정보 등을 불러올 수도 있다.

```python
print("거래소 종목코드", len(codeList))
for i, code in enumerate(codeList):
    secondCode = objCpCodeMgr.GetStockSectionKind(code)
    name = objCpCodeMgr.CodeToName(code)
    stdPrice = objCpCodeMgr.GetStockStdPrice(code)
    print(i, code, secondCode, stdPrice, name)
```

위 코드에서 개인적으로 관심있는 부분은 **`.GetStockSectionKind(code)`** 부분으로, 필자는 이 부분을 이용해서 거래소에 상장되어 있는 <span style="color:#00a070"><b>ETF 종목</b></span>들을 선별할 예정이다.

>다음의 내용에서 확인할 수 있듯이, **`ETF`** 종목에 대한 GetStockSectionKind(...) 메소드의 반환 값은 **`10`** 이다.

```
value = object.GetStockSectionKind(code)
code 에 해당하는 부구분코드를 반환한다.
code : 주식코드
반환값 : 부구분코드
typedef enum
{
    [helpstring("구분없음")],CPC_KSE_SECTION_KIND_NULL = 0,
    [helpstring("주권")],CPC_KSE_SECTION_KIND_ST = 1,
    [helpstring("투자회사")],CPC_KSE_SECTION_KIND_MF = 2,
    [helpstring("부동산투자회사"],CPC_KSE_SECTION_KIND_RT = 3,
    [helpstring("선박투자회사")],CPC_KSE_SECTION_KIND_SC = 4,
    [helpstring("사회간접자본투융자회사")]CPC_KSE_SECTION_KIND_IF = 5,
    [helpstring("주식예탁증서")],CPC_KSE_SECTION_KIND_DR = 6,
    [helpstring("신수인수권증권")],CPC_KSE_SECTION_KIND_SW = 7,
    [helpstring("신주인수권증서")],CPC_KSE_SECTION_KIND_SR = 8,
    [helpstring("주식워런트증권")],CPC_KSE_SECTION_KIND_ELW = 9,
    [helpstring("상장지수펀드(ETF)")]CPC_KSE_SECTION_KIND_ETF = 10,
    [helpstring("수익증권")],CPC_KSE_SECTION_KIND_BC = 11,
    [helpstring("해외ETF")],CPC_KSE_SECTION_KIND_FETF = 12,
    [helpstring("외국주권")],CPC_KSE_SECTION_KIND_FOREIGN = 13,
    [helpstring("선물")],CPC_KSE_SECTION_KIND_FU = 14,
    [helpstring("옵션")],CPC_KSE_SECTION_KIND_OP = 15,
    [helpstring("KONEX")],CPC_KSE_SECTION_KIND_KN = 16,
    [helpstring("ETN")],CPC_KSE_SECTION_KIND_ETN = 17,
} CPE_KSE_SECTION_KIND;
```

---

### 5.2 OHLC 데이터 호출: `CpSysDib.StockChart`

크레온 플러스에서 특정 종목의 `OHLC` 데이터를 날짜별로 호출할 수 있는 API 가 몇 개 있는 것으로 알고 있는데, 필자는 그 중에서 **`win32com.client.Dispatch('CpSysDib.StockChart')`** 메소드를 활용해 원하는 종목의 `OHLC` 데이터를 호출하였다.

>중복된 기능을 제공하는 `API` 마다 어떠한 차이가 있는지는 잘 모르겠으나, 일단 이번 포스팅에서는 **`CpSysDib.StockChart`** 모듈을 활용해 `OHLC` 데이터를 호출한다.

다음은 위의 **[▲ 5.1](https://enidanny.github.io/investing/invest-creon-api-python/#51-%EC%A2%85%EB%AA%A9-%EB%A6%AC%EC%8A%A4%ED%8A%B8-%ED%98%B8%EC%B6%9C-cputilcpcodemgr)** 에서 활용한 종목 정보 호출 메소드와 함께 `ETF` 종목의 OHLC 데이터를 불러와 데이터프레임으로 저장하는 코드이다. 간단한 예제 형태로 작성하였으니, 참고할 부분이 있다면 적당히 긁어서 쓰면 될 것 같다.

```python
import win32com.client
import pandas as pd
import time

objCpCybos = win32com.client.Dispatch("CpUtil.CpCybos")
isConnect = objCpCybos.IsConnect
if (isConnect == 0):
    print("CREON PLUS 접속 오류")
    exit()

objCpCodeMgr = win32com.client.Dispatch("CpUtil.CpCodeMgr")
codeList = objCpCodeMgr.GetStockListByMarket(1)

# ETF 종목 코드 저장
etfList = []
for i, code in enumerate(codeList):
    if objCpCodeMgr.GetStockSectionKind(code) == 10:
        if 'KODEX' in objCpCodeMgr.CodeToName(code):
            etfList.append(code)
        elif 'TIGER' in objCpCodeMgr.CodeToName(code):
            etfList.append(code)

cpOhlc = win32com.client.Dispatch('CpSysDib.StockChart')

def get_Chart_DataFrame(code, qty):
    # [1] Set Input Value ----------
    cpOhlc.SetInputValue(0, code)               # 0: 종목 코드
    cpOhlc.SetInputValue(1, ord('2'))           # ord(*). * = 1: 기간, 2: 개수
    cpOhlc.SetInputValue(4, qty)                # 4: 호출 데이터 개수
    cpOhlc.SetInputValue(5, [0, 2, 3, 4, 5])    # 0: 날짜
    cpOhlc.SetInputValue(6, ord('D'))           # 0: 날짜, 2~5: OHLC 값
    cpOhlc.SetInputValue(9, ord('1'))           # 1:수정(modified) 주가

    # [2] Request ----------
    cpOhlc.BlockRequest()

    # [3] Get Input Value ----------
    cnt = cpOhlc.GetHeaderValue(3)  # check received data (might be same with "qty")
    m_column = ['date', 'o','h','l','c']    # open, high, low, close
    m_row =[]
    for j in range(cnt):
        # m_index.append()
        m_row.append([cpOhlc.GetDataValue(0, j),
                    cpOhlc.GetDataValue(1, j), 
                    cpOhlc.GetDataValue(2, j),
                    cpOhlc.GetDataValue(3, j),
                    cpOhlc.GetDataValue(4, j)])
    res = pd.DataFrame(m_row, columns = m_column)
    return res

if __name__ == '__main__':
    try:
        for etf in etfList:    
            df = get_Chart_DataFrame(etf, 100)
            df = df.sort_values('date', ascending=True)
            df.index = list(range(0,len(df)))
            filename = 'C:/ju-gong/etf_list/'+etf+'.csv' 
            df.to_csv(filename)
            time.sleep(0.2)
    except:
        pass
```

#### 5.2.1 ETF 종목 코드 저장

위의 전체 코드 중 다음의 코드는 ETF 종목들의 코드만 분류해서 리스트로 저장하는 코드이다. 별도의 필터링 없이 **`objCpCodeMgr.GetStockSectionKind(code)`** 값이 `10` 에 해당하는 종목 코드 (`ETF`)를 선별하는 경우 너무 많으므로 (`678개`), `ETF` 이름에 `KODEX` 나 `TIGER` 가 포함된 종목만을 선별하였다.

>이와 같이 선별한 이유는 딱히 없으며, 이렇게 하면 `300여개` 의 종목 코드를 얻을 수 있다.

```python
# ETF 종목 코드 저장
etfList = []
for i, code in enumerate(codeList):
    if objCpCodeMgr.GetStockSectionKind(code) == 10:
        if 'KODEX' in objCpCodeMgr.CodeToName(code):
            etfList.append(code)
        elif 'TIGER' in objCpCodeMgr.CodeToName(code):
            etfList.append(code)
```

#### 5.2.2 OHLC 데이터 저장

위의 5.2.1 에서 소개한 코드 이후에 이어지는 부분은 선별한 ETF 종목들의 OHLC 데이터를 개별 데이터프레임으로 정리한 후에 `.csv` 파일로 저장하는 코드에 해당한다. 

```python
cpOhlc = win32com.client.Dispatch('CpSysDib.StockChart')
...

if __name__ == '__main__':
    try:
        for etf in etfList:    
            df = get_Chart_DataFrame(etf, 100)
            df = df.sort_values('date', ascending=True)
            df.index = list(range(0,len(df)))
            filename = 'C:/ju-gong/etf_list/'+etf+'.csv' 
            df.to_csv(filename)
            time.sleep(0.2)
    except:
        pass
```

여기서 사용된 `get_Chart_DataFrame(...)` 함수는 필자가 임의로 정의한 함수이며, **`종목 코드`** 와 **`데이터 개수`** 를 인자로 받는다. **`데이터 개수 (qty)`** 는 가장 최근 날짜를 기준으로 이전 데이터를 **몇 개까지** 불러올 것인지를 설정하는 인자이며, 해당 개수보다 실제 데이터가 적을 경우에는 최대로 불러올 수 있는 만큼만 불러온다.

>`get_Chart_DataFrame(...)` 함수는 전체 예제 코드에 정의된 부분을 참고할 것.

그 외에 몇 가지 부연설명을 하자면,

* 위 예제 코드에서 파일 이름에 경로 (`path`) 정보를 포함시켜두기는 했지만, 이 부분은 필요에 따라 배제해도 된다.
* `time.sleep(0.2)` 를 넣은 이유는, 너무 잦은 빈도로 증권사 정보를 호출하는 경우 `증권사 API` (?) 로부터 (*`현재 등급으로는 너무 많은 거래정보를 단시간내에 불러올 수 없다는 내용의`*) 알람 메시지가 호출될 수 있어서 이를 방지하기 위해 추가하였다 (~~그래도 뜬다~~).

위 코드를 적용하면 선별된 **`ETF 종목별 (최대 100일 치의) OHLC`** 데이터를 `.csv` 파일로 저장할 수 있으며, 개별 파일들은 아래의 그림과 같은 형태로 저장된다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/invest-creon1-fig1.png" alt="">
</figure>

>물론, 이렇게 개별 파일로 저장하는 것은 다소 비효율적인 방식이긴한데, 대충 저장해두고 나중에 데이터프레임 형태 그대로 불러서 사용하기에는 이게 편해서 그냥 예제 코드 작성한다는 느낌으로 이렇게 정리했다.🙃

<!---
<span style="color:#A0a020"><b></b></span>
--->