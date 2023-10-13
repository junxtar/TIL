## 주석

- 코드의 설명을 달아놓는 기능입니다.
  - 개발자들끼리 협업할때 코드에 대한 설명을 추가하거나 주의사항을 적어놓을 때 사용합니다.
  - 실제 프로그램 실행에는 영향을 미치지 않습니다.

## 주석에 대한 견해

> 참고 블로그에 있는 글이 너무 인상 깊어서 작성하게 되었습니다.

소스 코드로 우리의 의도를 표현하지 못해서, 실패를 만회하기 위해 주석을 사용합니다.
프로그래밍 언어를 치밀하게 사용하여 의도를 표현할 능력이 있다면, 주석은 거의 필요하지 않습니다.
그러므로 주석이 필요한 상황에 처한다면 "이 주석문이 과연 정말 필요할까?" 라는 물음을 자신에게 던져
한번 더 생각해보는 시간을 가지면 좋겠습니다.

## 좋은 주석

### 법적인 주석

- 몇몇 회사는 법적인 이유로 특정 주석을 넣으라 명시하는데 소스 첫머리에 저작권 & 소유권 정보를 주석으로 남긴다.

  <div class="colorscripter-code" style="color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important; position:relative !important;overflow:auto"><table class="colorscripter-code-table" style="margin:0;padding:0;border:none;background-color:#fafafa;border-radius:4px;" cellspacing="0" cellpadding="0"><tr><td style="padding:6px;border-right:2px solid #e5e5e5"><div style="margin:0;padding:0;word-break:normal;text-align:right;color:#666;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="line-height:130%">1</div><div style="line-height:130%">2</div></div></td><td style="padding:6px 0;text-align:left"><div style="margin:0;padding:0;color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#010101"></span><span style="color:#0099cc">/</span><span style="color:#010101"></span><span style="color:#0099cc">/</span>&nbsp;Copyright&nbsp;(C)&nbsp;<span style="color:#004fc8">2003</span>&nbsp;,<span style="color:#004fc8">2004</span>&nbsp;,<span style="color:#004fc8">2005</span>&nbsp;<span style="color:#ff3399">by</span>&nbsp;Object&nbsp;Mentor,&nbsp;Inc.&nbsp;All&nbsp;rights&nbsp;reserved.</div><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#010101"></span><span style="color:#0099cc">/</span><span style="color:#010101"></span><span style="color:#0099cc">/</span>&nbsp;GNU&nbsp;General&nbsp;Public&nbsp;License&nbsp;버전&nbsp;<span style="color:#004fc8">2</span>&nbsp;이상을&nbsp;따르는&nbsp;조건으로&nbsp;배포한다.</div></div></td><td style="vertical-align:bottom;padding:0 2px 4px 0"><a href="http://colorscripter.com/info#e" target="_blank" style="text-decoration:none;color:white"><span style="font-size:9px;word-break:normal;background-color:#e5e5e5;color:white;border-radius:10px;padding:1px">cs</span></a></td></tr></table></div>

### 정보를 제공하는 주석

- 밑에서 제시하는 코드에서는 정규식을 활용한 문자열 변환하는 과정이다. 정규식에 대한 정보를 주석으로 제공합니다.

<div class="colorscripter-code" style="color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important; position:relative !important;overflow:auto"><table class="colorscripter-code-table" style="margin:0;padding:0;border:none;background-color:#fafafa;border-radius:4px;" cellspacing="0" cellpadding="0"><tr><td style="padding:6px;border-right:2px solid #e5e5e5"><div style="margin:0;padding:0;word-break:normal;text-align:right;color:#666;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="line-height:130%">1</div><div style="line-height:130%">2</div></div></td><td style="padding:6px 0;text-align:left"><div style="margin:0;padding:0;color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="padding:0 6px; white-space:pre; line-height:130%">String&nbsp;str&nbsp;=&nbsp;"a1b2c3d4";&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">str&nbsp;=&nbsp;str.replaceAll("[0-9]",&nbsp;"");&nbsp;//문자열에&nbsp;포함한&nbsp;숫자를&nbsp;제거하는&nbsp;정규식</div></div></td><td style="vertical-align:bottom;padding:0 2px 4px 0"><a href="http://colorscripter.com/info#e" target="_blank" style="text-decoration:none;color:white"><span style="font-size:9px;word-break:normal;background-color:#e5e5e5;color:white;border-radius:10px;padding:1px">cs</span></a></td></tr></table></div>

### 의도를 설명하는 주석

[참고자료](https://velog.io/@hangem422/clean-code-comment)
[참고자료](https://danpatpang.github.io/tip/2018/04/12/Tip_java_comment/)
출처: CleanCode - 로버트 C.마틴
