---
layout: post
title: '글을 쓰는 방법'
author: minchul.kang
date: 2020-01-11 12:00
tags: [introduction]
---


4주간 본 사이트에 포스팅하는 방법에 대해 알아봅시다.   
이 포스트는 독자가 [이 github organization](https://github.com/web-together)의 member로 참여되었음을 전제 하에 설명합니다.   
또한 독자가 markdown 문법을 알고 있다는 전제 하에 설명합니다.

## 저자 등록

글을 처음 쓰신다면 본인을 저자로 등록해야 합니다. [`_authors` 폴더](https://github.com/web-together/web-together.github.io/tree/master/_authors)를 보면 지금까지 이 사이트에 글을 작성한 저자들의 이름으로 된 markdown 파일들이 나타나게 됩니다. 이 곳에 `본인이름.md`으로 markdown 파일을 만들어 주고, 그 안에 다음과 같이 내용을 채워줍니다.

```
---
name: 영문_이름
title: 한글_이름
image: /files/authors/저자_사진.jpg
---
```

`/files/authors` 폴더에 사이트에 나타나길 원하는 본인의 사진을 올리시면 되는데,
꼭 본인 얼굴이 나타날 필요는 없습니다. 

저자로 등록하게 되면 authors 목록에 저자 본인 이름이 등록 되고, 앞으로 저자 본인이 쓰는 모든 글들에 대해 아래 사진과 같이 저자가 작성한 다른 글들의 목록들을 볼 수 있게 됩니다.

![저자등록](/files/minchul-introduction-01.png)


## 글 쓰기

저자로 등록이 되었다면 이제 글을 쓰면 됩니다. [`_posts` 폴더](https://github.com/web-together/web-together.github.io/tree/master/_posts)를 살펴보면 `날짜-제목.md` 형식의
블로그 글들이 모여있음을 확인할 수 있습니다. 이 markdown 하나하나가 각각의 블로그 글이라고 보시면 됩니다.

예를 들어, 오늘 날짜는 2020-01-12이므로 `_posts` 폴더 안에 `2020-01-12-test.md` 라는 형식으로 markdown 파일을 만들어볼 수 있겠네요.

### front matter

블로그 본문을 작성하기 전에, 이 사이트에게

> 이 markdown은 조금 특별한 markdown이야!
>
> 이 markdown은 블로그 글이야!

를 파일 상단에 적어줌으로써 알려주어야 합니다. 이렇게 알려주는 문구를 [front matter](https://jekyllrb.com/docs/front-matter/)라고 합니다. 다른 블로그 글을 한 번 볼까요? 가령 지금 보고 계신 [이 블로그글](https://raw.githubusercontent.com/web-together/web-together.github.io/master/_posts/2020-01-11-how-to-write.md)의 front matter를 봅시다.

```
---
layout: post
title: '글을 쓰는 방법'
author: minchul.kang
date: 2020-01-11 12:00
tags: [introduction]
---
```

이렇게 써 있네요. `layout:post`는 *이 markdown은 블로그 글이야* 하고 알려주는 부분이라고 보시면 됩니다.

`title`은 블로그 제목입니다. 반드시 적어주어야겠죠? `author`는 앞서 등록한 본인의 이름을 적어주시면 되는데, 한글 이름(e.g. 강민철)이 아니라, `_authors`에 추가한 markdown 이름(e.g.minchul.kang)으로 적어주세요. 

`date`는 날짜와 시간입니다. 형식에 맞게 적어주셔야 합니다. 

`tags` 는 말 그대로 이 블로그의 태그입니다. 모든 글에는 `Tag` 가 붙습니다. 이 `Tag`는 해당 글의 주제를 키워드로 알려주고, 추후에 비슷한 `Tag (주제)` 끼리 모아볼 수 있게 하고,
검색의 효과를 높이는 효용을 주기 때문에 작성한 글에 대한 적절한 키워드를 꼭 등록/기입해주어야 합니다.

본인이 작성한 글에 대한 Tag가 이미 `_tags` 폴더에 등록이 되어 있다면, 그냥 적어주시면 됩니다. 
이미 등록된 태그의 목록은 [`_tags` 폴더](https://github.com/web-together/web-together.github.io/tree/master/_tags)를 보셔도 되고, [이 url (/tags) ](https://web-together.github.io/tags/)에서 확인하실 수도 있습니다.

예를 들어 `introduction` 이라는 이름의 태그는 이미 `_tags` 안에 등록되어 있으므로, 
그냥 아래와 같이 적어주시면 될 겁니다.


```
tags: [introduction]
```

하지만 작성하고자 하는 태그가 없다면, 새로운 태그를 등록해주셔야 합니다.

### Tag 등록

새로운 tag를 등록하기 위해서는 [`_tags` 폴더](https://github.com/web-together/web-together.github.io/tree/master/_tags) 안에 `태그이름.md` 형식으로 markdown을 만든 뒤,
그 안에 다음과 같은 내용을 채워주시면 됩니다.

```
---
name: TagName_In_English
title: '태그의 한글 이름'
---
```

그럼 다음부터 [이 url (/tags) ](https://web-together.github.io/tags/)에서 등록한 tag를 볼 수 있게 되고,
해당 tag로 글을 모아볼 수 있게 됩니다.

![Code Festival이라는 태그로 글들 모아보기](/files/minchul-introduction-03.png)


### 사진, 코드 첨부하기

글을 작성하시다가 사진을 이용하고 싶으실 떄가 있을 겁니다. 하지만 여기서, 행사, 컨퍼런스 후기, 각종 인터뷰 및 뉴스, 체험기 등은 사진이 많으면 좋지만, 기술적인 튜토리얼에 해당하는 글에는 꼭 필요한 경우를 제외하고는 사진의 사용을 최소화하는 것이 좋습니다. 따라서,

> 실행화면, 이해를 높이는 도식도 정도만 사진으로 표현합시다.
> 
> 특히, 코드는 사진이 아닌 markdown 문법으로 작성하는 것을 추천합니다. 

왜냐하면, 독자들이 예제 코드를 드래그 해서 
쓰기에 용이하고, 키워드 검색 등에 유리하기 때문입니다.

블로그에 사용할 사진은 [`files` 폴더](https://github.com/web-together/web-together.github.io/tree/master/files)에 저장하시면 됩니다.

이건 필수는 아니지만, 
사진 파일의 이름을 다른 사람과 겹치지 않도록 abc.jpg, xxx.jpg 등이 아닌, 자기이름-글제목-사진번호.jpg 와 같은 형식으로 업로드해주시면 감사하겠습니다.

또한, 코드는 아래와 같이 작성하면 자동으로 언어-문법 별로 색깔(highlight)이 칠해집니다.

```

\```특정언어
코드
\```

```
> 위에서 \를 뺀 형식으로 작성해주시면 됩니다.

예를 들어, C++ 코드에 맞는 색을 입히고 싶으면 아래와 같이 작성해주시면 됩니다.

```

\```cpp
int main(){
  return 0;
}
\```
# 여기서 \ 빼기

```

그럼 결과적으로 아래과 같이 c++에 맞게 색깔이 입혀집니다.

```cpp
int main(){
  return 0;
}
```

다른 언어도 마찬가지죠. javascript를 봅시다.

```

\```javascript

document.write("Hello, world!"); 
alert("Hello, world!");
console.log("Hello, world!");

\```

```

다음과 같이 색이 입혀집니다.

```javascript
document.write("Hello, world!"); 
alert("Hello, world!");
console.log("Hello, world!");
```

파이썬도 마찬가지입니다.

```python
print("this is python")
```


### 발행

블로그를 모두 작성했다면, (원한다면) 로컬에서 실행해보고, pull request를 날리시면 됩니다.


## 추가 유의사항

### 설명은 길고 자세하게, 예제코드는 짧고 간결하게 

독자는 해당 기술에 대해 모른다는 전제하에 작성해야 가독성과 이해도가 높아집니다.   
또한 저자 본인도 **말로는 표현하기 어려운, 어렴풋이 알고 있는 개념을 남에게 설명하듯 다시 말로 적어 봄으로써** 능률적인 실력향샹을 꾀할 수 있습니다.
따라서 기술 설명은 길고 자세히 작성하는 것을 추천해드립니다.

또한 간결한 예제 코드를 포함해야 이해가 빠릅니다.

때론 100마디 말을 몇 줄의 코드가 더 잘 설명하는 개념도 있기 마련입니다.
짧고 간결한 예제 코드는 기술 글에 대한 수준을 높여줍니다.

### 무엇을 알고 있음을 전제로 하나?

이 또한 글에 꼭 적어두는 것이 좋습니다. 독자에 대한 배려 뿐 아니라, 저자 본인의 개발 지식에 대한 세이브포인트라고 생각하셔도 좋습니다.

