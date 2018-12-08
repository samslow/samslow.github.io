---
layout: post
title:  "MacOs DialogFlow에 Csv 넣기"
subtitle:   "개발자가 선택장애를 가지면 써지는 글"
categories: development
tags: python
comments: true
---

GoogleAssistant를 사용하기위해 DialogFlow를 사용하며 겪은 문제중 

`csv import` 에 관한 해결을 적은 글입니다.



# MacOS DialogFlow 에 csv 넣기
## 크롤링까진 무난했다

공공데이터API에서 가져온 식품 DB를 Ruby on Rails로 가져오는 것 까진 좋았는데

Mac에서는 UTF Encoding의 문제로 DB로부터 csv파일을 추출하여 열면 한글이 와장창 깨지게 된다.

![image-20181209001235334](/assets/post_img/image-20181209001235334.png)

<center>"쎄뎅 대쩧?"</center>



Excel 내에서 Encoding을 변경하는 방법도 있을 수 있겠지만 어떻게 하는지 몰라서

Mac에서 csv파일을 열 때 한글 깨짐 오류는 아래의 방법으로 해결했다.

1. csv파일을 `텍스트 편집기` 로 연다

2. cmd+shift+s를 눌러 다른 이름으로 저장

3. `UTF-16` 방식으로 인코딩설정을 한다.

4. 파일 이름을 `파일이름.csv` 로 저장한다

   ![스크린샷 2018-12-09 오전 12.10.02](/assets/post_img/df-scv0.png)

   ![image-20181209001421956](/assets/post_img/image-20181209001421956.png)

   <center>다행히 정상적으로 출력된다!</center>



## 끝날때까지 끝낸게 아니다.

이 데이터를 그대로 DialogFlow에 Upload Entity로 import하게되면 대충 아래와 같은 문구를 접하게 된다.

![image-20181209001704947](/assets/post_img/스크린샷 2018-12-09 오전 12.16.34.png)

그래서 조금더 짱구를 굴려서 Entity를 추가하고 오른쪽 위 `···`버튼을 눌러 `Switch to raw mode` 에서 데이터를 긁어서 넣어보는 시도를 해보았다.

![스크린샷 2018-12-09 오전 12.18.21](/assets/post_img/스크린샷 2018-12-09 오전 12.18.21.png)

이번에는 `CSV format` 이 안 맞다는 아래와 같은 오류가 뜬다.

![image-20181209002016894](/assets/post_img/image-20181209002016894.png)

도대체 그 포맷이 뭘까 생각하여 단순하게 대충 만든 Entity를 csv로 추출하여 보니 `" A "," B " ` 의 구조를 가져야만 했다.

그리고 추가적으로 한 Row는 하나의 Entity이다.

오류 메세지 진짜 엄청 많네

내 데이터의 문제는 

1. 모든 Row가 `""` 로 둘러싸여 있지 않다.
2. 쉼표 뒤에 데이터가 없다 ( 공란으로 Download는 가능하지만 올리는건 안됨 ^^ )
3. 아래 경고메시지에 무방비하다; 괄호의 사용 금지`(), {}, [], <>`
   - ![스크린샷 2018-12-09 오전 12.24.47](/assets/post_img/스크린샷 2018-12-09 오전 12.24.47.png)

그래서 데이터 전처리를 위해 새롭게 코드를 짜게된다.

```python
f = open("16extract_name_full_foods_converted.csv", 'r', encoding="utf16", errors='ignore')
o = open("converted_name_foods.csv", 'w')
line = f.read().splitlines()

for i in range(13531):
	if line[i][0] != '\"': # 더블쿼트가 없다면 추가!
		line[i] = '\"' + line[i] + '\"'
	
	line[i] = line[i] + ", " + line[i] # 콤마와 데이터 복사 붙이기
	#괄호를 공백 혹은 제거하기
	line[i] = str(line[i]).replace('(', ' ')
	line[i] = str(line[i]).replace(')', '')
	line[i] = str(line[i]).replace('>', ' ')
	line[i] = str(line[i]).replace('<', '')

	o.write(line[i]+ '\n')

f.close()
o.close()
```

이 모든것을 하고나면 데이터는 예쁘게 나오게 된다.

![image-20181209003431221](/assets/post_img/image-20181209003431221.png)

저대로 예쁘게 긁어서 Raw mode에 넣어주면 끗!