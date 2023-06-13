---
title: "nRF5 SDK (15) - nRF52840-DK SAADC 제어"
categories:
  - nRF5 SDK
tags:
  - nRF5SDK
  - SAADC  
  - AC voltage
  - IoT
  - 교류 전압
toc: true
toc_sticky: true
---

## 15. nRF52840-DK SAADC 제어

이번 포스팅에서는 **`nRF5 SDK`** 에서 제공되는 예제 중 <span style="color:#AA50A0"><b>SAADC</b></span> 관련 예제를 간단하게 살펴보고자 한다.

### 15.1 SAADC in nRF52840 SoC

현재 필자는 펌웨어 개발을 주로 하고 있어서 하드웨어 분야에 대해서는 잘 모르지만, ADC 설계는 `파이프라인, 델타-시그마` 등 어느 정도 용도에 따라 규격화된 몇몇 구조가 있는 듯 하다. 그 중 `nRF52` 시리즈에는 **`Successive approximation(SA)`** 구조로 설계된 8-channel ADC 가 내장되어 있다. (*차동모드 적용시 4-channel*)

주요 스펙은 다음과 같으며, 보다 자세한 특성은 데이터 시트를 참고하면 된다.

* Maximum sampling rate: `200 kHz`
* Resolution: `8, 10, 12, 14 bits`
* Oversampling: `0, 2 ~ 256x`

---

### 15.2 SAADC example

**`nRF5 SDK`** 는 대부분의 주변기기 활용 방법에 대한 소스코드를 **...examples/peripherals** 폴더 내에서 제공해주고 있다.

>참고로 해당 폴더에 있는 예제 프로젝트들은 `SoftDevice` 를 사용하지 않으므로, **`BLE`** 통신을 활성화 하는 프로젝트의 소스 코드와는 다소 차이가 있을 수는 있지만, **`nRF5 SDK`** 에서 제공되는 주변기기 라이브러리를 `(e.g. UART, TWI, SPI, SAADC, GPIOTE...)`를 어떻게 제어하면 되는지 대략적으로 참고할 수 있다.

<span style="color:#AA50A0"><b>SAADC</b></span> 의 경우는 아래의 폴더에 있는 프로젝트에 그 활용 예가 정리되어 있다.

* **nRF5 SDK 버전: `17.1.0`**

📁 **`nRF5_SDK.../examples/peripheral/saadc`** 

다음은 위 프로젝트의 소스 코드의 **`main`** 함수 부분이다.

<div class="colorscripter-code" style="color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important; position:relative !important;overflow:auto"><table class="colorscripter-code-table" style="margin:0;padding:0;border:none;background-color:#fafafa;border-radius:4px;" cellspacing="0" cellpadding="0"><tr><td style="padding:6px;border-right:2px solid #e5e5e5"><div style="margin:0;padding:0;word-break:normal;text-align:right;color:#666;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="line-height:130%">1</div><div style="line-height:130%">2</div><div style="line-height:130%">3</div><div style="line-height:130%">4</div><div style="line-height:130%">5</div><div style="line-height:130%">6</div><div style="line-height:130%">7</div><div style="line-height:130%">8</div><div style="line-height:130%">9</div><div style="line-height:130%">10</div><div style="line-height:130%">11</div><div style="line-height:130%">12</div><div style="line-height:130%">13</div><div style="line-height:130%">14</div><div style="line-height:130%">15</div><div style="line-height:130%">16</div><div style="line-height:130%">17</div><div style="line-height:130%">18</div><div style="line-height:130%">19</div><div style="line-height:130%">20</div><div style="line-height:130%">21</div><div style="line-height:130%">22</div><div style="line-height:130%">23</div><div style="line-height:130%">24</div></div></td><td style="padding:6px 0;text-align:left"><div style="margin:0;padding:0;color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#999999">/**</span></div><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#999999">&nbsp;*&nbsp;@brief&nbsp;Function&nbsp;for&nbsp;main&nbsp;application&nbsp;entry.</span></div><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#999999">&nbsp;*/</span></div><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#066de2">int</span>&nbsp;main(<span style="color:#a71d5d">void</span>)</div><div style="padding:0 6px; white-space:pre; line-height:130%">{</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;uint32_t&nbsp;err_code&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">=</span>&nbsp;NRF_LOG_INIT(<span style="color:#0086b3">NULL</span>);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;APP_ERROR_CHECK(err_code);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;NRF_LOG_DEFAULT_BACKENDS_INIT();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;ret_code_t&nbsp;ret_code&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">=</span>&nbsp;nrf_pwr_mgmt_init();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;APP_ERROR_CHECK(ret_code);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;saadc_init();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;saadc_sampling_event_init();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;saadc_sampling_event_enable();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;NRF_LOG_INFO(<span style="color:#63a35c">"SAADC&nbsp;HAL&nbsp;simple&nbsp;example&nbsp;started."</span>);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#a71d5d">while</span>&nbsp;(<span style="color:#0099cc">1</span>)</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;{</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nrf_pwr_mgmt_run();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;NRF_LOG_FLUSH();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;}</div><div style="padding:0 6px; white-space:pre; line-height:130%">}</div></div><div style="text-align:right;margin-top:-13px;margin-right:5px;font-size:9px;font-style:italic"><a href="http://colorscripter.com/info#e" target="_blank" style="color:#e5e5e5text-decoration:none">Colored by Color Scripter</a></div></td><td style="vertical-align:bottom;padding:0 2px 4px 0"><a href="http://colorscripter.com/info#e" target="_blank" style="text-decoration:none;color:white"><span style="font-size:9px;word-break:normal;background-color:#e5e5e5;color:white;border-radius:10px;padding:1px">cs</span></a></td></tr></table></div>

