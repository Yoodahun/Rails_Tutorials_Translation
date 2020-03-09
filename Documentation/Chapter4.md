# 제 4장 Rails의 향기가 나는 Ruby

 이번 챕터에서는 [제3장](Chapter3.md) 에서 다루었던 예제를 기반으로, Rails에서 중요한 Ruby의 많은 요소들에 대해 좀 더 심도깊게 알아보도록 합니다. Ruby는 많은 기능을 가진 언어입니다만, Rails개발자에게 필요한 지식은 비교적 작은 수준입니다. 또한 일반적으로 Ruby 입문서에서 다루는 내용과는 꽤나 거리가 있습니다. 이번 챕터에서의 목적은, "Rails의 향기가 나는 Ruby" 라는 것에 대한 확고한 지식을 여러분의 개발경험에 상관없이 똑같이 알려드리도록 하겠습니다. 이번 챕터에서는 많은 주제들이 포함되어 있습니다만, 한 번 읽는 수준으로 이해할 필요는 전혀 없습니다. 앞으로 이 챕터를 빈번하게 돌아와 참고하게 될 것입니다.



## 4.1 작성동기

이전 챕터에서 보여드렸다시피, Ruby의 기초지식이 전혀 없는 상태에서 Rails 어플리케이션의 골격을 만들고, 게다가 테스트까지 해보았습니다. 본 튜토리얼에서 제공하는 테스트케이스 코드를 통과할때까지 에러메세지의 수정을 몇 번이나 반복하는 식으로 작업을 진행해왔습니다. 이러한 초보적인 작업을 언제까지나 할 수는 없기 때문에,  지금의 여러분들에게 Ruby에 관한 지식과 경험의 한계를 직접 몸으로 느껴가면서 이 한계를 뛰어넘기위해 이번 챕터에서 Ruby를 배워보도록 해봅시다.

