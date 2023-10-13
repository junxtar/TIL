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

# **좋은 주석**

### 법적인 주석

```
// Copyright (C) 2003,2004,2005 by Object Mentor, Inc. All rights reserved.
// GNU General Public License 버전 2 이상을 따르는 조선으로 베포한다.
```

- 몇몇 회사는 법적인 이유로 특정 주석을 넣으라 명시하는데 소스 첫머리에 저작권 & 소유권 정보를 주석으로 남긴다.

### 정보를 제공하는 주석

```
String str = "a1b2c3d4";
str = str.replaceAll("[0-9]", ""); //문자열에 포함한 숫자를 제거하는 정규식
```

- 밑에서 제시하는 코드에서는 정규식을 활용한 문자열 변환하는 과정이다. 정규식에 대한 정보를 주석으로 제공합니다.

### 의도를 설명하는 주석

```
static class Person implements Comparable<Person> {
		String name;
		int age;

		private Person(String name, int age) {
			this.name = name;
			this.age = age;
		}

		@Override
		public int compareTo(Person o) {
			if (name.length() == o.name.length()) {
				return name.compareTo(o.name);
			}
			return name.length() - o.name.length(); // 이름의 길이순으로 정렬 같다면 사전순
		}
	}

```

- 때때로 주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명합니다.

### 의미를 명료하게 밝히는 주석

```
public void testCompareTo() throws Exception {
    WikipagePath a = PathParser.parse("PageA");
    WikiPagePath ab = PathParser.parse("PageA. PageB");
    Wikipagepath b = PathParser.parse("PageB");
    WikiPagepath aa = PathParser.parse("PageA.PageA");
    WikiPagePath bb = PathParser.parse("PageB. PageB");
    WikipagePath ba = PathParser.parse("PageB. PageA");

    assertTrue(a.compareTo(a) == 0); // a == a
    assertTrue(a.compareTo(b) != 0); // a != b
    assertTrue(ab.compareTo(ab) == 0); // ab == ab
    assertTrue(a.compareTo(b) == -1); // a < b
    assertTrue(aa.compareTo(ab) == -1); // aa < ab
    assertTrue(ba.compareTo(bb) == -1); // ba < bb
    assertTrue(b.compareTo(a) == 1); // b > a
    assertTrue(ab.compareTo(aa) == 1); // ab > aa
    assertTrue(bb.compareTo(ba) == 1); // bb > ba
}
```

- 인수나 반환값이 표준 라이브러리나 변경하지 못하하는 코드에 속하면 의미를 명료하게 밝히는 주석이 유용하다.

### 결과를 경고하는 주석

```
//여유 시간이 충분하지 않다면 실행하지 마십시오.
public void testCodeRellyBigFile(){
	...
}
```

- 다른 프로그래머에게 결과를 경고할 목적으로 주석을 사용한다.

### TODO 주석

```
//TODO 현재 기본 전략만 반환하고 있다.
//기획팀과 비즈니스로직 성립시 신규 전략 추가가 필요하다.
public Strategy makeStrategy() throws Exception { return new DefaultStrategy(); }
```

- '앞으로 할 일'을 TODO 주석으로 남겨두면 편하다.
- 당장 구현하지 않아도 되는 일들을 주석으로 남겨두는 것이다.
- 우선순위가 높지 않은 경우, 미래에 해도 되는 일의 경우에 이렇게 남겨놓으면 좋다.

### 중요성을 강조하는 주석

```
String listltemContent = match.group(3).trim();
// 여기서 trim온 정말 중요하다. trim 합수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문지열로 인식되기 때문이다.
new ListltemWidget(this, listltemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

- 독자가 대수롭지 않게 여길 만하지만 중요성을 강조하기 위해 사용한다.

<!-- # **나쁜 주석** -->

[참고자료](https://velog.io/@hangem422/clean-code-comment)
출처: CleanCode - 로버트 C.마틴