<a></a>

위 소스 코드 중 핵심이 되는 부분은 다음의 세 줄이다.

<div class="colorscripter-code" style="color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important; position:relative !important;overflow:auto"><table class="colorscripter-code-table" style="margin:0;padding:0;border:none;background-color:#fafafa;border-radius:4px;" cellspacing="0" cellpadding="0"><tr><td style="padding:6px;border-right:2px solid #e5e5e5"><div style="margin:0;padding:0;word-break:normal;text-align:right;color:#666;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="line-height:130%">1</div><div style="line-height:130%">2</div><div style="line-height:130%">3</div><div style="line-height:130%">4</div><div style="line-height:130%">5</div><div style="line-height:130%">6</div><div style="line-height:130%">7</div><div style="line-height:130%">8</div><div style="line-height:130%">9</div><div style="line-height:130%">10</div><div style="line-height:130%">11</div></div></td><td style="padding:6px 0;text-align:left"><div style="margin:0;padding:0;color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#999999">/**</span></div><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#999999">&nbsp;*&nbsp;@brief&nbsp;Function&nbsp;for&nbsp;main&nbsp;application&nbsp;entry.</span></div><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#999999">&nbsp;*/</span></div><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#066de2">int</span>&nbsp;main(<span style="color:#a71d5d">void</span>)</div><div style="padding:0 6px; white-space:pre; line-height:130%">{</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;<span style="color:#999999">//...</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;saadc_init();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;saadc_sampling_event_init();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;saadc_sampling_event_enable();</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;<span style="color:#999999">//...</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">}</div></div><div style="text-align:right;margin-top:-13px;margin-right:5px;font-size:9px;font-style:italic"><a href="http://colorscripter.com/info#e" target="_blank" style="color:#e5e5e5text-decoration:none">Colored by Color Scripter</a></div></td><td style="vertical-align:bottom;padding:0 2px 4px 0"><a href="http://colorscripter.com/info#e" target="_blank" style="text-decoration:none;color:white"><span style="font-size:9px;word-break:normal;background-color:#e5e5e5;color:white;border-radius:10px;padding:1px">cs</span></a></td></tr></table></div>

<a></a>

---

### 15.3 SAADC example 소스코드 분석

#### 15.3.1 saadc_init();

먼저, `saadc_init` 함수 내에서는 saadc 모듈을 초기화 하는 함수를 호출하며, 이때 ADC 의 기본적인 채널 설정 값을 입력하도록 되어있다.

>SE (Single Ended) 모드로 동작시킬지, Differential 모드로 동작시킬지 또한, 어떤 핀을 활성화 할 것인지 등을 선택할 수 있으며, 아래 예제 코드에서는 제공되고 있지 않으나, `nrf_saadc_channel_config_t` 타입의 변수를 이용해서 인터럽트의 우선순위나, ADC 해상도 (`resolution`), 오버 샘플링 기능등을 설정할 수도 있다.

