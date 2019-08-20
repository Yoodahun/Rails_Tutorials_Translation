# 제 5장 레이아웃의 작성

[제 4장](Chapter4.md) 에서의 간단한 Ruby투어에서는, *Sample* 어플리케이션에 어플리케이션 스타일 시트를 적용하는 방법을 배워보았습니다.([4.1](Chapter4.md#41-작성동기)) 그러나 [4.3.4](Chapter4.md#434-다시-한-번-CSS) 에서 지적한 바와 같이, 해당 스타일시트는 아직 아무것도 작성되어있지 않은 상태입니다. 이번 챕터에서는 어플리케이션에 Bootstrap 프레임워크를 적용하고, 커스텀 스타일을 추가해볼 것입니다. 또한 지금까지 작성한 페이지(Home이나 About)등에 링크를 레이아웃에 추가해보겠습니다(5.1) 도중에 파셜(*Partial*) , Rails의 라우팅, Asset Pipeline에 대해 배워 볼 것이며, Sass에 대해서도 소개하는 시간을 가지겠습니다.(5.2) 이번 장의 마지막 부분에는 유저를 사이트에 로그인시키기 위한 중요한 작업을 진행해볼 것입니다.(5.4)



이번 장에서는, Sample 어플리케이션에 레이아웃을 추가하거나, 수정하는 등의 부분을 중점적으로 소개하겠습니다. 또한 레이아웃에 대해서는 테스트주도개발을 한다거나, 모든 테스트 케이스를 작성하지 않는 부분도 있습니다.(테스트를 작성하는 것과 작성하지 않는 것에 대한 가이드라인에 대해서는 [컬럼3.3](Chapter3.md#컬럼-33-결국-테스트는-언제-하는-것이-좋은가) 에서 말씀드리고 있습니다.) 때문에 이번 장에서는 텍스트에디터에 의한 수정과 브라우저에 의한 확인이 대부분의 작업이 될 것입니다. 테스트 주도 개발을 하는 유일한 부분은, 5.3.1의 Contact페이지를 추가하는 부분정도입니다. 마지막으로 새로운 테스트방법인 "통합 테스트(혹은 결합테스트, *Integration Test*)" 에 대해 소개하고자 합니다.(5.3.4) 통합 테스트를 통하여 최종적으로 레이아웃이나 링크가 제대로 움직이는지를 체크해볼 것입니다.



## 5.1 구조를 추가해보자

*Rails Tutorial* 은, Web 개발을 위한 책이며, Web디자인을 위한 책은 아닙니다. 그러나 그렇다고해서 *아무런 스타일도 없는* 쓸쓸한 디자인의 어플리케이션으로 언제까지나 작업하고 있을 수도 없는 노릇이빈다. 이번 장에서는 레이아웃에 몇가지 구조와 CSS를 추가하여 최소한의 스타일을 추가해볼 것입니다. 커스텀 CSS룰 이외에도, Twitter에 의한 오픈소스 Web 디자인 프레임워크로 공개되어있는 [*Bootstrap*](http://getbootstrap.com/) 을 이용해볼 것입니다. 또한 코드 그 자체에도 스타일을 적용해볼 것입니다. 즉, 여기저기 붙여넣은 레이아웃 코드를, 파셜(*Partial*) 기능을 사용하여 정리해나가는 그러한 과정을 진행해볼 것입니다.



Web 어플리케이션을 작성할 때, 유저인터페이스의 개념을 되도록 빠른 단계에서 파악해놓는 것이 좋습니다. 본 튜토리얼에서는 목업(Web의 문맥에서는 와이어프레임 이라고도 불립니다.) 이라고 하는, 개발 후의 어플리케이션의 디자인을 스케치해볼 것입니다. 또한 본 챕터에서는 주로 [3.2](Chapter3.md#32-정적인-페이지) 소개한 사이트로고, 네이게이션 헤더, 사이트 푸터를 포함한 정적페이지를 개발해볼 것입니다.  제일 중요한 Home페이지의 목업은 아래와 같습니다. 목업을 기반으로 작성한 최종 결과는 본 장의 마지막 부분에서 확인해볼 수 있을 것입니다. 목업과 최종결과물을 확인해본다면, 세부적인 부분이 조금 다른 것을 알 수 있습니다. (예를 들어, 실제로는 마지막에 Rails 로고를 페이지에 추가합니다.) 그러나 목업은 어디까지나 목업이기 때문에 정확하게 만들 필요는 없습니다.

​                ![](../image/Chapter5/home_page_mockup_3rd_edition.png)



Git의로 버전관리를 하고 있는 경우라면, 지금까지와 마찬가지로 지금 시점에서 새로운 브랜치를 작성하는 것이 좋을 것 같습니다.

`$ git checkout -b filling-in-layout`



### 5.1.1 네비게이션

제일 첫 번째로,  Sample 어플리케이션에 링크와 스타일을 추가하기 위해, 사이트의 레이아웃 파일 `application.html.erb` 에 HTML 구조를 추가하고, 레이아웃파일을 수정해봅시다. 이번 수정은 영역(div태그)의 추가, CSS클래스의 추가, 사이트 네비게이션의 기점이 되는 영역의 추가도 포함됩니다. 완전한 파일은 아래의 코드와 같습니다. 이어서 해당 파일을 구성하고 있는 많은 코드들에 대해 해설하겠습니다. 

```erb
<!-- app/views/layouts/application.html.erb -->
<!DOCTYPE html>
<html>
  <head>
    <title><%= full_title(yield(:title)) %></title>
    <%= csrf_meta_tags %>
    <%= stylesheet_link_tag    'application', media: 'all',
                               'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application',
                               'data-turbolinks-track': 'reload' %>
    <!--[if lt IE 9]>
      <script src="//cdnjs.cloudflare.com/ajax/libs/html5shiv/r29/html5.min.js">
      </script>
    <![endif]-->
  </head>
  <body>
    <header class="navbar navbar-fixed-top navbar-inverse">
      <div class="container">
        <%= link_to "sample app", '#', id: "logo" %>
        <nav>
          <ul class="nav navbar-nav navbar-right">
            <li><%= link_to "Home",   '#' %></li>
            <li><%= link_to "Help",   '#' %></li>
            <li><%= link_to "Log in", '#' %></li>
          </ul>
        </nav>
      </div>
    </header>
    <div class="container">
      <%= yield %>
    </div>
  </body>
</html>
```

위 코드에서 새로운 코드를 확인해봅시다. [3.4.1](Chapter3.md#341-타이틀을-테스트해보자-Red) 에서도 간단히 설명했습니다만, Rails은 기본으로 HTML5를 지원하고 있습니다. (`<!DOCTYPE html>` 이라고 쓰여져 있는 것이 HTML5라는 것을 선언하고 있는 것입니다.) 그렇지만, HTML5은 비교적 새로운 기술이며, 일부 브라우저 (특히 Internet Explorer) 에서는 HTML5의 지원이 제대로 되지 않을 수가 있습니다. 그 때문에 다음과 같은 JavaScript 코드(일명 [HTML5 shim (or shiv)](https://github.com/aFarkas/html5shiv) )를 사용하여 문제를 해결하고 있습니다.

```html
<!--[if lt IE 9]>
  <script src="//cdnjs.cloudflare.com/ajax/libs/html5shiv/r29/html5.min.js">
  </script>
<![endif]-->
```

위 코드는 조금은 신기해보이는 코드가 있습니다.

`<!--[if lt IE 9]>`

위 코드는 Microsoft Internet Explorer (IE) 의 버전이 9보다 낮을 경우, (`if It IE 9`) 실행되는 코드입니다. 조금 신기해보이는 이 `if It IE 9` 는, Rails의 일부는 *아닙니다.* 위 코드는 [조건 코멘트](https://ja.wikipedia.org/wiki/条件付きコメント) 라는 것으로, 이번과 같은 상황을 위해 Internet Explorer 에서 특별하게 지원해주고 있습니다. 이것으로 Firefox, Chrome, Safari등의 다른 브라우저에 영향을 주지 않으면서도 IE의 버전이 9미만일 경우만, HTML5 shim 을 읽어들이는, 매우 편리한 녀석입니다.



그 다음 이어지는 부분에서는, 사이트의 로그를 표시하는 `header`(`div` 태그에 의한) 몇몇개의 영역태그, 네비게이션 링크의 리스트가 있습니다. 

```erb
<header class="navbar navbar-fixed-top navbar-inverse">
  <div class="container">
    <%= link_to "sample app", '#', id: "logo" %>
    <nav>
      <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home",   '#' %></li>
        <li><%= link_to "Help",   '#' %></li>
        <li><%= link_to "Log in", '#' %></li>
      </ul>
    </nav>
  </div>
</header>
```

`header` 태그는 페이지 윗부분에 와야할 요소들을 포함하고 있습니다. `header` 태그에는, `navbar`, `navbar-fixed-top`, `navbar-inverse` 라고 하는 세 가지 CSS 클래스가 스페이스로 구분지어져 적용되고 있습니다.

```html
<header class="navbar navbar-fixed-top navbar-inverse">
```

모든 HTML태그는, 클래스와 *id* 둘 다 지정할 수 있습니다. 단순한 라벨로써, CSS에서 스타일을 지정하기 위할 때 편리하게 사용할 수 있습니다. 클래스와 id의 차이점으로는, 클래스는 페이지 내부에서 몇번이고 중복해서 사용할 수 있는 것에 반해,  id는 단 한 번만 사용할 수 있습니다. 위 예제와 같은 경우, 모든 navbar 클래스에는 , 5.1.2 에서 인스톨하는 Bootstrap 프레임워크에 의해 특별한 의미가 부여됩니다.



`header` 태그 안쪽에는 `div` 태그가 있습니다.

`<div class="container">` 

`div` 태그는 일반적으로 표시영역(범위) 를 나타내며, 세부 요소(태그)를 각각의 개별부품으로 나눌때에도 사용합니다. 특히 조금 오래된 스타일의 HTML에서는, `div` 태그는 사이트 내부에서 거의 모든 범위에서 사용해왔습니다. 그러나 HTML5부터는 자주 쓰이는 범위마다 세분화할 수 있게 되었습니다. 구체적으로는 `header`태그, `nav` 태그, `section`태그 가 새롭게 사용할 수 있게 되었습니다. 또한 `header` 태그의 클래스와 마찬가지로, `div` 태그 에도 CSS클래스(`container`) 가 적용되고 있습니다만, 이 클래스도 Bootstrap에 있어서 특별한 의미를 가지고 있습니다.



div 태그 다음에는 erb Ruby코드가 등장합니다.

``` erb
<%= link_to "sample app", '#', id: "logo" %>
<nav>
  <ul class="nav navbar-nav navbar-right">
    <li><%= link_to "Home",   '#' %></li>
    <li><%= link_to "Help",   '#' %></li>
    <li><%= link_to "Log in", '#' %></li>
  </ul>
</nav>
```

여기서는 링크를 생성하기 위해서, Rails 헬퍼의 `link_to` 를 사용합니다. ([3.2.2](Chapter3.md#322-Static-Page를-편집해보자) 에서 본 앵커태그 `a` 가 생성됩니다.) `link_to` 의 첫 번째 파라미터로는 링크 텍스트, 두 번째 파라미터로는 URL 이 입력됩니다. 이 URL은 5.3.3에서 *Named Routes* 로 변환됩니다만, 지금은 Web디자인에서 일반적으로 사용되는 스터브용(더미 링크) URL인 `#` 을 입력합니다. 세 번째 파라미터로는 옵션해시로써, Sample 어플리케이션의 링크에서 CSS id `logo` 를 지정하고 있습니다. ( 다른 세 개의 링크에는 옵션해시가 지정되어있지 않습니다만, 필수가 아니기때문에 상관없습니다.)  Rails 헬퍼는, 이러한 옵션해시를 자주 사용하고 있으며, Rails 코드를 사용하면서, 임의의 HTML 옵션을 유연하게 추가할 수 있습니다.



div의 안쪽 태그 2번째 요소는 리스트아이템 태그 `li` 와 순번이 없는 리스트 태그 `ul` 에 의해 생성된 네비게이션 링크 리스트가 있습니다.

```erb
<nav>
  <ul class="nav navbar-nav navbar-right">
    <li><%= link_to "Home",   '#' %></li>
    <li><%= link_to "Help",   '#' %></li>
    <li><%= link_to "Log in", '#' %></li>
  </ul>
</nav>
```

정확하게는 여기서는 불필요합니다만, `nav` 태그 에는 "`nav` 태그 안쪽이 네비게이션 링크이다" 라고 하는 의미를 명시적으로 나타내기 위한 기능이 있습니다. 게다가 `ul` 태그에 적용되어 있는 `nav` 나 `navbar-nav`, `navbar-right` 클래스도 Bootstrap에 있어서 특별한 의미를 가지고 있습니다. 따라서 5.1.2에서 Bootstrap의 CSS를 추가했을 때, 위 태그에 스타일이 자동적으로 적용될 것입니다. 브라우저에서 소스코드를 확인하면 알 수 있을 것입니다만, Rails가 erb코드를 확인하고, 레이아웃을 출력한다면, 위 코드는 아래와 같이 바뀔 것입니다.

```html
<nav>
  <ul class="nav navbar-nav navbar-right">
    <li><a href="#">Home</a></li>
    <li><a href="#">Help</a></li>
    <li><a href="#">Log in</a></li>
  </ul>
</nav>
```

이 것이 Rails에서 브라우저에게 전달되는 HTML입니다.

레이아웃의 마지막 부분에는 메인컨텐츠용의 `div` 가 있습니다.

```erb
<div class="container">
  <%= yield %>
</div>
```

마찬가지로, `container` 클래스도 Bootstrap 에 있어서 특별한 의미를 가지고 있습니다. [3.4.3](Chapter3.md#343-레이아웃과-html에-직접-쓰는-Ruby-Refactor-연습) 에서 확인한 것과 마찬가지로, `yield` 메소드는  Web사이트의 레이아웃에 각각의 페이지의 내용을 출력합니다.



5.1.3에서 추가하는 사이트푸터를 제외하고는, 이것으로 레이아웃이 완성되었습니다. Home페이지에 접속하여 표시결과를 확인할 수 있습니다. 이후 스타일 요소를 이용하기 위해서는 `home.html.erb` 뷰 파일에 특별한 요소를 몇가지 추가합니다.

```erb
<!-- app/views/static_pages/home.html.erb -->
<div class="center jumbotron">
  <h1>Welcome to the Sample App</h1>

  <h2>
    This is the home page for the
    <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
    sample application.
  </h2>

  <%= link_to "Sign up now!", '#', class: "btn btn-lg btn-primary" %>
</div>

<%= link_to image_tag("rails.png", alt: "Rails logo"),
            'http://rubyonrails.org/' %>
```