일단 [3.2](Chapter3.md#32-정적인-페이지) 에서와 마찬가지로 토픽브랜치를 작성하고, 변경사항을 커밋해봅시다.

`git checkout -b rails-flavored-ruby`

토픽 브랜치에서 커밋한 변경사항은, 4.5에서 Merge하기로 합니다.

### 4.1.1 Helper

전 장의 마지막에서는 Rails의 레이아웃을 이용하여 뷰에서의 중복을 제거하고, 거의 정적인 페이지를 만들어보았습니다. 아래의 소스코드는 3장에서의 레이아웃파일과 같은 내용입니다.

```erb
<!DOCTYPE html>
<html>
  <head>
    <title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>
    <%= csrf_meta_tags %>
    <%= stylesheet_link_tag    'application', media: 'all',
                               'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application',
                               'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= yield %>
  </body>
</html>
```

여기서 아래의 코드를 주목해주세요.

```erb
<%= stylesheet_link_tag 'application', media: 'all',
                                       'data-turbolinks-track': 'reload' %>
```

여서는 Rails의 메소드 `stylesheet_link_tag` (자세한 것은 [Rails API](http://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-stylesheet_link_tag) 참고해주세요) 를 사용하여 `application.css` 을  [미디어 타입](http://www.w3.org/TR/CSS2/media.html) 으로 사용할 수 있게합니다. (미디어 타입에는 컴퓨터의 화면이나 인쇄화면도 포함되어있습니다.) Rails 개발 경험자에게 있어서 위 소스는 매우 단순한 코드이긴 합니다. 혼란스러울만한 Ruby의 개념이 4가지 정도가 있습니다. Rails의 메소드, 괄호를 쓰지 않는 메소드의 호출, 심볼, 해시 입니다. 이것들의 개념은 본 챕터에서 모두 설명하겠습니다.

### 4.1.2 Custom Helper

Rails의 뷰에서는 방대한 메소드를 사용할 수 있습니다만, 그것뿐만 아니라 새롭게 선언하는 것도 가능합니다. 새롭게 만든 메소드는 *커스텀 헬퍼* 라고 부릅니다. 커스텀헬퍼를 만드는 방법을 배우기 위해 일단 아래의 코드를 확인해봅시다.

`<%= yield(:title) %> | Ruby on Rails Tutorial Sample App`

위의 코드는, 페이지타이틀의 정의에 의존합니다. 이 정의는 다음과 같이 뷰에서 `provide` 에 의해 정의되어 있습니다.

```erb
<% provide(:title, "Home") %>
<h1>Sample App</h1>
<p>
  This is the home page for the
  <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
  sample application.
</p>
```

이 때, 만약 타이틀을 부여하고 있지 않으면, 타이틀이 빈 공백이 되어버리고 맙니다. 이것을 막기 위해 모든 페이지에서 쓰이는 기본 타이틀을 정하고, 어떠한 특정 페이지에서만 다른 타이틀을 표시하게끔 옵션을 주는 것도 가능합니다. 이것은 현재의 레이아웃에서도 *어떠한 점* 을 제외하고는 달성할 수 있습니다. 만약 뷰 하나에서 `provide` 를 삭제한다면, 그 페이지 고유 타이틀 대신에 다음 타이틀만 표시됩니다.

`  | Ruby on Rails Tutorial Sample App`

기본 타이틀로써는 이것은 틀린 것은 아닙니다만, 문자 앞쪽에 쓸모없는 파이프 ( | )가 들어가 있습니다.

페이지 타이틀이 올바르게 표시되지 않는 문제를 해결하기 위해, `full_title` 이라고 하는 헬퍼를 만들어 보도록 합니다. `full_time` 헬퍼는 페이지 타이틀이 정의되어있지 않을때, 기본 타이틀 "Ruby on Rails Tutorial Sample App" 을 표시하도록 하고, 페이지 타이틀이 정의되어 있다면, 기본 타이틀에 파이프를 추가하여 표시하도록 합니다.

```ruby
# app/helper/application_helper.rb #
module ApplicationHelper

  # 페이지마다 완전한 타이틀을 리턴합니다.
  def full_title(page_title = '')
    base_title = "Ruby on Rails Tutorial Sample App"
    if page_title.empty?
      base_title
    else
      page_title + " | " + base_title
    end
  end
end
```

헬퍼의 작성이 끝났기 때문에 이것을 사용해서 레이아웃을 심플하게 할 수 있습니다.

`<title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>`

즉, 위 코드가 아래와 같이 바뀌게 됩니다.

`<title><%= full_title(yield(:title)) %></title>`

바꾼 결과는 아래와 같습니다.

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
  </head>
  <body>
    <%= yield %>
  </body>
</html>
```

이 헬퍼를 정의하는 것으로, Home페이지에 지금까지 표시되어왔던 불필요한 "Home" 이라는 단어는 표시되지 않고, 기본 타이틀만 올바르게 표시할 수 있게 되었ㅅ브니다. 이렇게 하기 위해선 아래의 코드와 같이 이전 테스트코드를 조금 수정하여 `Home` 이라는 문자가 표시되지 않는 것을 확인하는 테스트케이스를 추가합니다.

```ruby
# test/controllers/static_pages_controller_test.rb
require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest
  test "should get home" do
    get static_pages_home_url
    assert_response :success
    assert_select "title", "Ruby on Rails Tutorial Sample App"
  end

  test "should get help" do
    get static_pages_help_url
    assert_response :success
    assert_select "title", "Help | Ruby on Rails Tutorial Sample App"
  end

  test "should get about" do
    get static_pages_about_url
    assert_response :success
    assert_select "title", "About | Ruby on Rails Tutorial Sample App"
  end
end
```

테스트를 실행해보면, 테스트가 실패하는 것을 알 수 있습니다.

```
$ rails test
3 tests, 6 assertions, 1 failures, 0 errors, 0 skips
```

테스트를 정상적으로 통과하기 위해선, 아래와 같이 Home페이지의 뷰에서 `provide` 행을 삭제해야할 필요가 있습니다.

```erb
<!-- app/views/static_pages/home.html.erb -->
<h1>Sample App</h1>
<p>
  This is the home page for the
  <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
  sample application.
</p>
```

이 시점에서 테스트를 실행하면, 통과할 것입니다.

`$ rails test`

*주의* : 지금까지는 `rails test`  실행한 결과의 일부(성공결과 혹은 실패결과) 를 기재했습니다만, 지면관계상 이 후의 실행결과는 생략합니다.

Rails 개발 경험자에게는 아래의 코드는 스타일 시트를 읽어들이는 메소드와 별반 다를 것 없는 단순한 것입니다만, 여기에도 Ruby의 중요한 개념이 다수 포함되어 있습니다. 구체적으로는 모듈, 메소드의 정의, 임의의 메소드 파라미터, 코멘트, 로컬변수의 할당, 논리값, 제어 플로우, 문자열의 결합, 그리고 리턴값 등입니다. 이 것들의 개념에 대해 이번 챕터에서 배워보도록 합시다.

```ruby
module ApplicationHelper

  # ページごとの完全なタイトルを返します。
  def full_title(page_title = '')
    base_title = "Ruby on Rails Tutorial Sample App"
    if page_title.empty?
      base_title
    else
      page_title + " | " + base_title
    end
  end
end
```



## 4.2 문자열과 메소드

Ruby를 배우기 위한 도구로써 주로 Rails console을 사용해보도록 합시다. 이것은 [2.3.3](Chapter2.md#233-유저는-많은-마이크로포스트를-가지고-있다) 에서 한 번 본 적이 있는, Rails 어플리케이션을 대화형태로 조작하기 위한 커맨드라인 툴입니다. Rails 콘솔은 `irb` (IRB: Interactive RuBy) 를 확장시켜 만들어졌기 때문에, Ruby의 기능을 모두 사용해볼 수 있습니다. (4.4.4 에서도 설명하겠습니다만, Rails의 콘솔로부터 Rails의 환경에 접근할 수도 있습니다.)

클라우드 IDE에서 사용하는 겨웅에는, 추천하는 irb의 설정이 있습니다. 간단한 텍스트에디터 `nano` 를 이용하여, 일단 홈 디렉토리에 `.irbrc` 파일을 만들어봅시다.

`$ nano ~/.irbrc`

다음으로, 아래와 같은 설정을 작성합니다. 이 설정으로 인하여 irb의 프롬프트가 간결하게 표시되며, irb의 자동 들여쓰기 기능도 비활성화됩니다.

```
IRB.conf[:PROMPT_MODE] = :SIMPLE
IRB.conf[:AUTO_INDENT_MODE] = false
```

 마지막으로 Ctrl+X를 눌러서 `nano` 에디터로부터 나옵니다. 파일을 저장할지말지 묻는 내용이 출력되는데, y키를 눌러서 `~/.irbrc` 파일을 저장합니다.

이것으로 준비작업은 끝났습니다. 다음으로 커맨드를 실행해서 Rails 콘솔을 움직여봅시다.

```
$ rails console
Loading development environment
>>
```

 디폴트 상태에서는, 콘솔은 *development(개발) 환경* 이라고 하는, Rails에 의해 정의된 3종류의 환경 중 1개로 기동합니다. (다른 2개는 *test* 와 *production (실제 배포환경*  입니다.)  이 환경들은 이번 챕터에서는 그렇게 중요하진 않습니다. 그러나 다음 챕터 이후에서부터는 중요한 개념이 됩니다. 7.1.1에서 이러한 환경에 대해 자세히 설명하겠습니다.

Rails콘솔은 대단한 학습도구이며, 그 안을 자유롭게 둘러볼 수 있습니다. 콘솔의 안에서 무엇을 하던지간에, 무엇을 부수던지하는 것은 없기때문에 안심하셔도 좋을 것 같습니다. 만약 Rails 콘솔에서 무엇인가 좋지않은 동작이 발생한다면, Ctrl+C를 이용하여 콘솔을 강제적으로 종료할 수 있습니다. 혹은 Ctrl+D 를 눌러서 정상적으로 콘솔을 종료할 수도 있습니다. (이러한 트러블 슈팅은 Command Line 튜토리얼 [Getting out of Trouble](https://www.learnenough.com/command-line-tutorial#aside-getting_out_of_trouble) 에서 확인할 수 있습니다.) 또한, 일반적인 터미널과 마찬가지로, Ctrl+P 혹은 위 화살표 방향키를 눌러서 이전 단계에 실행한 커맨드를 다시 사용할 수 있습니다. 입력하는 시간을 절약할 수 있으니 꼭 사용해보세요.

또한 이번 챕터를 진행하는 도중에, 잘 모르겠다싶은 코드가 있다면, [Ruby API](http://ruby-doc.org/) 를 참고해가면서 공부하는 것을 추천드립니다. Ruby API에는 많은 정보가 있습니다(조금은 어려울 수도 있습니다.). 예를 들어, Ruby의 문자열에 대해 상세히 알고 싶은 경우에는, Ruby API 엔트리의 `String` 클래스를 참고하는 것이 좋을 것 같습니다.

### 4.2.1 코멘트

Ruby의 코멘트는 샾 기호 `#` (해시마크 혹은 *octothorpe* 라고 읽기도 합니다.) 부터 시작하여 그 행의 끝나는 부분까지를 코멘트처리합니다. Ruby는 코멘트의 내용을 실행하지는 않습니다만, 적절한 코멘트는 해당 코드를 읽는 사람에게 있어서 매우 유용합니다. 예를 들어, 아래의 코드의 경우

```ruby
# 페이지 별로 완전한 타이틀을 리턴합니다.
def full_title(page_title = '')
  .
  .
  .
end
```

 맨 처음의 행이, 그 다음의 정의되어 있는 메소드의 목적을 설명하고 있는 코멘트에 해당합니다.

코드를 콘솔내부에서 입력하는 사람은 보통은 없습니다만, 여기서는 학습을 위해서 일부러 다음과 같이 코멘트를 추가해보겠습니다.

```
$ rails console
>> 17 + 42   # 정수의 덧셈
=> 59
```

이번 장의 코드를 (파일에 저장하는 것이 아닌) Rails 콘솔에 입력하거나, 복사해서 붙여넣기하는 정도라면 코멘트를 생략해도 상관없습니다. 코멘트를 Rails의 콘솔에 입력해도 코멘트는 항상 무시되기때문에 문제될 것은 없습니다.

### 4.2.2 문자열

문자열(*String*) 은, Web 어플리케이션에서는 아마도 제일 중요한 데이터 구조일 것입니다. 문자열은 Web페이지라고 하는 것이 궁극적으로 서버에서부터 브라우저에 송신되어진 문자열이기 때문입니다. 그러면 콘솔에서 문자열에 대해 알아보도록 합시다.

```
$ rails console
>> ""         # 빈 문자열
=> ""
>> "foo"      # 비어있지 않은 문자열
=> "foo"
```

여ㅅ 입력한 것은 *문자열 정수* 라고 불리며, 터블 쿼테이션 `""` 으로 감싸는 것으로 작성할 수 있습니다. 이 콘솔에서는 입력한 각각의 행을 확인하여 결과를 표시하고 있으며, 문자열 정수에 경우 문자열 자신을 표시합니다.

`+` 기호를 사용하여 문자열의 결합도 가능합니다.

```
>> "foo" + "bar"    # 文字列の結合
=> "foobar"
```

실행 결과로는, `foo` 와 `bar` 를 합한 `foobar` 가 됩니다.

문자열을 조합할 수 있는 다른 방법으로는, 식의 전개 ( *interpolation* )  이라고 하는 것이 있는데, `#{}` 이라고 하는 특수한 구문을 사용합니다.

```
>> first_name = "Michael"    # 변수의 대입
=> "Michael"
>> "#{first_name} Hartl"     # 문자열의 식 전개
=> "Michael Hartl"
```

예를 들어, `"Michael"` 이라고 하는 값을 `first_name` 변수에 대입( *assign* )  하여, 이 변수를 `"#{first_name} Hartl"` 이라고 하는 형태로 문자열 안에 기입하게 되면, 문자열의 안에서 해당 변수가 전개되게됩니다. 마찬가지로, 성과 이름 양쪽을 전부 변수에 할당하여 사용할 수도 있습니다.

```
>> first_name = "Michael"
=> "Michael"
>> last_name = "Hartl"
=> "Hartl"
>> first_name + " " + last_name    # 이름과 성 사이에 공백을 넣은 것.
=> "Michael Hartl"
>> "#{first_name} #{last_name}"    # 식 전개를 사용하여 결합 (위 결과와 동일)
=> "Michael Hartl"
```

마지막의 2개의 출력결과는, 똑같은 결과입니다. 저자는 두 번째 방식을 선호합니다. 공백을 `"  "` 와 같이 직접 공백을 넣는 것은 그다지 좋진 않은 것 같진 않습니다.

#### 출력

문자열을 *출력* 하고 싶을 경우 (화면에 표시하고 싶을 경우), Ruby의 메소드에서 제일 일반적으로 사용되는 것은 `puts` 입니다. (put의 3인칭단수 표현이 아닌, *put string* 의 약자입니다. 원래는 *put ess* 라고 발음합니다만, 최근에는 *puts* 라고 많이들 발음합니다.)

```
>> puts "foo"     # 문자열을 출력하는 경우
foo
=> nil
```

`puts` 메소드에서는 *부작용*  이 중요한 역할을 합니다. 무슨 말이냐 하면, `puts "foo" ` 는 문자열 "foo" 를 부작용으로 스크린에 표시합니다만, 리턴값으로서는 문자 그대로 아무것도 없다는 뜻의 `nil`  을 리턴합니다. nil은 "아무것도 없다" 라는 것을 나타내는 Ruby의 특별한 값입니다. 또한 `=> nil`  이라고 하는 결과는, 간소화를 위해 이 다음부터는 생략하겠습니다.  

위의 설명에서 알 수 있듯, `puts` 를 사용하여 출력하면, 개행문자인 `\n` 이 자동적으로 출력의 맨 마지막에 추가됩니다. (이것은 터미널의 `echo` 와 같은 동작을 합니다.) `print` 메소드도 마찬가지로 똑같은 출력을 합니다만, 개행문자가 추가되지 않는 점이 다릅니다.

```
>> print "foo"    # 문자의 화면출력 (puts와 같지만, 개행이 추가되진 않는다.)
foo=> nil
```

위와 같이 `foo` 가 출력 된 후에 개행되지 않기 때문에 그 상태에서 `=> nil` 이 연속해서 출력됩니다.

의도적으로 *개행* 을 추가하고 싶을 때에는 "백슬래시 (\) +n (\n) " 이라고 하는 개행문자를 사용합니다. 이 개행문자와 `print` 를 조합하면, `puts` 과 동일한 출력을 합니다.

```
>> print "foo\n"  # puts "foo" と等価
foo
=> nil
```

#### 싱글 쿼테이션 내부의 문자열

지금까지의 예시에서는 전부 *더블 쿼테이션 문자열* 을 사용하였습니다만, Ruby에서는 *싱글 쿼테이션* 도 지원합니다. 대부분의 경우, 더블쿼테이션과 싱글쿼테이션 어느쪽이든 똑같이 사용할 수 있습니다.

```
>> 'foo'          # 싱글 쿼테이션의 문자열
=> "foo"
>> 'foo' + 'bar'
=> "foobar"
```

단, 1가지 중요한 점이 다릅니다. Ruby에서는 싱글쿼테이션 문자열 안에는 식의 전개를 할 수 없습니다.

```
>> '#{foo} bar'     # 싱글 쿼테이션 안에서는 식의 전개를 할 수 없다.
=> "\#{foo} bar"
```

거꾸로 말하자면, 더블쿼테이션 문자열을 이용한 문자열에서 `#` 과 같이 특수한 문자를 사용하고 싶은 경우에는, 이 문자를 백슬래시로 이스케이프(*escape*) 하여 사용할 필요가 있습니다.

더블 쿼테이션의 문자열도 싱글 쿼테이션의 문자열과 같은 처리가 할 수 있고, 더블 쿼테이션 문자열에서는 식의 전개도 할 수 있다면, 싱글 쿼테이션 문자열은 어떤식으로 쓸 수가 있을까요? 싱글 쿼테이션은 입력한 문자를 이스케이프하지 않고 "그대로" 사용하고 싶을 때 편리합니다. 예를 들어 "백슬래시"의 문자는, 개행문자 `\n` 와 마찬가지로 많은 시스템상에서 특수문자로써 다루어집니다. 또한 싱글 쿼테이션으로 문자열을 감싸면, 간단하게 백슬래시문자와 같은 특수문자를 그대로 변수에 넣을 수 있습니다.

```
>> '\n'       # '백슬래시 n' 그대로 다룬다.
=> "\\n"
```

앞에서 말씀드린 `#{}` 문자와 같이, Ruby에서 백슬래시 그 자체를 이스케이프하기 위해서는, 백슬래시가 하나 더 필요합니다. 더블 쿼테이션 문자열 안에서는, 백슬래시 문자 그 자체는 두개의 백슬래시에 의해 표시됩니다. 이러한 예제와 같은 경우에는 큰 문제는 되지 않습니다만, 다음과 같이 이스케이프가 필요한 문자가 대량으로 발생한 경우에는, 싱글 쿼테이션이 매우 편리합니다.

```
>> 'Newlines (\n) and tabs (\t) both use the backslash character \.'
=> "Newlines (\\n) and tabs (\\t) both use the backslash character \\."
```

 마지막으로 한 번 더 말씀드리겠습니다. 대부분의 경우, 싱글쿼테이션과 더블쿼테이션 양쪽 다 사용해도 큰 차이는 없습니다. 실제로 일반적인 소스코드에서는 명확한 이유가 없이, 양쪽이 혼용되어 사용하는 케이스도 많습니다. 이상으로 Ruby의 문자열에 관한 설명을 끝내겠습니다. Ruby의 세계에 오신 것을 환영합니다!

##### 연습

1. `city` 변수에 적절한 행정구역을 할당하시고, `prefecture`  변수에 적절한 행정구역을 대입해보세요.
2. 앞서 만든 변수와, 식의 전개를 이용하여 "서울시 강남구" 와 같은 주소의 문자열을 만들어 보세요. 출력할 때에는 `puts` 사용하세요.
3. 위 문자열 사이에 반각 스페이스를 탭으로 바꾸어보세요. (*Hint*: 개행문자와 마찬가지로, 탭도 특수문자입니다.)
4. 탭으로 바꾼 문자열을, 더블 쿼테이션에서 싱글 쿼테이션으로 바꾸어보면 어떻게 되나요?

### 4.2.3 오브젝트 메세지의 송수신

Ruby에서는, 거의 모든 것이 `오브젝트` 입니다. 문자열이나 *nil* 까지 오브젝트입니다. 여기서의 기술적인 의미는 [4.4.2](#442-Class의-상속) 에서 설명합니다만, 오브젝트에 관한 의미나 정의는, 책을 읽어보기만 해서는 이해하기 어렵습니다. 많은 오브젝트의 예시를 경험하는 것으로, "오브젝트란 무엇인가" 라는 직감을 기를 필요가 있습니다.

반대로, 오브젝트에 무엇인가를 설명하는 것은 간단합니다. 오브젝트라는 것은 (어떠한 경우에도) 메세지에 응답하는 것입니다. 문자열과 같은 오브젝트는, 예를 들어 `length` 라고 하는 메세지에 응답할 수 있습니다. 이 것은 문자열의 문자수를 반환합니다.

```ruby
>> "foobar".length        # 문자열에 "length" 라고 하는 메세지를 보냄.
=> 6
```

오젝트에 넘겨지는 메세지는, 일반적으로는 *메소드* 입니다. 메소드는, 해당 오브젝트 안에 정의되어진 메소드입니다. Ruby의 문자열은 다음과 같이 `empty?` 메소드에도 응답할 수 가 있습니다. 

```ruby
>> "foobar".empty?
=> false
>> "".empty?
=> true
```

`empty?` 메소드의 말미에 있는 물음표에 주목해주세요. Ruby에서 메소드가 `true` 혹은 `false`  라고 하는 논리식(*boolean*) 을 반환하는 것을, 말미의 물음표를 붙이는 관습이 있습니다. 논리값은 특히 제어의 흐름을 변경할 때 유용합니다.

```ruby
>> s = "foobar"
>> if s.empty?
>>   "The string is empty"
>> else
>>   "The string is nonempty"
>> end
=> "The string is nonempty"
```

또한, 논리식은 각각 `&&`(and) 나 `||`(or), `!`(not) 오퍼레이터로 나타낼 수 있습니다.

```ruby
>> x = "foo"
=> "foo"
>> y = ""
=> ""
>> puts "Both strings are empty" if x.empty? && y.empty?
=> nil
>> puts "One of the strings is empty" if x.empty? || y.empty?
"One of the strings is empty"
=> nil
>> puts "x is not empty" if !x.empty?
"x is not empty"
=> nil
```

Ruby에서는, 대부분이 오브젝트입니다. 따라서, ` nil` 또한 오브젝트이며, 이것 또한 많은 메소드를 다룹니다. 거의 대부분의 오브젝트를 문자열로 변환하는 ` to_s` 메소드를 사용하여,  nil을 문자열로 변환해봅니다.

```ruby
>> nil.to_s
=> ""
```

확ㄹ하게 빈 문자열이 출력되었습니다. 이번에는 *nil* 에 대한 메소드를 `chain` 형태로 넘겨봅시다.

```ruby
>> nil.empty?
NoMethodError: undefined method `empty?' for nil:NilClass
>> nil.to_s.empty?      # 메소드 체인
=> true
```

이렇게 `nil` 오브젝트 자신은 `empty?` 메소드를 사용할 수 없습니다만, `nil.to_s` 를 하면, 사용할 수 있게 됩니다.

여러분들이 관찰한 대로, 오브젝트가 `nil` 인지 아닌지 확인하는 메소드도 있습니다.

```ruby
>> "foo".nil?
=> false
>> "".nil?
=> false
>> nil.nil?
=> true
```

다음 코드는,

`puts "x is not empty" if !x.empty?`

`if` 키워드의 다른 사용법을 표현하고 있습니다 .Ruby에서는 이렇게 뒤에 오는 `if` 의 조건식이 참일때 만 실행할 수 있는 식을 작성하는 것이 가능합니다. 코드가 매우 간결하게 되며 `unless` 도 표현할 수 있습니다.

```ruby
>> string = "foobar"
>> puts "The string '#{string}' is nonempty." unless string.empty?
The string 'foobar' is nonempty.
=> nil
```

Ruby에서 `nil` 은 특별한 오브젝트입니다. Ruby의 오브젝트 중, 오브젝트 그 자체의 값이 논리값이 false가 되는 것은, `false` 와 `nil` 2개 밖에 없습니다. 또한 "`!!`" 라는 연산자를 사용하면, 그 오브젝트를 2번 부정하는 것이 되기 때문에, 어떠한 오브젝트도 강제적으로 논리값을 변환할 수 있습니다.

```ruby
>> !!nil
=> false
```

이 외에 많은 Ruby오브젝트는 0 또한 *true* 를 반환합니다.

```ruby
>> !!0
=> true
```

##### 연습

1. "racecar" 의 문자열의 길이는 몇입니까? `length` 메소드를 사용하여 알아봅시다.
2. `reserve` 메소드를 사용하여 ,"racecar" 의 문자열을 거꾸로 읽으면 어떻게되는지 알아봅시다.
3. 변수 `s` 에 "racecar" 를 대입해봅시다. 그 후에 비교연산자 (`==`) 를 사용하여 변수 `s` 와 `s.reverse` 가 같은 값인지 확인해봅시다.
4. 아래의 코드를 실행해보면, 어떠한 결과가 나옵니까? 변수 `s` 에 [onomatopoeia](http://www.dictionary.com/browse/onomatopoeia) 라는 단어를 대입하면 어떻게 됩니까? *Hint* : 윗 화살표 (혹은 Ctrl+P 커맨드) 를 사용하여 커맨드를 재실행하면, 다시 입력할 필요가 없습니다.

```ruby
>> puts "It's a palindrome!" if s == s.reverse
```



### 4.2.4 Method의 정의

Rails의 콘솔에서도 Home액션이나 `full_title` 헬퍼와 같은 방법으로 메소드를 정의할 수 있습니다. (메소도의 정의는 파일에서 하는 것이 보통이므로, 콘솔에서 하는 것은 조금은 귀찮지만 간단한 것 정도는 가능합니다.) 예를 들어 `parameter` 1개를 받아 `parameter` 가 비어있는지 어떤지를 확인하는 *string_message* 라는 메소드를 정의해봅시다.

```ruby
>> def string_message(str = '')
>>   if str.empty?
>>     "It's an empty string!"
>>   else
>>     "The string is nonempty."
>>   end
>> end
=> :string_message
>> puts string_message("foobar")
The string is nonempty.
>> puts string_message("")
It's an empty string!
>> puts string_message
It's an empty string!
```

 마지막의 예시를 보시면 아시겠지만, 메소드의 파라미터를 생략할 수도 있습니다. (괄호도 생략 가능합니다) 이것은 다음 코드

`def string_message(str = '')`

는 파라미터에 기본값을 포함하고 있기 때문입니다.(이 예시에서 디폴트값은 빈 문자열입니다.) 이렇게 지정해놓으면 `str` 변수에 파라미터를 지정하는 것도, 지정하지 않는 것도 가능합니다. 파라미터를 넘기지 않는 경우에는 지정된 디폴트 값이 자동으로 설정됩니다.



여기서 Ruby의 메소드에는 "*암묵적인 리턴값이 있다*" 라는  것에 주의해주세요. 이것은 메소드 내부에서 평가된 식의 값이 자동적으로 리턴된다는 것을 의미합니다. 위의 예시의 경우 파라미터 `str` 이 비어있는지 아닌지에 따라 두 가지의 메세지 문자열 중에 하나를 리턴합니다. 물론 Ruby에서는 리턴값을 명시적으로 지정하는 것도 가능합니다. 다음 메소드는 위의 코드와 같은 결과를 리턴합니다.
```ruby
>> def string_message(str = ‘’)
>>   return “It’s an empty string!” if str.empty?
>>   return “The string is nonempty.”
>> end
```

위 설명에서 눈치채신 분도 있을 지도 모르겠습니다만, 2번째의 `return` 은 사실 없어도 그만입니다. 메소드 내부의 마지막에 있는 식은(위 경우에는 `"The string is nonempty."` )  `return` 키워드가 없어도 암묵적으로 값을 리턴합니다. 여기서는 양쪽에 `return` 을 사용하는 것이 대칭성이 있고 보기 쉽기 때문에 바람직하다고 할 수 있겠습니다.

메소드에서 파라미터의 변수 이름에 어떠한 이름을 사용하여도, 메소드를 호출하는 쪽에서는 아무런 영향이 없다는 점을 알아두세요. 즉 맨 첫 줄의 `str` 을 다른 변수명 (`the_function_argument` 등) 으로 변경하여도 메소드를 호출하는 입장에서는 똑같이 동작합니다.
```ruby
>> def string_message(the_function_argument = ‘’)
>>   if the_function_argument.empty?
>>     “It’s an empty string!”
>>   else
>>     “The string is nonempty.”
>>   end
>> end
=> :string_message
>> puts string_message(“”)
It’s an empty string!
>> puts string_message(“foobar”)
The string is nonempty.
```

##### 연습

1. 아래의 `FILL_IN` 부분을 적절한 코드로 바꾸고, 리턴문이 어떠한 것이 리턴되는지 확인해보세요. 첫 번째 줄은 리턴되고, 두번 째 줄은 리턴이 안되면 성공입니다.
2. 위에서 정의한 메소드를 사용하여,  “racecar” 와 “onomatopoeia” 가 리턴되는지 어떤지 확인해보세요. 첫 번째는 리턴이 되고, 두 번째는 리턴이 안되면 성공입니다.
3. `palindrome_tester("racercar")` 에 대해 `nil?` 메소드를 사용하여, 리턴값이 `nil` 인지 아닌지 확인해보세요. (즉, `nil?` 을 호출한 결과가 `true` 인지를 확인하는 것_ 이 메소드 체인은, `nil?` 메소드가 아래의 메소드의 리턴값을 받아 그 결과를 내보낸다는 의미입니다.



### 4.2.5 다시 한 번 Title Helper

이 것으로 `full_title` 헬퍼 의 코드를 이해하기 위한 준비를 끝냈습니다. 코멘트를 사용하여 각 행이 어떠한 처리를 하는지 코멘트를 달아봅시다.
```ruby
module ApplicationHelper

  # 각 페이지 당 완전한 페이지 타이틀을 리턴합니다.               # 코멘트 행
  def full_title(page_title = ‘’)                     # 메소드 정의와 파라미터
    base_title = “Ruby on Rails Tutorial Sample App”  # 변수 대입
    if page_title.empty?                              # 논리값 확인
      base_title                                      # 암묵적 리턴값
    else 
      page_title + “ | “ + base_title                 # 문자열의 결합
    end
  end
end
```

Web사이트의 레이아웃에서 사용하는 컴팩트한 헬퍼 메소드는, 메소드의 정의, 파라미터의 할당, 논리값 확인, 컨트롤 플로우, 문자열의 식 전개등 Ruby의 많은 요소가 포함되어 있습니다. 마지막으로 `module ApplicationHelper` 이라고 하는 요소에 대해 설명하겠습니다. 모듈은 관련된 메소드를 모아놓은 단위입니다. `include` 메소드를 사용하여 모듈을 읽어들이는 것이 가능합니다. (*mix in* 이라고도 불립니다.) 간단히 Ruby의 코드를 작성하는 것이라면, 모듈을 작성할 때마다 명시적으로 선언하여 읽어들이는 것이 보통입니다만, Rails에서는 자동적으로 헬퍼 모듈을 읽어들이기 때문에, `include` 키워드를 사용할 필요는 없습니다. 즉 `full_title` 메소드는, 자동적으로 모든 뷰에서 사용하는 것이 가능하다는 것입니다.



## 4.3 다른 데이터 구조

Web 어플리케이션은 단순한 문자열의 집합에 지나지 않습니다만,  실제로는 이러한 문자열을 만들기 위한 문자열 이외의 데이터 구조도 필요합니다. 이번 섹션에서는 Rails 어플리케이션을 작성하기 위해 중요한 몇가지 Ruby의 데이터 구조에 대해 설명하겠습니다.

### 4.3.1 배열과 범위연산자
배열(Array) 은, 특정의 순서를 가진 요소의 리스트입니다. Rails Tutorial에서는 지금까지 배열에 대해 설명드리진 않았습니다만, 배열을 이해하는 것 해시(4.3.3) 이나 Rails의 데이터모델을 이해하기 위해 중요한 기반이 됩니다. (데이터 모델이란 `has_many` 등의 관련 지어서 12.3.3이나 13.1.3에서 자세히 설명하겠습니다.)

Ruby의 문자열을 이해하는데에 많은 시간을 사용하였기 때문에, 다음 내용으로 진행해보겠습니다. `split` 메소드를 사용하면, 문자열을 자연스럽게 변환한 배열을 얻을 수 있습니다.
```ruby
>>  “foo bar     baz”.split     # 문자열을 3개의 요소로 분할
=> [“foo”, “bar”, “baz”]
```
이 조작에 의해 3개의 문자열로 구성된 배열을 얻을 수 있습니다. `split` 으로 문자열을 잘라내어 배열을 할 때에는 공백이 디폴트로 설정되어 있습니다만, 다으모가 같이 다른 문자를 지정하여  잘라낼 수 있습니다.

```ruby
>> “fooxbarxbaz”.split(‘x’)
=> [“foo”, “bar”, “baz”]
```

많은 프로그래밍 언어와 마찬가지로, Ruby의 배열은 제로오리진 을 채용하고 있습니다. 배열의 맨 첫번째 인덱스가 0부터 시작하고 2번째는 1. 2, 3,… 식으로 이어지는 것을 의미합니다.

```ruby
>> a = [42, 8, 17]
=> [42, 8, 17]
>> a[0]               # Ruby에서는 각 괄호를 이용하여 배열의 요소에 접근할 수 있다.
=> 42
>> a[1]
=> 8
>> a[2]
=> 17
>> a[-1]              # 배열의 인덱스는 마이너스를 지정할 수도 있다.
=> 17
```

 위에서 표시했듯이, 배열의 요소에 접근하기 위해서는 각괄호를 사용합니다. Ruby에서는 각괄호 이외에도 배열의 요소를 접근하기 위한 방법을 제공하고 있습니다.

```ruby
>> a                  # 배열 a의 내용을 확인한다.
=> [42, 8, 17]
>> a.first
=> 42
>> a.second
=> 8
>> a.last
=> 17
>> a.last == a[-1]    # 이퀄기호 2개를 이용하여 비교할 수 있음.
=> true
```

마지막 줄에서는 같은 것을 확인하는 비교연산자  `==` 을 사용해보았습니다. 이 연산자나 `!=` (같지 않음) 등의 연산자는 다른 많은 프로그래밍 언어와 같습니다.

```ruby
>> x = a.length       # 배열도 문자열과 마찬가지로, length를 사용할 수 있다.
=> 3
>> x == 3
=> true
>> x == 1
=> false
>> x != 1
=> true
>> x >= 1
=> true
>> x < 1
=> false
```

배열은, `lenght` 이외에도 많은 여러가지 메소드를 사용할 수 있습니다.

```ruby
>> a
=> [42, 8, 17]
>> a.empty?
=> false
>> a.include?(42)
=> true
>> a.sort
=> [8, 17, 42]
>> a.reverse
=> [17, 8, 42]
>> a.shuffle
=> [17, 42, 8]
>> a
=> [42, 8, 17]
```

위의 어떤 메소드를 실행시켜도 `a` 자체는 변경되지 않는 점을 주목해주세요. 배열의 내용을 *변경* 하고 싶을 때에는, 해당 메소드에 대응하는 “강제적” 메소드를 사용합니다. “강제 메소드”의 이름에는, 원래 메소드 마지막에 느낌표(!) 를 붙여 사용해주세요.

```ruby
>> a
=> [42, 8, 17]
>> a.sort!
=> [8, 17, 42]
>> a
=> [8, 17, 42]
```

또한, `push` 메소드 (혹은 같은 기능의 `<<` 연산자를 사용하여 배열에 요소를 추가할 수도 있습니다.

```ruby
>> a.push(6)                  # 6을 배열에 추가한다.
=> [42, 8, 17, 6]
>> a << 7                     # 7을 배열에 추가한다.
=> [42, 8, 17, 6, 7]
>> a << “foo” << “bar”        # 배열에 연속하여 추가한다.
=> [42, 8, 17, 6, 7, “foo”, “bar”]
```

마지막 라인에서는 요소의 추가를 체인으로 연결하고 있는 것을 볼 수 있습니다. 많은 프로그래밍 언어의 배열과는 다르게, Ruby에서는 다른 데이터형을 배열 안에 넣을 수 있습니다. (위 예시의 경우 정수와 문자열)

위에서는 문자열을 배열에 변환하는 `split` 을 사용하였습니다. `join` 메소드는 거꾸로 동작합니다.

```ruby
>> a
=> [42, 8, 17, 6, 7, “foo”, “bar”]
>> a.join                       # 単純に連結する
=> “4281767foobar”
>> a.join(‘, ‘)                 # カンマ+スペースを使って連結する
=> “42, 8, 17, 6, 7, foo, bar”
```

범위(range) 는, 배열과 밀접한 관계를 가지고 있습니다. `to_a` 메소드를 사용하여 배열로 변환하면, 이해하기 쉬울 것입니다.

```
>> 0..9
=> 0..9
>> 0..9.to_a              # 9에 대해 to_a를 호출하고 있습ㄴ디ㅏ.
NoMethodError: undefined method `to_a’ for 9:Fixnum
>> (0..9).to_a            # 둥근 괄호를 사용하여 범위 오브젝트로서 to_a 메소드를 호출합니다.
=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

`0..9` 는 범위로써 유효합니다만, 위 2번째 코드에서는 메소드를 호출 할 때에 괄호로 감싸줄 필요가 있다는 걸 알 수 있습니다.

범위는 배열의 요소를 꺼낼 때 편리합니다.

```ruby
>> a = %w[foo bar baz quux]         # %wを使って文字列の配列に変換
=> [“foo”, “bar”, “baz”, “quux”]
>> a[0..2]
=> [“foo”, “bar”, “baz”]
```

인덱스를 -1로 지정할 수 있는 것은 매우 편리합니다. -1을 사용하면, 배열의 길이를 몰라도 배열의 맨 마지막 요소를 지정할 수 있으며, 이것으로 인해 배열을 특정 시작 위치의 요소로부터 맨 마지막 요소까지 한 번에 선택할 수도 있습니다.

```ruby
>> a = (0..9).to_a
=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>> a[2..(a.length-1)]               # 명시적으로 배열의 길이를 이용하여 요소 선택.
=> [2, 3, 4, 5, 6, 7, 8, 9]
>> a[2..-1]                         # 인덱스에 -1를 사용하여 선택
=> [2, 3, 4, 5, 6, 7, 8, 9]
```

다음과 같이 문자열에 대해서도 범위 오브젝트로 다룰 수 있습니다.

```ruby
>> ('a'..'e').to_a
=> ["a", "b", "c", "d", "e"]
```

##### 연습
1. 문자열 “A man, a plan, a canal, Panama” 를 “, “ 로 분할하여 배열하여 변수 a로 대입해 보세요.
2. 다음은, 변수 `a` 의 요소를 연결한 결과(문자열) 을 변수 `s` 에 대입해봅시다.
3. 변수 `s`  를 반각 스페이스로 분할 한 다음, 한 번더 연결하여 문자열로 만들어 봅시다. (*Hint*: 메소드 체인을 이용하면 한 줄로도 가능합니다. ) 리턴문을 체크하는 메소드를 사용하여 변수 `s`의 리턴값을 확인해봅시다. `downcase` 메소드를 사용하여 `s.downcase`는 리턴값이 있는 메소드라는 것을 확인해봅시다.
4. `a`에서 `z`까지의 범위 오브젝트를 작성하고, 7번째의 요소를 꺼내어보세요. 마찬가지로 뒤에서부터 7번째 요소를 꺼내어 보세요. (*Hint*: 범위 오브젝트를 배열로 변환하는 것을 잊지마세요.)

### 4.3.2 블록
배열과 범위는 *블록* 을 포함하는 여러가지 메소드에 대해 대응할 수 있습니다. 블록은, Ruby의 매우 강력한 기능이기도하며 이해하기 어려운 기능이기도 합니다.
```ruby
>> (1..5).each { |i| puts 2 * i }
2
4
6
8
10
=> 1..5
```
위 코드에서, 범위 오브젝트인 `(1..5)` 에 대해 `each` 메소드를 호출하고 있습니다. 메소드에 전달되는 `{ |i| puts 2 * i }` 는 **블록** 이라고 불리는 부분입니다. `|i|` 에서는 변수를 파이프 ( | ) 로 감싸고 있습니다만, 이것은 블록변수를 다루는 Ruby의 문법입니다. 블록을 조작할 때에 사용하는 변수를 지정합니다. 이 경우, 범위 오브젝트의 `each` 메소드는 `i` 라고 하는 하나의 로컬변수를 사용하여 블록을 조작할 수 있습니다.  그리고 범위에 포함되는 각각의 값을 `i` 변수에 대입하여 블록을 실행합니다.

블록이라는 것을 나타내기 위해선 () 괄호를 사용하여 감쌉니다만, 다음과 같이 `do` 와 `end` 로 감싸서 표현할 수 있습니다.

```ruby
>> (1..5).each do |i|
?>   puts 2 * i
>> end
2
4
6
8
10
=> 1..5
```

블록에서는 여러개의 행을 기술할 수 있습니다. (실제 대부분의 블록은 여러 행으로 이루어져 있습니다.) *Rails Tutorial* 에서는 Ruby 공통 관습에 따라, 짧은 1행의 블록에는 () 괄호를 사용하고, 긴 1행이나 여러 행의 블록에는 `do..end` 문법을 사용합니다.

```
>> (1..5).each do |number|
?>   puts 2 * number
>>   puts '--'
>> end
2
--
4
--
6
--
8
--
10
--
=> 1..5
```

이번에는 `i` 대신에 `number` 를 사용하는 것을 주목해주세요. 이 변수 (블록변수) 의 이름은 반드시 `i` 를 사용해야하는 것은 아닙니다.

블록은 보기와는 달리 쓰임새가 많아 블록을 충분하게 이해하기 위해서는 상당한 프로그래밍경험이 필요합니다. 이해를 하기 위해서는 블록을 포함한 코드를 많이 읽어서 이해할 수 밖에 없습니다. 다행스럽게도 인간에게는 각각의 사례를 일반화하는 능력이 잇습니다. 참고를 위해서 `map` 메소드 등을 사용한 블록의 사용예를 몇 가지 보여드릴까 합니다.
```ruby
>> 3.times { puts "Betelgeuse!" }   # 3.timesではブロックに変数を使っていない
"Betelgeuse!"
"Betelgeuse!"
"Betelgeuse!"
=> 3
>> (1..5).map { |i| i**2 }          # 「**」記法は冪乗 (べき乗) 
=> [1, 4, 9, 16, 25]
>> %w[a b c]                        # %w で文字列の配列を作成
=> ["a", "b", "c"]
>> %w[a b c].map { |char| char.upcase }
=> ["A", "B", "C"]
>> %w[A B C].map { |char| char.downcase }
=> ["a", "b", "c"]
```
위 코드와 같이, `map` 메소드는 주어진 블록을 배열이나 범위 오브젝트의 각 요소에 대해 적용하여, 그 결과를 리턴합니다. 또한 마지막 2개의 코드는 `map` 의 블록 내부에 선언한 파라미터 (char) 에 대해 메소드를 호출하고 있습니다. 이러한 케이스에서는 생략기법이 일반적으로, 다음과 같이 작성하는 것도 가능합니다. (이러한 기법을 *symbol-to-proc* 이라고도 합니다.)

```ruby
>> %w[A B C].map { |char| char.downcase }
=> [“a”, “b”, “c”]
>> %w[A B C].map(&:downcase)
=> [“a”, “b”, “c”]
```

(메소드 이름에 *심볼* 이 쓰이고 있습니다. 이상하게 보일 수도 있습니다. 이것에 대해서는 4.3.3에서 설명합니다.) 한 가지 재미있는 이야기를 할까 합니다. 이것은 사실은 원래 Ruby on Rails 의 독자적인 문법이었습니다. 그러나 많은 사람이 이러한 문법을 편해했기 때문에, 현재는 Ruby의 코어기능으로써 도입되었습니다.

마지막으로, Unit Test에도 주목해봅시다.
```ruby
test “should get home” do
  get static_pages_home_url
  assert_response :success
  assert_select “title”, “Ruby on Rails Tutorial Sample App”
end
```

여기서는 동작을 세세하게 이해할 필요는 없습니다.(실제로 필자도 해당 코드를 한눈에 완벽하게 파악할 수 있다고는 말할 수 없습니다.) 여기서 중요한 점은, 테스트 코드에 `do` 라고 하는 키워드가 사용되고 있는 점이고, 거기에서 테스트 코드가 “애당초 블록으로 되어있다.” 라는 것입니다. 즉 해당 `test` 메소드는 문자열(설명문)과 블록을 파라미터로 삼아 테스트를 실행할 때 블록 내부의 문법이 실행되는 것을 이해할 수 있을 것입니다.

1.5.4 에서 랜덤한 서브도메인을 생성하기 위해, 다음 Ruby코드를 소개해드린 적이 있었습니다. 이 코드를 이해하기 위한 준비가 끝났기 때문에, 다시 한 번 더 읽어봅시다.

`('a'..'z').to_a.shuffle[0..7].join`

실행 순서를 확인해보면, 조금은 이해하기 쉬워질 것 입니다.

```ruby
>> ('a'..'z').to_a                     # 영어로 이루어진 배열을 작성
=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o",
"p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z"]
>> ('a'..'z').to_a.shuffle             # 순서를 셔플
=> ["c", "g", "l", "k", "h", "z", "s", "i", "n", "d", "y", "u", "t", "j", "q",
"b", "r", "o", "f", "e", "w", "v", "m", "a", "x", "p"]
>> ('a'..'z').to_a.shuffle[0..7]       # 배열의 앞 8개 요소를 추려낸다
=> ["f", "w", "i", "a", "h", "p", "c", "x"]
>> ('a'..'z').to_a.shuffle[0..7].join  # 추려낸 요스를 하나의 문자열로 만든다.
=> "mznpybuj"
```

##### 연습
1. 범위 오브젝트 `0..16` 를 사용하여 각 요소에 2승을 출력해보세요.
2. `yeller`( 큰 소리를 내다 ) 라고 하는 메소드를 정의해보세요. 이 메소드는, 문자열의 요소로 구성되어진 배열을 파라미터로 받고, 각 요소를 결헙한 후, 대문자로 하여 결과를 리턴합니다. 예를들어 `yeller([‘o’, ‘l’, ‘d’])` 라고 실행할 때, “OLD” 라고 하는 결과가 리턴된다면 성공입니다. *Hint*: `map` 과 `upcase`, `join` 메소드를 사용해봅시다.
3. `random_subdomain` 이라고 하는 메소드를 정의해보세요. 이 메소드는 랜덤한 8개의 문자를 생성하고, 문자열로 리턴합니다. *Hint*: 서브 도메인을 작성할 때 사용한 Ruby코드를 메소드화 한 것입니다.
4. 아래의 코드의 “?” 부분을, 각각 적절한 메소드로 바꾸어보세요. *Hint*: `split`, `shuffle`, `join` 메소드를 조합하면, 메소드에 넘겨진 문자열(파라미터)를 셔플할 수 있습니다.

```ruby
>> def string_shuffle(s)
>>   s.?(‘’).?.?
>> end
>> string_shuffle(“foobar”)
=> “oobfra”
```



### 4.3.3 Hash와 Symbol

해시는 본질적으로는 배열과 같습니다만, 인덱스로 정수값 이외의 값도 사용할 수 있는 점이 배열과 다릅니다. ( 이 이유때문에, Perl등의 다른 언어에서는 해시를 *연상배열* 이라고 부르기도 합니다.)  해시의 인덱스 (Key라고 부르는 것이 보통) 는, 보통은 어떠한 오브젝트의 형태입니다. 예를 들어, 다음과 같은 문자열을 Key로 사용합니다.

```ruby
>> user = {}                          # {}는 빈 해시
=> {}
>> user[“first_name”] = “Michael”     # Key는 “first_name” . Value는 “Michael”
=> “Michael”
>> user[“last_name”] = “Hartl”        # Key는 “last_name”. Value는 “Hartl”
=> “Hartl”
>> user[“first_name”]                 # 요소에 접근하는 것은 배열과 비슷함.
=> “Michael”
>> user                               # 해시의 내용 표현
=> {“last_name”=>”Hartl”, “first_name”=>”Michael”}
```

해시는, key값에 페어값을 대괄호{} 로 감싸서 표시합니다. 키와 밸류의 페어를 가지지 않은 대괄호는 (`{}`)  빈 해시입니다. 여기서 중요한 점은, 해시의 대괄호는, 블록의 대괄호와 전혀 다른 것이라는 점입니다. (이 것은 조금은 헷갈릴 수도 있는 부분입니다.) 해시는 배열과 비슷합니다만, 한 가지 중요한 점이 다릅니다. 그것은 해시에서는 요소의 “순서”가 보장되어 있지 않다는 점입니다. 만약 요소의 순서를 중요시한다면, 배열을 사용할 필요가 있습니다.

해시의 1개 요소를 괄호를 사용하여 정의하는 대신에, 다음과 같이 키와 밸류를 해시로켓 `=>` 를 이용하여 표현하는 것이 간단합니니다.
```ruby
>> user = { "first_name" => "Michael", "last_name" => "Hartl" }
=> {"last_name"=>"Hartl", "first_name"=>"Michael"}
```

여기서는 Ruby에서의 관습으로써 해시의 제일 첫 번째 값과 제일 마지막 값에는 공백을 추가합니다. 이 공백은 있어도 없어도 그만이며, 콘솔에서는 무시되어 표시됩니다. (어째서 스페이스를 놓게되었는지는 필자도 잘 모르겠습니다. 아마도 초기의 유명한 Ruby프로그래머가 선호한 결과, 관습이 되어버렸다고 생각합니다.)

여기까지는 해시의 키로써 문자열을 사용했습니다만, Rails에서는 문자열보다도 심볼을 사용하는 편이 보통입니다. 심볼은 문자열과 닮아 있습니다만, 쿼테이션으로 감싸는 대신에, 콜론을 앞에 놓는 점이 다릅니다. 예를 들어, `:name` 은 심볼입니다. 물론 불필요한 것은 생각하지마시고, 일단 심볼을 단순한 문자열이라고 생각해도 상관없습니다.

```ruby
>> “name”.split(‘’)
=> [“n”, “a”, “m”, “e”]
>> :name.split(‘’)
NoMethodError: undefined method `split’ for :name:Symbol
>> “foobar”.reverse
=> “raboof”
>> :foobar.reverse
NoMethodError: undefined method `reverse' for :foobar:Symbol
```

심볼은, Ruby이외에서는 매우 소수의 언어에서밖에 쓰지이 않는 특수한 데이터 형식입니다. 처음에는 신기하게 생각할 수도 있겠습니다만, Rails에서는 심볼을 매우 흔하게 사용하고 있기 때문에, 익숙해지도록 합시다. 문자열과는 조금 다르게, 모든 문자를 사용할 수 있는 것은 아니기에, 조심하도록 합시다.

```ruby
>> :foo-bar
NameError: undefined local variable or method `bar’ for main:Object
>> :2foo
SyntaxError
```

그렇다고는 하나, 일반적인 알파벳등은 사용할 수 있기 때문에 곤란한 일은 없을 것입니다.

해시의 키로 심볼을 쓰는 경우에는, `user` 의 해시는 다음과 같이 정의할 수 있습니다.
```ruby
>> user = { :name => “Michael Hartl”, :email => “michael@example.com” }
=> {:name=>”Michael Hartl”, :email=>”michael@example.com”}
>> user[:name]              # :name 의 밸류값을 액세스.
=> “Michael Hartl”
>> user[:password]          # 정의되지 않은 키의 밸류값에 액세스.
=> nil
```

마지막의 예씨를 보면, 정의되지 않은 해시값은 단순하게 `nil` 이라는 것을 알 수 있습니다.

해시에서는 심볼을 키로 사용하는것이 일반적이기 때문에, Ruby 1.9 에서 이러한 특수한 경우를 위해 새로운 문법이 지원되기 시작되었습니다.
```ruby
>> h1 = { :name => "Michael Hartl", :email => "michael@example.com" }
=> {:name=>"Michael Hartl", :email=>"michael@example.com"}
>> h2 = { name: "Michael Hartl", email: "michael@example.com" }
=> {:name=>"Michael Hartl", :email=>"michael@example.com"}
>> h1 == h2
=> true
```

2번째 문법에서는 심볼과 해시로켓의 조합을 다음과 같이 키의 이름 이후 콜론을 놓고 그 다음에 값이 오도록 하였습니다.

```ruby
{ name: “Michael Hartl”, email: “michael@example.com” }
```

위의 구성에서는 JavaScript 등 다른 언어의 해시 기법과 매우 닮아 있어 Rails 커뮤니티에서도 인기가 높습니다. 어느쪽의 문법도 자주 사용되고 있는 문법이기 때문에, 두 가지 문법을 구분하는 것이 중요합니다. 첫 번째 문법이 알기 어려운 것도 사실입니다. 예를 들어 `:name` 은 심볼로써 독립된 심볼입니다만, 파라미터가 없는 `name:` 은 심볼 자체로 성립되지 않습니다. 다음의 코드 `:name =>` 와 `name:` 은 해시로써의 데이터구조는 완전 일치합니다. 즉

`{:name => “Michael Hartl”}`

과

`{name: “Michael Hartl”}`

은 동일한 코드라는 것입니다. (일반적으로는 생략기법이 바람직합니다만, 명시적으로 선두에 콜론을 붙여 사용하는 (`:name`) 것이 심볼이라고 하는 것을 강조하는 경향이 있습니다.)

아래의 코드와 같이, 해시의 키값에는 거의 모든 값을 넣을 수 있습니다. 다른 해시를 사용할 수도 있습니다.

```ruby
>> params = {}        # 'params' 라고 하는 해시 정의 ('parameters' の略)。
=> {}
>> params[:user] = { name: "Michael Hartl", email: "mhartl@example.com" }
=> {:name=>"Michael Hartl", :email=>"mhartl@example.com"}
>> params
=> {:user=>{:name=>"Michael Hartl", :email=>"mhartl@example.com"}}
>>  params[:user][:email]
=> "mhartl@example.com"
```

Rails에서는 이러한 해시의 해시 (혹은 네스트 해시)가 매우 많이 사용되고 있습니다. 실제의 사용 예시는 7.3 에서 설명합니다.

배열이나 범위 오브젝트와 마찬가지로, 해시에서도 `each` 메소드를 호출할 수 있습니다. 예를 들어 `:success` 와 `:danger` 라고 하는 2가지 상태를 가지는 `flash` 라고 하는 이름의 해시에 대해 생각해봅시다.

```ruby
>> flash = { success: "It worked!", danger: "It failed." }
=> {:success=>"It worked!", :danger=>"It failed."}
>> flash.each do |key, value|
?>   puts "Key #{key.inspect} has value #{value.inspect}"
>> end
Key :success has value "It worked!"
Key :danger has value "It failed."
```

여기서 배열의 `each` 메소드에서는, 블록의 파라미터가 1개뿐이었지만, 해시의 `each`에서는 블록의 파라미터는 키와 밸류 2개로 되어 있는 것을 알 수 있습니다. 따라서 해시에 대하여 `each` 메소드가 실해오디면, 해시의 1개의 “키와 밸류쌍” 단위로 처리를 반복합니다.

마지막으로, 편리한 `inspect` 메소드를 소개하겠습니다. 이 메소드는 요구된 오브젝트를 표현하는 문자열을 리턴합니다.

```ruby
>> puts (1..5).to_a            # 배열을 문자열로써 출력
1
2
3
4
5
>> puts (1..5).to_a.inspect    # 배열의 리터럴(고정값)을 출력
[1, 2, 3, 4, 5]
>> puts :name, :name.inspect
name
:name
>> puts “It worked!”, “It worked!”.inspect
It worked!
“It worked!”
```

오브젝트를 표시하기 위해 `inspect` 메소드를 사용하는 것은 매우 자주 있는 일이기 때문에 `p` 메소들 라고하는 숏컷기능도 있습니다.

```ruby
>> p :name             # 'puts :name.inspect' 와 같은 의미
:name
```

##### 연습 
1. 키가 `one, two, three` 가 있고, 각각의 밸류값으로 `uno, dos, tres` 가 있는 해시를 만들어보세요. 해시의 각 요소를 확인하고 각각의 키와 밸류값을 `'#{key}의 스페인어는 #{value}'` 의 형태로 출력해보세요.
2. `person1, person2, person3` 라고 하는 3개의 해시를 생성하고, 각각의 해시에 `:first`와 `:last` 키를 추가하여 적절한 값을 입력해보세요. `params` 이라고 하는 해시의 해시를 만들어 보세요.
	1. 키 `params[:father]` 의 값에 `person1` 를 대입.
	2. 키 `parmas[:mother]` 의 값에 `person2` 를 대입.
	3. 키 `params[:child]` 의 값에 `person3` 를 대입.
마지막으로 해시의 해시를 조사하여 제대로 된 밸류값이 들어있는지 확인해보세요. (예를 들어, `params[:father][:first]` 가 `person1[:first]` 과 일치하는지 확인해보세요.)
3. `user` 라고 하는 해시를 정의해보세요. 이 해시에는 3가지의 키값 `:name, :email, :password_degest` 를 가지고 있으며, 각각의 밸류값으로 여러분의 이름, 메일 주소, 16문자의 랜덤 문자열을 대입해보세요.
4. Ruby API를 사용하여 Hash클래스 `merge` 메소드에 대해 알아보세요. 다음의 코드를 실해앟지말고, 어떠한 결과가 리턴될지 추측하실 수 있으신가요? 추측할 수 있다면, 실제 코드를 실행하여 예상한 결과와 같은지 확인해봅시다.



```ruby
  { "a" => 100, "b" => 200 }.merge({ "b" => 300 })
```



### 4.3.4 다시 한 번 CSS

그렇다면, 다시 한 번 더 레이아웃에 CSS (Cascading Style Sheet) 를 추가하는 다음 코드를 봅시다.
```ruby
<%= stylesheet_link_tag 'application', media: 'all',
                                       'data-turbolinks-track': 'reload' %>
```

지금이라면 이 코드를 이해할 수 있게 되었을 것입니다. Rails에서는 스타일시트를 추가하기 위해 특별한 메소드를 사용하고 있습니다.
```ruby
stylesheet_link_tag ‘application’, media: ‘all’,
                                   ‘data-turbolinks-track’: ‘reload’
```

위 코드에서는, 아래의 메소드를 호출하고 있습니다. 그러나 여기서 조금 이상한 점들이 있습니다. 첫 번째로는 소괄호()가 쓰이지 않고 있습니다. 사실 Ruby에서는 소괄호를 사용해도, 사용하지 않아도 상관없습니다. 다음 2개의 코드는 똑같이 실행됩니다.

```ruby
# 메소드 호출 시의 소괄호는 생략가능.
stylesheet_link_tag('application', media: 'all',
                                   'data-turbolinks-track': 'reload')
stylesheet_link_tag 'application', media: 'all',
                                   'data-turbolinks-track': 'reload'
```

다음으로, `media` 파라미터는 해시와 비슷하게 보입니다만, 대괄호가 아닌 점이 이상합니다. 해시가 메소드 호출 시의 마지막 파라미터일 때에는 대괄호를 생략할 수 있습니다. 다음의 코드는 똑같은 결과입니다.

```ruby 
#  마지막 파라미터가 해시일 경우, 대괄호를 생략할 수 있습니다.
stylesheet_link_tag 'application', { media: 'all',
                                     'data-turbolinks-track': 'reload' }
stylesheet_link_tag 'application', media: 'all',
                                   'data-turbolinks-track': 'reload'
```

마지막으로 Ruby에서는 다음과 같은 코드를 정상적으로 실행되는 것이 신기한 점입니다.

```ruby
stylesheet_link_tag ‘application’, media: ‘all’,
                                   ‘data-turbolinks-track’: ‘reload’
```

위 코드는 도중에 개행이 포함되어있음에도 불구하고 실행되는 것이 신기합니다. Ruby는 개행과 공백을 구별하지 않습니다. 코드를 분할하는 이유는 1행에 80문자 이상 들어있는 소스코드의 경우, 읽기 어렵기 때문입니다.

따라서
```ruby
stylesheet_link_tag 'application', media: 'all',
                                   'data-turbolinks-track': 'reload'
```

위의 코드에서는 `stylesheet_link_tag` 메소드에는 2개의 파라미터가 넘겨지고 있습니다. 첫 파라미터의 문자열은, 스타일시트로의 패스가 표시되어있습니다. 다음 파라미터인 해시에는 2가지 요소가 있는데, 첫 번째 요소에는 미디어 타입을 나타내고, 그 다음 요소에는 Rails 4.0 에서 추가된  [turbolinks](https://github.com/rails/turbolinks) 라고 하는 기능을 On 하고 있습니다. 이 결과, <%= %> 로 감싸져있는 코드를 실행한 결과가 ERB의 템플릿에 삽입됩니다. 브라우저 상에서 위 페이지의 소스코드를 확인한다면, 필요한 스타일시트가 포함되어 있는 것을 확인할 수 있습니다. (CSS 파일이름 뒤에, `?body=1`과 같은 행이 추가되어 있을 때도 있습니다. 이것은 Rails에 의해 삽입되어진 것으로써 서버상에서 변경이 있는 경우, 브라우저가 CSS를 다시 읽어들일 때에 사용됩니다.)

```ruby
<link data-turbolinks-track="true" href="/assets/application.css"
media="all" rel="stylesheet" />
```

## 4.4 Ruby에서의 Class

Ruby에서는 대부분의 모든 것을 오브젝트라는 것은 이미 설명해드렸습니다만, 이번 섹션에서는 실제로 오브젝트를 몇가지 정의해보겠습니다. Ruby에서는 많은 오브젝트 지향언어와 마찬가지로, 메소드를 모아서 정의하는 것에 클래스를 사용합니다. 이 클래스로부터 인스터늣가 생성되는 것으로 오브젝트가 생성되는 것입니다. 오브젝트지행 프로그래밍의 경험이 없는 분들에게는 이 것이 무엇을 의미하는 것인지 알기 어려울 수도 있다고 생각하기 때문에, 몇 가지의 구체적인 예시를 들어보겠습니다.

### 4.4.1 Constructor
지금까지의 많은 예시들 중에서 클래스를 사용하여 오브젝트의 인스턴스를 생성해보았습니다만, 오브젝트를 생성하는 것을 명시적으로 설명드리진 않았습니다. 예를 들어 더블쿼테이션을 사용하여 문자열의 인스턴스를 작성했습니다만, 이 것은 문자열의 오브젝트를 암묵적으로 작성하는 리터럴 생성자 입니다.
```ruby
>> s = "foobar"       # 더블 쿼테이션은 사실 문자열의 생성자
=> "foobar"
>> s.class
=> String
```

위 코드에서는 문자열에서 `class` 메소드를 호출하고 있습니다. 해당 문자열이 소속되어 있는 클래스를 단순하게 리턴하는 것을 알 수 있습니다.

암묵적인 리터럴 생성자를 사용하는 대신, 명시적으로 같은, 이름이 있는 생성자를 사용하는 것도 가능합니다. 이 생성자는, 클래스이름에 대해 `new` 메소드를 호출합니다.

```ruby
>> s = String.new("foobar")   # 문자열의 new를 사용한 생성자
=> "foobar"
>> s.class
=> String
>> s == "foobar"
=> true
```

위 동작은 리터럴 생성자와 같습ㄴ디ㅏ. 동작의 내용이 명확하게 표시되어 있습니다.

배열도 문자열과 마찬가지로, 인스턴스를 생성할 수 있습니다.
```ruby 
>> a = Array.new([1, 3, 2])
=> [1, 3, 2]
```

단, 해시의 경우 조금 다릅니다. 배열의 생성자인 `Array.new` 는 배열의 초기값을 파라미터로 넘길 수 있습니다만, `Hash.new` 는 해시의 디폴트값을 파라미터로 넘깁니다. 키값이 존재하지 않는 경우의 디폴트 값입니다.

```ruby
>> h = Hash.new
=> {}
>> h[:foo]            # 존재하지 않는 키 (:foo) 에 대한 값을 참조
=> nil
>> h = Hash.new(0)    # 존재하지 않는 키의 디폴트값을 nil이 아닌 0으로 설정
=> {}
>> h[:foo]
=> 0
```

메소드가 클래스 자신 (이 경우에는 `new`) 에 대해 호출되었을 때는, 해당 메소드를 *클래스 메소드* 라고 합니다. 클래스의 `new` 메소드를 호출한 결과는 해당 클래스의 오브젝트이며, 클래스의 인스턴스라고도 불립니다.
`length` 처럼, 인스턴스에서 호출할 수 있는 메소드는 *인스턴스 메소드* 라고합니다.

##### 연습
1. 1부터 10까지 범위오브젝트를 생성하는 리터럴 생성자는 무엇입니까? (복습입니다.)
2. 이번에는 `Range` 클래스와 `new` 메소드를 사용하여, 1부터 10까지의 범위 오브젝트를 작성해보세요. *Hint*: 2개의 파라미터를 넘길 필요가 있습니다.
3. 비교 연산자 `==` 를 사용하여 위 2개의 과제에서 만들어진 각각의 오브젝트가 같다는 것을 확인해보세요.



### 4.4.2 Class의 상속

클래스에 대해 배울 때, *superclass* 메소드를 사용하여 클래스의 계층을 알아본다면, 이해가 쉬울 것입니다.
``` ruby
>> s = String.new("foobar")
=> "foobar"
>> s.class                        # 변수s의 클래스를 확인
=> String
>> s.class.superclass             # String클래스의 수퍼클래스를 확인
=> Object
>> s.class.superclass.superclass  # Ruby 1.9부터BasicObject가 도입
=> BasicObject
>> s.class.superclass.superclass.superclass
=> nil
```

상속계층은 이전 자료에서 설명드린바 있습니다. 여기서는 `String`  클래스의 슈퍼클래스는 `Object` 클래스이며, `Object` 클래스의 슈퍼클래스는 `BasicObject` 클래스입니다. `BasicObject` 클래스는 슈퍼클래스를 가지고 있지 않은 것을 알 수 있습니다. 아래의 도형에서는 모든 Ruby의 오브젝트에 있어서 성립합니다. 클래스계층을 거꾸로 거슬러 올라가면, Ruby에  있어서 모든 클래스는 최종적으로 슈퍼클래스를 가지고 있지 않은 `BasicObject`  클래스를 상속받습니다. 이것이 “Ruby에서는 대부분 모든 것이 오브젝트이다” 라는 것의 기술적인 의미입니다.

![](../image/Chapter4/string_inheritance_ruby_1_9.png)



클래스에 대해 깊은 이해를 하기 위해선, 자신이 직접 클래스를 만들어 보는 것이 제일 빠릅니다. `Word` 라는 클래스를 작성하고, 그 안에 어떠한 단어를 앞에서부터 읽어도 뒤에서부터 읽어도 똑같은 말이 된다면 `true`를 리턴하는 `palindrome?` 이라는 메소드를 작성해봅시다.
```ruby
>> class Word
>>   def palindrome?(string)
>>     string == string.reverse
>>   end
>> end
=> :palindrome?
```

이 클래스와 메소드는 다음과 같이 사용하는 것이 가능합니다.

``` ruby
>> w = Word.new              # Word오브젝트를 작성한다.
=> #<Word:0x22d0b20>
>> w.palindrome?("foobar")
=> false
>> w.palindrome?("level")
=> true
```

만 위의 예시가 조금 부자연스럽다고 생각한다면, 꽤나 날카로운 지적이라고 할 수 있습니다. 이것은 일부러 부자연스럽게 작성한 메소드이기 때문입니다. 문자열을 파라미터로 넘기는 메소드를 작성하기위해 일부러 새로운 클래스를 만드는 것은 바람직하지 않습니다. 단어는 문자열이기 때문에, 아래의 코드처럼, `Word` 클래스는 `String` 클래스를 상속받는 것이 자연스럽습니다. (다음 코드를 입력하기 전에, 방금 작성한 `Word`  클래스의 정의를 삭제하기 위해, 일단 Rails 의 콘솔을 종료해주세요.)

``` ruby
>> class Word < String             # Word클래스는 String클래스를 계승합니다.
>>   # 문자열이 뒤에서부터 읽어도 똑같다면, true를 리턴합니다.
>>   def palindrome?
>>     self == self.reverse        # self는 문자열 자신을 뜻합니다.
>>   end
>> end
=> :palindrome?
```

[3.2](Chapter3.md#32-정적인-페이지)에서도 간단하게 설명하였습니다만, 위의 코드는 상속을 설명하기위한, Ruby의 `Word < String ` 문법입니다. 이 것으로 새로운 `palindrome?` 이라는 메소드 뿐만 아니라, String클래스의 모든 메소드를 다룰 수 있게 되는 것입니다.

``` ruby
>> s = Word.new("level")    # 새로운 Word를 만들고"level" 로 초기화합니다.
=> "level"
>> s.palindrome?            # Word가 거꾸로 읽어도 같은 단어인지 확인합니다.
=> true
>> s.length                 # Word는String 클래스의 모든 메소드를 상속받습니다.
=> 5
```

`Word` 클래스는 `String` 클래스를 상속받고 있기 때문에, 콘솔을 사용하여 클래스 계층을 명시적으로 확인할 수도 있습니다.

``` ruby
>> s.class
=> Word
>> s.class.superclass
=> String
>> s.class.superclass.superclass
=> Object
```

![](../image/Chapter4/word_inheritance_ruby_1_9.png)



`Word` 클래스는, 단어의 문자를 거꾸로 한 것이, 원래의 단어와 같은지를 체크하기 위해, `Word` 클래스의 안에서 자기자신이 가진 단어를 접근하여 체크하는 것을 주목해주세요. Ruby에서는 `self` 키워드를 사용하여 지정하는 것이 가능합니다. `Word`  클래스의 안에서는, `self` 는 오브젝트 자기 자신을 가르킵니다. 즉 다음 코드를 사용하여

`self == self.reverse`

단어가 거꾸로해도 똑같은 단어인지 확인하고 잇는 것입니다. 또한 String 클래스의 내부에는 메소드나 속성을 호출할 때 `self.` 를 생략할 수 있습니다.

`self == reverse`

와 같은 생략문법도 사용할 수 있습니다.

##### 연습

1. Range클래스의 상속계층을 알아봅시다. 마찬가지로 Hash와 Symbol 클래스의 상속계층도 알아봅시다.
2. `self.reverse` 의 `self` 를 생략하고, `reverse` 를 써도 제대로 동작하는 것을 확인해봅시다.



### 4.4.3 기본 Class의 변경

상속은 강력한 개념입니다. 만약 상속을 사용하지 않고 `palindrome?` 메소드를 `String` 클래스 자체에 추가하여 (즉 String 클래스를 확장) 좀 더 자연스러운 방법으로 사용하고 싶다면, 일부러 Word클래스를 작성하지 않고 `palindrome?` 을 정수형 문자열에 대해 직접 실행할 수도 있을 것입니다. 그런 것이 가능할까요? (현재의 코드는 그렇게 되어있지 않기 때문에, 다음과 같은 에러가 출력됩니다.)

``` ruby
>> "level".palindrome?
NoMethodError: undefined method `palindrome?'  for "level":String
```

놀랍게도, Ruby에서는 기본으로 제공되는 기본 클래스의 확장이 가능합니다. Ruby의 클래스는 자유럽게 변경할 수 있으며, 클래스 설계자가 아닌 개발자라도 기본 클래스에 메소드를 자유롭게 추가하는 것이 가능합니다.

``` ruby
>> class String
>>   # 문자열이 거꾸로 읽어도 같은 문자열일 경우, true를 리턴
>>   def palindrome?
>>     self == self.reverse
>>   end
>> end
=> :String
>> "deified".palindrome?
=> true
```

기본으로 제공되는 클래스의 변경은, 매우 강력한 테크닉입니다만, 큰 힘에는 큰 책임이 따르기 마련입니다. 정말 그럴만한 이유가 없는 한, 기본으로 제공되는 클래스에 메소드를 추가하는 것은 바람직하진 않습니다. Ruby의 경우, 기본 제공 클래스의 변경을 정당화할 수 있는 이유는 몇가지인가 있습니다. 예를 들어, Web 어플리케이션에는 변수가 절대로 공백(`nil`)  이 되면 안되는 경우가 종종 있습니다. (유저 이름 등에 스페이스나 [그 외 다른 공백문자](http://en.wikipedia.org/wiki/Whitespace_(computer_science)) 가 들어가면 안되는 것) 그렇기 때문에, Rails는 `blank?` 메소드를 Ruby에 추가하고 있습니다. Rails의 확장은 자동적으로 Rails 콘솔에도 적용되기 때문에, 다음과 같이 콘솔에서 확장한 결과를 확인할 수 있습니다. (주의: 다음 코드는 순수한 `irb` 에서는 작동하지 않습니다.)

``` ruby
>> "".blank?
=> true
>> "      ".empty?
=> false
>> "      ".blank?
=> true
>> nil.blank?
=> true
```

스페이스로 이루어진 문자열은 빈 상태 (empty) 라고는 인식되지 않습니다만,  공백 (blank)  이라는 상태로는 인식되고 있습니다. 여기서 `nil` 은 공백이라고 인식되는 것이 주의해주세요. `nil` 은 문자열이 아니기 때문에, Rails는  `blank?` 메소드를 `String` 클래스가 아닌, 그보다 더 위의 기저클래스에 추가되어 있다는 것을 추측해볼 수 있습니다. 기저 클래스라는 것은 (이번 챕터의 맨 처음 설명한) `Object` 클래스입니다. Rails에 의해 Ruby에서의 기본 클래스에 추가되어지는 예는 9.1에서 설명합니다.

##### 연습
1. `palindrome?` 메소드를 사용하여,  “racecar”가 거꾸로 읽어도 같은 단어이고, “onomatopoeia” 가 거꾸로 읽어도 같은 단어가 아니라는 것을 확인해보세요. 남인도어 “Malayalam” 이라고 하는 단어는 어떻습니까? *Hint*: `downcase` 메소드로 소문자로 바꾸는 것을 잊지 마십시오.
2. 아래의 코드를 참고하여 `String`클래스에 `shuffle` 메소드를 추가해보세요.
3. 아래의 코드에서 `self.` 를 삭제해도 제대로 동작하는지 확인하세요.

``` ruby
>> class String
>>   def shuffle
>>     self.?('').?.? #?를 적절하게 수정해보세요.
>>   end
>> end
>> "foobar".shuffle
=> "borafo"
```



### 4.4.4 Controller Class

지금까지 클래스나 상속에 대해 설명했습니다만, 이러한 설명은 전 챕터에서도 설명한 것 같은 느낌이 듭니다. StaticPages컨트롤러에서 상속이나 클래스에 대해 접해보았을 것이라 생각합니다.
``` ruby
class StaticPagesController < ApplicationController

  def home
  end

  def help
  end

  def about
  end
end
```

여기까지의 설명으로, 드디어 Rails의 코드를 설명할 수 있게 되었습니다. 이번에는 `StaticPagesController`  에서`ApplicationController` 를 상속하여 구현하고 있는 `home` 이나 `help` , `about` 액션을 확인해보겠습니다. Rails 콘솔에는 세션마다 로컬Rails환경을 읽어들이기 때문에, 콘솔 내부에서 명시적으로 컨트롤러를 작성한다거나, 해당 클래스계층을 알아볼 수도 있습니다. 코드를 확인해봅시다.

``` ruby
>> controller = StaticPagesController.new
=> #<StaticPagesController:0x22855d0>
>> controller.class
=> StaticPagesController
>> controller.class.superclass
=> ApplicationController
>> controller.class.superclass.superclass
=> ActionController::Base
>> controller.class.superclass.superclass.superclass
=> ActionController::Metal
>> controller.class.superclass.superclass.superclass.superclass
=> AbstractController::Base
>> controller.class.superclass.superclass.superclass.superclass.superclass
=> Object
```

상속은 아래와 같습니다.

![](../image/Chapter4/static_pages_controller_inheritance.png)



Rails 콘솔에서는, 그 내부에서 컨트롤러의 액션을 호출하는 것이 가능합니다.

```ruby
>> controller.home
=> nil
```

여기서 `home` 액션의 내부는 아무것도 없기 때문에, `nil` 이 리턴됩니다.

여기서 중요한 점은, Rails의 액션에는 리턴값이 없습니다. 적어도 리턴되는 값은 중요하지 않습니다. [제 3장](Chapter3.md) 에서설명했다시피, `home` 액션은 Web페이지를 표시하기 위한 액션이기 때문이며, 값을 리턴하기 위한 액션은 아닙니다. 게다가 제 3장에서는 한 번 `StaticPagesController.new` 를 실행하지 않았습니다. 어째서 이렇게하여도 평범하게 동작하는 것일까요?

사실 Rails는 Ruby로 작성되어 있으나, Ruby와는 전혀 관계가 없다고 생각하면 됩니다. Rails의 클래스는 보통 Ruby 오브젝트와 같은 동작을 합니다만, 많은 클래스에서는 Rails만의 독특한 기능들로 구성되어 있습니다. Ruby와는 별개로 학습할 필요가 있는 것입니다.

##### 연습

1. [제 2장](Chapter2.md) 에서 만든 Toy 어플리케이션의 디렉토리에서 Rails 콘솔을 실행하여 `User.new` 를 실행하고, `user` 오브젝트가 작성되는 것을 확인해봅시다.
2. 생성한 `user` 오브젝트의 클래스 상속관계를 확인해봅시다.



### 4.4.5 User Class

마지막으로 완전한 클래스를 작성해보는 것으로 이번 챕터를 마무리해봅시다. 여기서 제 6장에서 사용하는 `User`  클래스를 처음부터 만들어봅시다.

지금까지는 콘솔 상에서 클래스를 정의했습니다만, 이런 귀찮은 것은 더이상 할 필요는 없습니다. 지금부터는 어플리케이션의 루트 디렉토리에 `example_user.rb` 파일을 작성하고, 아래의 코드를 작성해봅시다.

``` ruby
#example_user.rb
class User
  attr_accessor :name, :email

  def initialize(attributes = {})
    @name  = attributes[:name]
    @email = attributes[:email]
  end

  def formatted_email
    "#{@name} <#{@email}>"
  end
end
```

위 코드는 지금까지의 코드보다 조금 어렵게 작성되어 있기 때문에, 순서대로 차근차근 확인해봅시다. 일단 다음 코드는

`attr_accessor :name, :email`

유저이름과 이메일(*속성: attribute* ) 에 대응하는 접근자(*accessor*) 를 각각 작성합니다. 접근자를 작성하는 것으로, 해당 데이터를 꺼내는 메소드 (*getter*) 와 데이터를 설정하는 메소드 (*setter*) 를 각각 정의할 수 있습니다. 구체적으로는 위 코드를 실행하는 것으로 인스턴스 변수 `@name`과 인스턴스 변수  `@email` 에 접근할 수 있는 메소드를 Rails가 준비해줍니다. 2.2.2에서 가볍게 설명했습니다만, Rails에서는 인스턴스 변수를 컨트롤러 내부에서 선언하는 것만으로도 뷰에서 사용할 수 있다는 점에서, 이용가치는 충분히 있습니다. 그러나 일반적으론느 해당 클래스 내부라면 어디서든 접근할 수 있는 변수로써 사용됩니다. (이 것에 대해서는 나중에 자세히 설명합니다.) 그리고 인스턴스 변수는 항상 `@` 기호를 머리에 붙여 사용합니다. 아직 정의하지 않은 값은 `nil` 이 됩니다.

다음 코드에 있는 `initialize` 는, Ruby의 특수한 메소드입니다. 이 메소드는 `User.new` 를 실행하면 자동적으로 호출되는 메소드입니다. 이 경우 `initialize` 메소드는 다음과 같이 `attribute` 라고 하는 파라미터 1개를 필요로합니다.

``` ruby
  def initialize(attributes = {})
    @name  = attributes[:name]
    @email = attributes[:email]
  end
```

위 코드에서 `attribute` 변수는 빈 해시를 디폴트 값으로 가지고 있기 때문에, 이름이나 메일주소가 없는 유저를 작성하는 것이 가능합니다. ([4.3.3](#433-Hash와-Symbol)을 떠올려보세요. 존재하지 않는 키에 대해 해시는 `nil` 를 리턴하기 때문에, `:name`키가 없다면 `attributes[:name]` 은 `nil` 상태이며, `attributes[:email]` 도 마찬가지입니다.

마지막으로 `formatted_email` 메소드를 정의해봅시다. ([4.2.2](#422-문자열)) 이 메소드는 문자열의 식전개를 이용하여 `@name`, `@email` 에 삽입된 값을 유저의 메일주소로써 구성할 수 있습니다.

``` ruby
  def formatted_email
    "#{@name} <#{@email}>"
  end
```

`@` 기호를 사용하여 표시하고 있는 것 처럼, `@name`과 `@email` 양쪽도 인스턴스 변수이기 때문에, 자동적으로 `formatted_email` 메소드에서 사용할 수 있게 됩니다.

Rails 콘솔을 이용하여 example_user의 코드를 `require` 하여 자신이 만든 클래스를 사용해봅시다.

``` ruby
>> require './example_user'     # example_user의 코드를 읽어들이는 방법
=> true
>> example = User.new
=> #<User:0x224ceec @email=nil, @name=nil>
>> example.name                 # attributes[:name]은 존재하지 않기 때문에 nil
=> nil
>> example.name = "Example User"           # 이름을 대입한다.
=> "Example User"
>> example.email = "user@example.com"      # 메일주소를 대입한다.
=> "user@example.com"
>> example.formatted_email
=> "Example User <user@example.com>"
```

위 코드에서 require에 있는 `'.'` 은 Unix의 *현재 디렉토리* 를 표현하며, `./example_user` 라고 하는 패스는, 현재 디렉토리에서 상대 패스로 example_uesr 파일을 찾도록 Ruby에 선언하는 것입니다. 다음 코드에서는 비어 있는 example_user를 생성합니다. 다음으로 대응하는 속성에 각각 수동으로 값을 대입하는 것으로 이름과 메일주소를 부여합니다. ( 우리는 `attr_accessor` 를 사용하고 있기 때문에, 이것으로 대입할 수 있게됩니다.) 다음 코드는

`example.name = “Example User”`

`@name` 변수에 `”Example_uesr"` 라고 하는 값을 설정합니다. 마찬가지로 `email` 속성에도 값을 설정합니다. 이 값들은 `formatted_email` 메소드에서 사용합니다.

[4.3.4](#434-다시-한-번-CSS) 에서 제일 마지막의 해시 파라미터로 소괄호를 생략할 수 있다는 것을 설명했습니다. 그것과 마찬가지로 `initialize` 메소드에 해시를 넘기는 것으로, 새로운 유저를 작성할 수 있게 됩니다.

``` ruby
>> user = User.new(name: "Michael Hartl", email: "mhartl@example.com")
=> #<User:0x225167c @email="mhartl@example.com",
@name="Michael Hartl">
>> user.formatted_email
=> "Michael Hartl <mhartl@example.com>"
```

이 것은 일반적으로 매스 어사인먼트 (*mass assignment*) 라고 불리는 방법이며, Rails 어플리케이션에서 자주 사용됩니다. 예를들어 제 7장에서는 실제로 해시 파라미터를 사용하여 오브젝트를 초기화 하는 코드가 있습니다.

##### 연습
1. User클래스에 정의되어 있는 name속성을 수정하여, first_name속성과 last_name 속성으로 나누어봅시다. 또한 해당 속성들을 사용하여 “Michael Hartl” 이라고 하는 문자열을 리턴하는 `full_name` 메소드를 정의해봅시다. 마지막으로  `formatted_email` 메소드의 `name` 의 부분을 `full_name` 으로 바꾸어봅시다. (원래의 결과와 같은 결과를 나타낸다면 성공한 것입니다.)
2. “Hartl, Michael” 과 같은 포맷 (성과 이름이 컴마, 스페이스로 구분하는 문자열) 으로 리턴하는 `alphabetical_name` 메소드를 정의해봅시다.
3. `full_name.split` 과 `alphabetical_name.split(‘, ‘).reverse` 의 결과를 비교하여 같은 결과가 되는지 확인해봅시다.



## 4.5 마지막으로
이상으로 Ruby언어의 개요를 끝내겠습니다. [제 5장](Chapter5.md) 에서는 이번 챕터에서 배운 내용을 샘플 어플리케이션의 개발에 활용해보겠습니다.

[4.4.5](#445-User-Class) 에서 작성한 `example_user.rb` 파일은 이후로는 사용하지 않습니다. 삭제해주세요.

`rm example_user.rb`

다음으로, 이외 변경은 레포에 커밋을 하고 `master` 브랜치에 머지해봅시다.

``` 
$ git commit -am "Add a full_title helper"
$ git checkout master
$ git merge rails-flavored-ruby
```

습관적으로 리모트 레포지토리에 푸시를 하거나 Heroku에 배포하기 전에 테스트 코드를 실행하여 기존 동작에 영향이 없는지 확인해보도록 합시다.

`rails test`

이 이후 Bitbucket에 푸시하여 

`git pust`

마지막으로 Heroku에 배포하면 이번 챕터는 끝입니다.

`git push heroku`



### 4.5.1 4장의 정리

- Ruby는 문자열을 다루는 많은 메소드를 가지고 있다.
- Ruby는 모든것이 오브젝트이다.
- Ruby는 `def` 라고 하는 키워드를 사용하여 메소드를 정의한다.
- Ruby에서는 `class` 라고 하는 키워드를 사용하여 클래스를 정의한다.
- Rails의 뷰에서는 정적  HTML말고도 ERB(Embedded RuBy) 를 사용할 수 있다.
- Ruby의 기본 제공 클래스에는 배열, 범위, 해시 등이 있다.
- Ruby 의 블록은 (다른 기능과 비교해서) 유난한 기능으로, 데이터구조보다도 자연스러운 반복문을 사용할 수 있다.
- 심볼은 라벨이다. 추가적인 구조는 가지고 있지 않은 (대입 등을 할 수 없다.)  문자열같은 것.
- Ruby에서는 클래스를 상속할 수 있다.
- Ruby에서는 기본 제공 클래스조차도 내부를 참조한다던지, 수정할 수 있다.
- “deified” 단어는 거꾸로해도 똑같은 단어이다.