<div class="colorscripter-code" style="color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important; position:relative !important;overflow:auto"><table class="colorscripter-code-table" style="margin:0;padding:0;border:none;background-color:#fafafa;border-radius:4px;" cellspacing="0" cellpadding="0"><tr><td style="padding:6px;border-right:2px solid #e5e5e5"><div style="margin:0;padding:0;word-break:normal;text-align:right;color:#666;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="line-height:130%">1</div><div style="line-height:130%">2</div><div style="line-height:130%">3</div><div style="line-height:130%">4</div><div style="line-height:130%">5</div><div style="line-height:130%">6</div><div style="line-height:130%">7</div><div style="line-height:130%">8</div><div style="line-height:130%">9</div><div style="line-height:130%">10</div><div style="line-height:130%">11</div><div style="line-height:130%">12</div><div style="line-height:130%">13</div><div style="line-height:130%">14</div><div style="line-height:130%">15</div><div style="line-height:130%">16</div><div style="line-height:130%">17</div><div style="line-height:130%">18</div><div style="line-height:130%">19</div></div></td><td style="padding:6px 0;text-align:left"><div style="margin:0;padding:0;color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#a71d5d">void</span>&nbsp;saadc_init(<span style="color:#a71d5d">void</span>)</div><div style="padding:0 6px; white-space:pre; line-height:130%">{</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;ret_code_t&nbsp;err_code;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;nrf_saadc_channel_config_t&nbsp;channel_config&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">=</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;NRF_DRV_SAADC_DEFAULT_CHANNEL_CONFIG_SE(NRF_SAADC_INPUT_AIN0);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#999999">//NRF_SAADC_INPUT_AIN0</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;err_code&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">=</span>&nbsp;nrf_drv_saadc_init(<span style="color:#0086b3">NULL</span>,&nbsp;saadc_callback);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;APP_ERROR_CHECK(err_code);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;err_code&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">=</span>&nbsp;nrf_drv_saadc_channel_init(<span style="color:#0099cc">0</span>,&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">&amp;</span>channel_config);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;APP_ERROR_CHECK(err_code);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;err_code&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">=</span>&nbsp;nrf_drv_saadc_buffer_convert(m_buffer_pool[<span style="color:#0099cc">0</span>],&nbsp;SAMPLES_IN_BUFFER);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;APP_ERROR_CHECK(err_code);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;err_code&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">=</span>&nbsp;nrf_drv_saadc_buffer_convert(m_buffer_pool[<span style="color:#0099cc">1</span>],&nbsp;SAMPLES_IN_BUFFER);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;APP_ERROR_CHECK(err_code);</div><div style="padding:0 6px; white-space:pre; line-height:130%">}</div></div><div style="text-align:right;margin-top:-13px;margin-right:5px;font-size:9px;font-style:italic"><a href="http://colorscripter.com/info#e" target="_blank" style="color:#e5e5e5text-decoration:none">Colored by Color Scripter</a></div></td><td style="vertical-align:bottom;padding:0 2px 4px 0"><a href="http://colorscripter.com/info#e" target="_blank" style="text-decoration:none;color:white"><span style="font-size:9px;word-break:normal;background-color:#e5e5e5;color:white;border-radius:10px;padding:1px">cs</span></a></td></tr></table></div>

<a></a>

다음의 그림은 필자가 별도로 구성한 예제 프로그램 내의 소스 코드 중 하나 이상의 ADC 채널을 초기화 하는 부분의 코드이다. 약간 수정된 부분은 있지만, 기본적인 구조는 하나의 ADC 채널을 초기화 하는 로직과 유사한 것을 볼 수 있을 것이다

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/sdk-adc-fig1.png" alt="">
</figure>

>참고로 ADC `init()` 함수 마지막 부분을 보면 `nrf_drv_saadc_buffer_convert(,)` 함수를 호출하는 것을 볼 수 있다. `nRF5 SDK` 예제에서 이를 두 번 호출하는 이유에 대해서는 `Double Buffering` 기능을 활성화하여 데이터 누락이 없도록 하기 위함이라 하는데, 정확히 맞는 설명인지는 모르겠다. (하나 이상의 ADC 초기화 할 때는 `Double Buffering` 컨셉이 제대로 적용되지 않았던 것 같다😴)

---

#### 15.3.2 saadc_sampling_event_init();

본 예제에서는 `PPI` 기능을 활용해서 ADC 를 제어하는 방법을 보여주고 있으며, `saadc_sampling_event_init` 함수에서는 ADC 초기화 이후 `PPI` 기능을 이용해 `ADC` 와 `Timer` 를 연동시키는 작업을 수행한다.

`PPI` 는 노르딕 칩에서 제공되는 기능으로 `CPU` 개입 없이 내부 모듈 사이의 **`event`** 와 **`task`** 를 연동하기 위해 사용된다. **보다 자세한 내용은 사용하고자 하는 노르딕 칩의 데이터 시트를 확인하자.**

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/sdk-adc-fig2.png" alt="">
</figure>

>가령, 본 예제에서처럼 `Timer` 와 `ADC` 사이의 동작을 연동시키면, `CPU` 개입 없이  **`Timer` 의 카운터 값이 설정값에 도달했을 때** 라는 **`event`** 가 발생하면, **`ADC` 전압을 측정해라** 라는  **`task`** 를 자동으로 수행하게 할 수 있다.

예제의 소스 코드를 보면 400 ms 마다 `Timer` 틱 (tick)이 발생하도록 설정한 뒤에, 해당 틱이 발생했을 때 (**`event`**), `ADC` 샘플링을 수행하라고 **`task`** 를 설정해주는 함수가 작성되어있는 것을 볼 수 있을 것이다.

`ADC` 연동이 마무리 되면, `saadc_sampling_event_enable()` 함수를 호출해서 해당 기능을 활성화 하는 것으로 샘플링을 시작하게 된다.

---

### 15.3.4 활용 예시

앞서 살펴본 예제에서는 `PPI` 기능을 활용해 `ADC` 를 일정 시간마다 제어하고 있으나, `ADC` 초기화 이후에 `nrf_drv_saadc_sample();` 함수를 호출하는 것으로 원하는 시점에 샘플링을 수행할 수도 있다.

또한, 위 예제에서의 틱 값을 조절하면 `CPU` 개입 없이도 고속으로 `ADC` 채널의 전압을 샘플링 할 수 있는데 (물론 충분한 버퍼를 할당해두어야할 것이다), 이를 이용하면 일정 주파수 범위 내의 교류 전압도 측정해볼 수 있을 것이다.

>국내에서 일반 가정에 보급되는 교류의 주파수는 `60 Hz` 이므로 초당 120 개 이상의 샘플을 측정해서 저장하면 원본 파형을 복원할 수 있을 것이다. 실제로 테스트 해보면, 초당 `1000` 개의 샘플 측정 시 (측정 간격을 `1 ms` 로 설정) 별도의 후속처리 없이도 원본 파형에 가까운 그래프가 얻어짐을 확인해볼 수 있었다

* 소스코드 적용 예시

<div class="colorscripter-code" style="color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important; position:relative !important;overflow:auto"><table class="colorscripter-code-table" style="margin:0;padding:0;border:none;background-color:#fafafa;border-radius:4px;" cellspacing="0" cellpadding="0"><tr><td style="padding:6px;border-right:2px solid #e5e5e5"><div style="margin:0;padding:0;word-break:normal;text-align:right;color:#666;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="line-height:130%">1</div><div style="line-height:130%">2</div><div style="line-height:130%">3</div><div style="line-height:130%">4</div><div style="line-height:130%">5</div><div style="line-height:130%">6</div><div style="line-height:130%">7</div><div style="line-height:130%">8</div><div style="line-height:130%">9</div><div style="line-height:130%">10</div><div style="line-height:130%">11</div><div style="line-height:130%">12</div><div style="line-height:130%">13</div><div style="line-height:130%">14</div><div style="line-height:130%">15</div><div style="line-height:130%">16</div><div style="line-height:130%">17</div><div style="line-height:130%">18</div></div></td><td style="padding:6px 0;text-align:left"><div style="margin:0;padding:0;color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#a71d5d">void</span>&nbsp;saadc_sampling_event_init(<span style="color:#a71d5d">void</span>)</div><div style="padding:0 6px; white-space:pre; line-height:130%">{</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;ret_code_t&nbsp;err_code;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#999999">//&nbsp;...</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#999999">/*&nbsp;setup&nbsp;m_timer&nbsp;for&nbsp;compare&nbsp;event&nbsp;every&nbsp;400ms&nbsp;*/</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;uint32_t&nbsp;ticks&nbsp;<span style="color:#ff3399"></span><span style="color:#a71d5d">=</span>&nbsp;nrf_drv_timer_ms_to_ticks(<span style="color:#ff3399"></span><span style="color:#a71d5d">&amp;</span>m_timer,&nbsp;<span style="color:#0099cc">1</span>);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;nrf_drv_timer_extended_compare(<span style="color:#ff3399"></span><span style="color:#a71d5d">&amp;</span>m_timer,</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;NRF_TIMER_CC_CHANNEL0,</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ticks,</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;NRF_TIMER_SHORT_COMPARE0_CLEAR_MASK,</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#a71d5d">false</span>);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;nrf_drv_timer_enable(<span style="color:#ff3399"></span><span style="color:#a71d5d">&amp;</span>m_timer);</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#999999">//&nbsp;...</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">}</div></div><div style="text-align:right;margin-top:-13px;margin-right:5px;font-size:9px;font-style:italic"><a href="http://colorscripter.com/info#e" target="_blank" style="color:#e5e5e5text-decoration:none">Colored by Color Scripter</a></div></td><td style="vertical-align:bottom;padding:0 2px 4px 0"><a href="http://colorscripter.com/info#e" target="_blank" style="text-decoration:none;color:white"><span style="font-size:9px;word-break:normal;background-color:#e5e5e5;color:white;border-radius:10px;padding:1px">cs</span></a></td></tr></table></div>

<a></a>

<!---
<span style="color:#A0a020"><b></b></span>
--->