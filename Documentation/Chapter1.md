# 제 1장 제로부터 배포까지

『 [Ruby on Rails Tutorial : 실제 예를 통해 배워보자.](https://railstutorial.jp/) 』 에 오신 것을 환영합니다. 본 튜토리얼은 Custom Web Application의 개발방법을 알려드리기 위해 쓰여졌습니다. 개발을 하기 위해서는 유명한 [Ruby on Rails](http://rubyonrails.org/) 이라고 하는 Web Framework을 사용하고 있습니다. 게다가, Rails에서만 쓰이는 기초적인 지식에 기반하는 것이 아닌, Web개발의 기초를 배우는 것에 중점을 둔 **고도의 기술** 에 대해서도 기술하였습니다.  ([컬럼1.1](#컬럼1.1))
또한 본 서는  [Learn Enough to Be Dangerous](http://learnenough.com/story) 라고 하는 시리즈 중 하나 입니다. Learn Enough의 입문시리즈에서는 [Ruby on Rails Tutorial](http://railstutorial.org/) 의 기초지식을 배우는 데에 적절한 튜토리얼이 있습니다. 예를 들면, 시리즈의 첫 번째 책인 [Learn Enough Command Line to Be Dangerous](http://learnenough.com/command-line-tutorial) 에서는 Rails 튜토리얼과는 조금은 다른, 완전한 초보용의 튜토리얼이 서술되어 있습니다.

###### 컬럼 1.1 「숙련」이라고 하는 것은
> [Ruby on Rails Tutorial](http://railstutorial.org/) 은 [Learn Enough to Be Dangerous](http://learnenough.com/story) 시리즈의 하나이기도 하며, 시리즈 전체에 「*숙련 (Techinical Sophistication)*」 이라는 테마가 관통하고 있습니다. 기술적으로 매우 어려운 과제를 마법처럼 해결하기 위해선,  하드 스킬(조작방법등의 정형화하기 쉬운 스킬)과 소프트 스킬 (디버그등 정형화하기 어려운 스킬) , 양쪽의 기술의 숙련이 필요합니다. Web개발이나 프로그래밍은 일반적인 고도의 기술이라는 인식이 있으나, 기술은 알고 있으면 좋은 것뿐만은 아닙니다. 물론 메뉴항목을 클릭하면, 어떠한 이벤트가 발생하는 것정도도 모른다면 얘기는 달라지지만, 한편으로는 에러메세지를 검색하여 알아본다거나, 조금 더 힘내서 해볼지, 아니면 포기하고 재부팅할지를 판단하는 능력도 필요합니다. ( [図1.1](https://railstutorial.jp/chapters/beginning?version=5.1#fig-tech_support_cheat_sheet) )。  
> Web Application에서 동적인 부품은 매우 많기 때문에, 위에 서술한 양쪽의 기술을 습득하는 것이 필요합니다. 게다가 Rails의 Web Application 의 경우에는 적절한 Ruby gem의 선정방법, bundle install이나 bundle update의 실행방법, 로컬 웹서버가 멈췄을 때의 재부팅 방법 등의 기술들도 습득할 수 있습니다. (지금 제가 여기서 서술한 용어들을 전부 모른다고 해도 안심하세요. 본 튜토리얼에서 전부 설명합니다.)  
> 본 튜토리얼을 진행하면, 무슨 짓을해도 순서대로 진행이 안되는 것도 있을 수 있습니다. 그러기 쉬운 내용에 대해서는 되도록 정보를 많이 포함하도록 하고 있습니다만 모든 상황을 커버하는 것은 불가능입니다. 그러한 트러블은 오히려 숙련되기 우한 수련이라고 생각하고, 과제해결에 임합니다.(그래도 해결이 안되는 것은…「*버그가 아닙니다. 사양입니다!*」( [別著](https://www.learnenough.com/command-line-tutorial#aside-speak_geek) 로부터)  

*Ruby on Rails Tutorial*은, Web개발자나 IT창업가를 대상으로한, 매우 우수한 입문서로써 서술되어 있습니다. Ruby의 기초, HTML와CSS, 데이터베이스, 버전관리, 개발 방법 등, Web개발의 모든것이 망라되어 있습니다. 본 튜토리얼은 초심자뿐만 아니라 Web개발의 베테랑에게도 유용합니다. MVC나REST, 제네레이터, 마이그레이션, 루팅, ERB 등 Rails Framework의 코어기술을 본 튜토리얼에서 한 번에 배울 수 있습니다. 초심자, 베테랑을 따지지 않고 *Ruby on Rails Tutorial*을 끝까지 완주하는 것을 목표로 한다면, Rails 주변의 한층 더 고급의 내용의 서적, 블로그, 스크린캐스트(동영상) 등을 읽고 푸는 힘을 기를 수 있을 것입니다.

*Ruby on Rails Tutorial*에서는, 일관된 Web개발방법을 배우기 위해 3개의 Sample Application을 만듭니다. *hello* Application ( [1.3](https://railstutorial.jp/chapters/beginning?version=5.1#sec-the_hello_application) ), 좀 더 복잡한 *toy* Application ( [제2장](https://railstutorial.jp/chapters/toy_app?version=5.1#cha-a_toy_app) ), 실전편인 *sample* Application ( [第3章](https://railstutorial.jp/chapters/static_pages?version=5.1#cha-static_pages) 에서 [第14章](https://railstutorial.jp/chapters/following_users?version=5.1#cha-following_users) 까지) 입니다. 어플리케이션 이름을 굳이 구체적으로 해놓지 않은 것으로 부터 알 수 있듯이, *Ruby on Rails Tutorial* 에서 개발하는 어플리케이션에서는 특정의 웹 서비스에 치우치지 않은, 일반적인 기술을 염두에 두고 있으며, 독자의 목적에 관계없이, 본 튜토리얼에서는 웹 개발의 기초를 배울 수 있습니다. 튜토리얼의 마지막에 만드는 *Sample* Application은 「우연스럽게도」 [Twitter](http://twitter.com/) 와 매우 닮아 있습니다. (여담으로, Twiiter의 초기도 Rails로 만들어졌었습니다.) 튜토리얼에서는 일반적인 원칙에 따르는 것을 중시하고 있기 때문에, Twitter에 얽매이지않고, 어떠한 웹 어플리케이션이라도 개발할 수 있는 기초스킬을 배울 수 있습니다.

제1장에서는 제일 처음으로 필요한 소프트웨어를 설치하고, 개발환경을 정리한 다음 Ruby on Rails를 동작시킬 준비를 합니다. 그 다음 **hello_app** 이라고 하는 Rails 어플리케이션을 첫 샘플로써 제작하게 됩니다. *Rails Tutorial* 에서는 소프트웨어 개발의 현장에서도 바로 사용할 수 있는 최적의 기술을 익히기 위해서, 새로운 Rails프로젝트를 작성한 다음은 바로  Git을 사용하여 버전을 관리하게됩니다. 제 1장의 마지막에서는 작성한 어플리케이션을 빠르고 실제 환경에 배포하여 일반에 공개하는 것 까지 연습해봅니다.

제 2장에서는 Rails 어플리케이션의 기본적인 구조를 맛보기 위해, 또 다른 프로젝트를 작성합니다. 2장에서의 **Toy_app** 에서는, *scaffold* 를 이용하여 단기간에 코드를 자동생성합니다. 단, *scaffold*로 자동생성된 코드는 매우 읽기 난잡하기 때문에, 2장에서 자동생성된 코드의 해설은 하지 않습니다. 그 대신, 자동 생성된 URI(이른바 URLs)를 Web브라우저에서 확인하는 것은 해보도록 하겠습니다.

제 3장 이후에서는 드디어 본격적인 대규모 *Sample* Application을 개발합니다. 자동생성 코드는 사용하지 않고, 아무것도 없는 백지상태에서부터 코드를 작성해갑니다. *Sample* 어플리케이션의 개발에서는 **목업**, **테스트 구동 개발** *(TDD)* , **결합 테스트** 의 방법을 경험해볼 것입니다. 제 3장에서는 정적인 페이지를 작성하여, 거기에 동적인 요소를 순차적으로 추가해나갈 것입니다. 4정에서는 조금 멀리 돌아가 Rails를 받치고 있는 *Ruby*에 대하여 학습해봅니다. 제 5장부터 12장에 걸쳐 레이아웃, 유저의 데이터모델, 유저의 등록/인증시스템의 순으로 개발하고 *Sample* 어플리케이션의 기본적인 부분을 구현해봅니다. 마지막으로 13장과 14장에서는 마이크로 블로그 기능과 소셜기능을 구현하여 실제로 동작하는 웹 사이트로 완성시킵니다.

###### 컬럼 1.2 너무나도 손쉽고 편한 Scaffold의 달콤한 유혹
> Rails를 만든 *David Heinemer Hansson*에 의한 유명한 영상 「 [15분만에 만드는 블로그](http://www.youtube.com/watch?v=Gzj723LkRJY) (영어)」 가 강한 인상을 남긴 덕분에, Rails는 만들어진 처음부터 인기를 끌었습니다. 그 이후로도 비슷한 영상이 만들어졌으나, 어찌되었던 Rails의 능력 확인할 수 있기 때문에 시간이 되면 한 번 보시면 좋겠습니다. 단지 영상에서는 15분안에 블로그를 만들기 위해 *Scaffold* 라고 하는 간단작성기능을 사용하고 있습니다. Rails의 마법과도 같은 **generate scaffold** 명령어로 자동작성된 코드가 있기 때문에, 이러한 빠르고 손쉬운 작업이 가능한 것입니다.  
> 실제로 필자는, Ruby on Rails의 튜토리얼을 쓰면서도, ~[간편하게 코드를 쓸 수 있다](http://en.wikipedia.org/wiki/Dark_side_(Star_Wars))~(원문의  「quicker, easier, more seductive」 은 스타워즈 에피소드 V의 요다의 대사) scaffold의 기능을 사용하고 싶었던 적이 몇번이나 있었습니다. 그러나 자동생성된 코드는 불필요한 코드가 많고 복잡하기 때문에, Rails 초보분들에게는 맞지 않습니다. 운좋게도 자동생성된 코드를 읽었다고 하더라도, 정상적으로 움직이는 이유를 설명하라고 한다면 아마 설명하기 어려울 것입니다. scaffold 의 자동생성된 코드에 의존하는 이상, 코드 자동생성의 달인이 될 순 있겠지만, Rails에 관한 진짜 지식은 배울 수 없을 것입니다.  
> *Ruby on Rails Tutorial* 에서는 보다 더 Rails에 관한 실제 지식을 익히기 위해, Scaffold 명령어와는 거의 반대의 입장에서 개발을 진행할 것입니다. 구체적으로는 제 2장에서 작성하는 간단한 데모 어플리케이션에서는 scaffold를 사용하지만, 이 튜토리얼의 본 방송인 제 3장 이후에서의 샘플 애플리케이션에서는 scaffold를 일체 사용하지 않고 개발을 할 것 입니다.  scaffold를 사용하는 대신에, 개발의 각 스텝에서 간편한 한줄 정도의 코드를 작성할 것입니다. 이 한 줄 짜리의 코드는 무리하지않고 이해할 수 있을 정도로 심플하면서도, 어느정도는 개발을 음미할 수 있을 정도의 수준입니다. 각 스텝에서의 이해가 필요한 코드의 양은 많지는 않지만, 이러한 것들을 반복하는 것으로 최종적으로는 Rails의 지식을 높은 수준으로 얻을 수 있도록 구성되어 있습니다. 이렇게 얻은 지식은 응용성이 높을 것입니다.  

## 1.1 처음에는
Ruby on Rails (간단하게 「Rails」 라고 부를때도 있습니다.) 은 Ruby라고 하는 프로그래밍 언어로 쓰여진 Web 개발 Framework입니다. Ruby on Rails은 2004년에 처음 만들어진 이래로, 급속하게 성장했습니다. 현재는 동적인 Web 어플리케이션을 개발하는 Framework로써 유명하고 인기있는 프레임워크 중 하나가 되어, [Airbnb](http://airbnb.com/) 나 [Basecamp](http://basecamp.com/) , [Disney](http://disney.com/) , [GitHub](https://github.com/) , [Hulu](http://hulu.com/) , [Kickstarter](http://kickstarter.com/) , [Shopify](http://shopify.com/) , [Twitter](http://twitter.com/) , [Yellow Pages](http://yellowpages.com/) 등의 많은 기업에서 Rails가 사용되었습니다. 그 외에도 [ENTP](http://entp.com/) や [thoughtbot](http://thoughtbot.com/) , [Pivotal Labs](http://pivotallabs.com/) , [Hashrocket](http://hashrocket.com/) , [HappyFunCorp](http://www.happyfuncorp.com/) 등 Rails를 전문적으로 사용하는 회사도 많이 있습니다. 또한 Rails를 전문으로한 프리랜서 개발자들은 수없이 많습니다.

Rails가 이렇게 많은 인기를 끄는 것은 왜일까요? 한 가지 이유로는 Rails가 100% 오픈소스로 이루어져 있으며 제약이 적은 [MIT 라이센스](http://www.opensource.org/licenses/mit-license.php) 로 공개되어져있기 떄문입니다. 또한 Rails는 설계가 간결하고 아름답기 때문에 Rails가 인기있는 이유일 것입니다. 이 것을 실현가능하게 한 것은 Rails를 지탱하고 있는  [Ruby](http://ruby-lang.org/)  언어의 경의로운 유연함 덕분입니다. 구체적으로는 웹 어플리케이션 개발에 특화된 [DSL (도메인 고유 언어)](http://en.wikipedia.org/wiki/Domain_Specific_Language) 를 Ruby에서 구현하고 있기 때문에, HTML이나 데이터 모델의 작성, URL의 루팅 등, 웹 프로그래밍에 필요한 많은 작업을 간단하게 할 수 있습니다.  그 결과 Rails를 사용하여 웹 개발을 하면 코드가 간결하고 읽기 쉬워집니다.

게다가 Rails는 최신의 Web 테크놀로지나 프레임워크 설계에 재빠르게 응용할 수 있습니다. 예를 들어, Rails는  「REST」 라고 하는 설계사상의 중요성을 재빠르게 받아들이고, 대응한 프레임 워크 중 하나 입니다. (REST에 대해서는 나중에 말씀드립니다.) 또한 다른 프레임워크에서 성공한 기술이 있다면, Rails의 개발자인  [David Heinemeier Hansson](http://loudthinking.com/) (DHH) 나 [Rails의 코어 팀](http://rubyonrails.org/core) 은 그러한 아이디어를 적극적으로 Rails에 적용합니다. 인상적인 예로는 이전에는 서로 라이벌 관계였던 Merb와 Rails의 통합을 예를 들 수 있습니다. 그 통합의 결과로 Rails는 Merb의 모듈설계나 안정된 API[API](http://en.wikipedia.org/wiki/Application_programming_interface) 、그리고 퍼포먼스의 향상 등 많은 혜택을 받게 되었습니다.

끝으로 Rails에는 놀랄정도로 활발하고 다양한 커뮤니티가 존재합니다. Rails커뮤니티에는 수천명이 있는 오픈소스
[컨트리뷰터](http://contributors.rubyonrails.org/) 나 많은 참가자들이 참가하는 [컨퍼런스](http://railsconf.com/) , 방대한 수의  [gem](https://rubygems.org/) (페이지네이션이나 영상업로드 등 특정 문제를 위한 Gem등), 많은 정보를 발신하고 있는 블로그, 게시판, IRC가 있습니다. 이러한 활발하고 다양한 커뮤니티 덕분에 개발중에 어떠한 에러에 마주했을지라도 에러메세지를 그대로 구글에 검색하는 것만으로도 관련 블로그의 기사나 게시판의 스레드를 찾을 수 있습니다.

### 1.1.1 전제 조건

본 튜토리얼을 배우기 이전에 알고 있으면 좋은 지식들이 있지만, 필수는 아닙니다. 그렇다기보다 Rails 튜토리얼에서는 웹 개발에서 중요시하는 내용이 전부 포함되어 있기 때문입니다. 본 튜토리얼의 중심은 물론 Rails에 의한 웹 개발입니다만, 다른 프로그래밍 언어 Ruby나 Minitest(Rails 의 테스트툴) , 유닉스 커맨드 라인,  [HTML](https://en.wikipedia.org/wiki/HTML) , [CSS](https://en.wikipedia.org/wiki/CSS) , 약간의  [JavaScript](https://en.wikipedia.org/wiki/JavaScript) , 그리고 약간의  [SQL](https://en.wikipedia.org/wiki/SQL) . 이러한 지식들은 본 튜토리얼을 진행하는 것으로 배울 수 있으며 읽기 전에 다른 학습서비스 등으로 배워보는 것도 좋을 것 같습니다. 저는 [Learn Enough](https://www.learnenough.com/) 의 튜토리얼들도 추천드립니다.
	1. Developer Fundamentals
		*1.* [Learn Enough Command Line to Be Dangerous](https://www.learnenough.com/command-line) 
		*2.*  [Learn Enough Text Editor to Be Dangerous](https://www.learnenough.com/text-editor) 
		*3.*  [Learn Enough Git to Be Dangerous](https://www.learnenough.com/git) 
	2. Web Basics
		*1.*  [Learn Enough HTML to Be Dangerous](https://www.learnenough.com/html) 
		*2.*  [Learn Enough CSS & Layout to Be Dangerous](https://www.learnenough.com/css-and-layout) 
	3. Beginning Development
		*1.*  [Learn Enough JavaScript to Be Dangerous](https://www.learnenough.com/javascript) 
		*2.*  [Learn Enough Ruby to Be Dangerous](https://www.learnenough.com/ruby) 
	4. Application Development
		*1.*  [The Ruby on Rails Tutorial](https://www.railstutorial.org/) 

Ruby를 공부한 다음 Rails를 공부하는 것이 좋냐는 질문들을 많이 받습니다. 이 질문에 대한 답변으로, 독자들의 학습 스타일이나 프로그래밍 경험에 따라 다르기 때문에, 확답드리기는 어렵습니다.  Web개발을 처음부터 체계적으로 배우고 싶은 분이나 프로그래밍 경험이 전혀 없는 분들에게는 Ruby의 기초를 습득하시고 나서, 본 튜토리얼을 추천드립니다만, 반대로 지금부터 Rails를 이용하여 개발하고 싶은 분은 “어찌되었던 지금 당장 웹 개발을 하고 싶어!!” 라고 생각하시는 분들이 대부분일 것입니다. 그러한 분들은 싱글웹페이지를 만드는데 엄청 두꺼운 Ruby책을 보시지는 않을 것입니다. 그런 분들은 그냥 이대로 이 튜토리얼을 진행해도 괜찮을 것입니다. 진행하는 도중에 기초지식이 부족하다고 느낀다면, Ruby의 기초를 그때부터 배우신다던지, 혹은 아래에서 소개하는사이트에서 배우는 것도 추천드립니다.

	*  [The Learn Enough Society](https://www.learnenough.com/story) :  [Learn Enough](https://www.learnenough.com/)의 텍스트가 포함된 15시간 이상의 스트리밍 교육영상을 제공하는 구독형 서비스
	* [The Turing School of Software & Design](https://turing.io/) : 27시간 풀타임 프로그램
	*  [Bloc](https://www.bloc.io/) : 온라인 부트캠프 커리큘럼. 멘토링제공. Use the coupon code BLOCLOVESHARTL to get $500 off the enrollment fee.
	*  [Launch School](https://launchschool.com/railstutorial) : 온라인 Rails 부트캠프
	*  [Thinkful](https://www.thinkful.com/a/railstutorial) : 현업 엔지니어와 짝을 지어 개발하는 프로젝트기반 온라인 교육
	*  [Pragmatic Studio](https://pragmaticstudio.com/refs/railstutorial) : *Programming Ruby* 의 저자와 함께하는온라인 루비 온 레일즈 코스
	*  [RailsApps](https://tutorials.railsapps.org/hartl) : 많은 양의 Rails 프로젝트
	*  [Rails Guides](https://guides.rubyonrails.org/) : 최신의 Rails 레퍼런스

##### 연습
본 튜토리얼에서는 많은 연습물이 준비되어 있습니다. 곳곳에 있는 연습은 스킵하지 마시고 끝까지 풀어보세요.
본편과 연습문제를 나누기 때문에 연습문제에선 나온 소스는 본편 소스에 쓰지 않도록 하고 있습니다.  뒤집어 생각해보면 연습문제에서 발생한  사소한 차이가 튜토리얼을 진행했을 때의 코드에 사소한 차이가 생길 수도 있습니다. 그렇지만 이러한 차이에서 나오는 문제를 해결하기 위해서라도 자신이 직접 해결하는 것이 좋습니다.

 [Learn Enough Society](http://learnenough.com/story) 를 구독하여 자신의 문제를 올리고 해답을 얻을 수도 있을 것입니다.
 [Ruby on Rails Tutorial](http://railstutorial.org/) 의 특별부록도 있으니 관심있으시면  [Learn Enough to Be Dangerous](http://learnenough.com/story) 도 구독해보세요.

연습에서 준비한 과제들은 어려운 것들도 있습니다만, 일단 한 번 아래의 문제를 알아보세요!

	1. Ruby on Rails에서 사용하는 *Ruby gem* 은 어느 웹사이트로 접속하면 알 수 있습니까?  일단 [검색해봅시다](http://lmgtfy.com/?q=ruby+gem) .
	2. 현재 Rails에서 사용하는 최신 버전은 몇입니까?
	3. Ruby on Rails는 현재까지 몇번이나 다운로드 되었을까요?

### 1.1.2 본 튜토리얼에서의 약속

본 튜토리얼에서의 서술하는 용어 등은 대부분 설명이 필요없다고 생각합니다만, 설명이 필요하다고 생각한 것에 대해서 이번 섹션에서 설명드리겠습니다.

본 튜토리얼에서는 커맨드라인의 커맨드가 꽤나 많이 쓰여져있습니다. 간결하게 하기 위해 다음과 같은 Unix스타일의 프롬프트 (라인 앞에 **$** 를 표시하는 스타일)를 사용하여 그 명령어가 커맨드라인이라는 것을 표시합니다.
```shell
$ echo "hello world"
hello world
```

Rails에서는 커맨드라인에서 실행하는 명령어가 많이 존재합니다. 예를 들면, 1.3.2에서는 `rails server`  명령어를 사용하여 로컬환경에서 Web서버를 기동하기도 합니다.
`$ rails server`

*Rails Tutorial* 에서의 디렉토리 구분은, 커맨드라인의 프롬프트와 마찬가지로 Unix스타일의 “ / “ (슬래시)를 사용합니다. 예를 들면 Sample 어플리케이션 `production.rb` 의 설정파일은 다음과 같이 표시합니다.
`config/environments/production.rb`

이러한 파일패스는 어플리케이션의 루트 디렉토리로부터 상대패스라는 것을 이해하셔야합니다. 루트 디렉토리의 위치는 시스템에 따라 다르며, AWS의 C9을 이용한 *Sample* 어플리케이션의  경우는 다음과 같습니다.
`/home/ec2-user/environment/sample_app/`

기술을 간단하게 하기 위해 본 튜토리얼에서는 원칙적으로 `config/environments/production.rb` 라고 기술하여 루트 디렉토리보다 위의 패스는 생략하겠습니다.

*Rails Tutorial* 에서는 Rails 어플리케이션의 테스트도 다루기 때문에 코드에서 어떠한 행위를 하면 테스트를 실패하는지, 어떠한 행위를 하면 테스트를 통과하는지를 직접 체험하여 알 수 있게 되어있ㅅ브니다. 본 튜토리얼에서는 읽기 쉽게하기 위해 테스트 실패코드는 RED, 테스트가 성공하는 코드를 GREEN 으로 표기하겠습니다.

마지막으로 *Ruby on Rails Tutorial* 에서 쓰는 많은 샘플 코드는 알기 쉽게하기 위해 다음과 같이 쓰여졌습니다.
	1. 코드의 중요한 부분은 하이라이트를 추가하는 것입니다.

	class User < ApplicationRecord
  		validates :name,  presence: true
  		::validates :email, presence: true::
	end

하이라이트행은 보통 추가된 코드를 위해 표시합니다만, 그 전에 추가한 코드와의 차이를 강조하기 위해서도 꽤나 자주 표시합니다.
	2. 긴 코드의 중가부분은 수직으로 연속된 점으로 생략합니다.
```
class User < ApplicationRecord
	.
	.
	.
	has_secure_password
end
```
연속된 점은 생략을 표시하기 때문에 다른 코드와 같이 복사하지 않도록 주의해주세요.

## 1.2 일단 구동시켜보자
Ruby를 인스톨해서 Rails등의 서포트 소프트웨어를 하나부터 열까지 설치하는 연습은, 설사 베테랑 Rails개발자 라고 할지라도, 좀 지루한 작업일 것입니다. OS의 차이, 버전의 차이, 텍스트 에디터의 설정의 차이, IDE의 차이 등, 환경에 차이가 있으면 온갖 문제가 복합적으로 발생할 수 있습니다.  [Ruby on Rails Tutorial](http://railstutorial.org/) 에서 이 문제에 대해, 다음 2가지 방법으로 대응했습니다. 첫 번째는 1.1.1에서 소개한 [Learn Enough](http://learnenough.com/) 에 따라 학습하는 것입니다. 이 방법이라면 튜토리얼에서 필요한 환경 구축을 자동적으로 이용할 수 있습니다. 두 번째 방법으로, 본 튜토리얼을 학습하는 독자에게 권하는 방법은, Cloud 통합 개발환경 (이하 클라우드IDE)를 사용하는 것입니다. 클라우드IDE는 웹 브라우저 안에서 실행할 수 있기 때문에, OS가 다르더라도 똑같이 다룰 수 있습니다.이 방법은 Rails 개발환경의 구축이, 특히 Windows와 같은 환경에서는 조금 귀찮고 번거로운 작업이기 때문에 편리합니다. 게다가 클라우드IDE에서는 작업의 상태를 Cloud에 저장하기 때문에 튜토리얼 중에 다른 작업을 하고 다시 작업을 이어서 진행한다고 해도 바로 진행할 수 있는 메리트도 있습니다.

### 1.2.1 개발 환경
개발환경은 Rails 프로그래머가 제각각 다릅니다. 개발자는 익숙해지면 질수록 자신의 환경을 철저하게 커스터마이즈하기 때문입니다. 개발환경을 구분해보자면, 텍스트에디터나 커맨드라인을 쓰는 환경과, IDE 두개로 나눌 수 있을 것입니다. 그리고 Ruby on Rails 튜토리얼에서는 환경구축의 복잡함을 피하기 위해  [AWS Cloud9](http://c9.io/) 이라고 하는 멋진 클라우드IDE 서비스를 사용해보도록 합시다. AWS Cloud9에서는 Ruby나 RubyGems, Git등 Rails의 개발환경의 구축에 필요한 소프트웨어가 거의다 포함되어 있습니다. Rails나 Heroku등의 일부 소프트웨어는 인스톨이 필요합니다만, 이것도 나중에 설명드리도록 하겠습니다.

여담으로, 로컬환경에서 개발을 하는 것도 가능합니다만, Rails의 개발환경의 설치는 조금 번거롭고 어렵기 때문에, 대부분의 독자분들에게는 클라우드IDE를 사용하는 것을 추천드리고 있습니다. 로컬환경에서 진행하고 싶으신 분들은  [Learn Enough Dev Environment to Be Dangerous](https://learnenough.com/dev-environment-tutorial) 의 순서를 따라주세요.

클라우드IDE에는 Web개발에 필요한 텍스트 에디터, 파일브라우저, 커맨드라인 터미널이 제대로 갖추어져 있습니다. 또한 클라우드IDE의 텍스트에디터에서는 Ruby on Rails의 대규모 프로젝트에는 필수불가결한 파일검색도 할 수 있습니다. 설령 클라우드IDE를 나중에는 쓰지 않더라도 텍스트에디터 등의 개발툴에서 일반적으로 어떠한 것이 가능한가 알아놓는 것이 중요합니다.

![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/ide_anatomy_aws.png)
클라우드 IDE를 사용하기 위해선 다음과 같습니다.
1. Cloud9은 현재 Amazon Web Service에서 서비스중입니다. 때문에 AWS의 아이디가 필요합니다. AWS아이디를 가지신 분은 [AWS에 로그인](https://aws.amazon.com/) 해주시고,  [AWS 콘솔](https://console.aws.amazon.com/) 의 검색박스에서 *Cloud9* 입력하면, 개발환경을 작성하기 위한 페이지로 이동할 수 있습니다.
2. AWS아이디가 없으신 경우에는  [AWS Cloud9에 회원 가입을 해주시길 바랍니다.](https://www.railstutorial.org/cloud9-signup) 악용방지를 위해 신용카드의 정보입력이 필수입니다만, Rails 튜토리얼의 워크스페이스는 1년간 무료이니 안심하셔도 될 것 같습니다. 아이디의 활성화까지 최대 24시간정도가 걸리지만, 보통 10분정도로 회원가입을 하고 활성화할 수 있을 것입니다.
3. Cloud9의 관리페이지에 무사히 접속하셨으면, *Create environment* 를 클릭하여 생성페이지로 이동해주세요. 정보를 적절히 입력해주시고, 확인 버튼을 눌러서 진행해주시면, 최종적으로 C9의 개발환경을 만드실 수 있습니다. 이 때, *root* 유저라고 하는 경고메세지가 나올 수도 있습니다만, 여기에 대해서는 다음에 설명드리겠습니다. (AWS IAM의 권한설정이 필요)
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/cloud9_page_aws.png)
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/cloud9_name_environment.png)
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/cloud9_ide_aws.png)
Ruby의 세계에서는 들여쓰기에 2개의 스페이스를 쓰는 것이 공통 룰이 되어있습니다. C9의 에디터에서도 디폴트를 4에서 2로 변경하는 것을 추천드립니다. 들여쓰기 설정을 바꾸기 위해서는 오른쪽 상단의 톱니바퀴 아이콘을 클릭하여,  soft Tabs 설정을 열어 편집합니다. 설정의 변경은 바로 적용되기 때문에 Save 버튼을 클릭할 필요는 없습니다.
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/cloud9_two_spaces_aws.png)

또한 클라우드IDE를 사용하는 경우에는 Bitbucket과 Heroku 커맨드를 설치할 필요가 있습니다. 자세한 설명은 다음에 하겠습니다.

또한 지금 바로 설정할 필요는 없습니다만, 클라우드IDE에서 테스트환경을 셋업할 때의 필요한 설정 또한 다음에 말씀드리겠습니다. 자세한 것은 3.6.2에서 확인해주세요.

### 1.2.2 Rails를 설치해보자
1.2.1의 개발환경에는 필요한 소프트웨어가 전부 포함되어 있습니다만, Rails 그 자체는 포함되어있지 않습니다. 자주 쓰이는 편리한 설정과 설치를 해봅시다.
제일 처음에는 Ruby 도큐먼트를 설치하여 불필요한 시간을 사용하지 않도록, 커맨드에 설정을 조금 추가해봅시다. 이 것은 시스템에서 한 번만 설정해주면 OK입니다. 이 커맨드의 의미를 모르시더라도, 지금은 문제 없습니다.
`$ printf "install: --no-document \nupdate:  --no-document\n" >> ~/.gemrc`

다음으로, Rails를 설치하기 위해  `gem` 커맨드를 사용해봅시다. 이 커맨드는 *RubyGems* 패키지 매니저가 제공하는 것이며, 터미널에서 사용됩니다. 로컬환경에서 개발하는 경우에는 터미널을 사용합니다. 클라우드 IDE에서 사용할 경우에는 커맨드라인 구역에서 입력해봅시다.
`$ gem install rails -v 5.1.6`
`-v ` 옵션을 사용하여 설치하는 Rails의 버전을 정확하게 지정할 수 있습니다. 이것으로 이 다음부터의 튜토리얼을 제대로 진행할 수 있게 되었습니다.

## 1.3 첫 어플리케이션
컴퓨터 프로그래밍에서의  [오랜 전통](http://www.catb.org/jargon/html/H/hello-world.html) 에 따라, 첫 어플리케이션은 *Hello World* 라는 문구를 출력하는 프로그램으로 해봅시다. 구체적으로는 웹페이지에 *Hello World !* 라고 하는 문자열을 표시하는 단순한 어플리케이션을 개발환경과 실제 배포환경 각각 만들어봅시다.

어떠한 Rails 어플리케이션이라도 초기의 작성순서는 기본적으로 같습니다. `rails new` 커맨드를 실행하여 작성합니다. 이 커맨드를 실행하는 것으로 지정된 디렉토리에 Rails 어플리케이션의 뼈대를 간단하게 작성할 수 있습니다. C9환경을 사용하지 않는 경우에는 Rails 프로젝트에서 쓰기 위해 `environment` 디렉토리를 만들어주세요.
```
$ cd                  # ホームディレクトリに移動する
$ mkdir environment     # ‘environment’ ディレクトリを作成する
$ cd environment/       # ‘environment’ ディレクトリに移動する
```

###### 컬럼 1.3 재빠르게 배우고 싶은 분들을 위한 Unix 커맨드라인 강좌
> Windows유저나 macOS유저의 많은 경우에는 커맨드라인의 추억이 아마 없을 것입니다. (macOS 유저라면 조금은 아실지도 모르겠습니다.) 다행스럽게도, 지금은 추천해드리고 있는 클라우드개발환경 덕분에 Unix커맨드라인을 모두 같은 환경에서 사용할 수가 있어서, [Bash](https://en.wikipedia.org/wiki/Shell_(computing)) 와 같은 표준적인 [쉘 커맨드라인 인터페이스](http://en.wikipedia.org/wiki/Bash_(Unix_shell)) 를 사용할 수도 있습니다.  
> 커맨드라인의 기본적인 구조는 정말 심플합니다. 유저는 커맨드를 발행(issue)하여 실로 여러가지 조작을 할 수 있습니다. 디렉토리의 작성이라면 mkdir, 파일의 이동과 이름변경이라면 mv, 파일의 복사라면 cp 등등... GUI 환경만 접해본 유저분들께는 커맨드라인의 검은 화면은 조금 무섭게 느껴질 수도 있을 것입니다만 실제로는 그렇진 않습니다. 커맨드 라인은 그 자체가 강력한 툴이며, 엔지니어에게는 필수불가결한 도구입니다. 경험이 풍부한 개발자들의 데스크탑화면을 살짝 보면, 십중팔구는 검은 터미널 윈도우가 열려있고, 거기서 많은 커맨드라인쉘이 바쁘게 동작하고 있는 것을 볼 수 있을 것입니다.  
> 커맨드라인에 대해 얘기를 하면 끝이 없습니다만, 본 튜토리얼에서 필요한 Unix커맨드라인의 커맨드는 별로 많지 않으니 안심하셔도 될 것 같습니다. Unix커맨드라인에 대해 좀 더 알고 싶으신 분들은  [Learn Enough](http://learnenough.com/) 의 튜토리얼이나 [Learn Enough Command Line to Be Dangerous](http://learnenough.com/command-line-tutorial) 을 확인해주세요.  

로컬환경 혹은 클라우드IDE에서의 절차는 아래의 커맨드를 사용한 첫 어플리케이션의 작성입니다. 아래의 커맨드에서는 Rails의 버전을 명시적으로 지정하고 있는 것을 봐주십시오. 이러한 버전을 지정하는 것으로 인해, Rails 설치시의 지정한 버전과 같은 파일구조를 작성할 수 있습니다.  (*Could not find ‘railties’* 가 표시되는 경우, 설치한 Rails의 버전이 제대로 설치되지 않았을 수도 있습니다. rails의 버전을 확인해주세요.)
```
$ cd ~/environment
$ rails _5.1.6_ new hello_app
      create
      create  README.md
      create  Rakefile
      create  config.ru
      create  .gitignore
      create  Gemfile
      create  app
      create  app/assets/config/manifest.js
      create  app/assets/javascripts/application.js
      create  app/assets/javascripts/cable.js
      create  app/assets/stylesheets/application.css
      create  app/channels/application_cable/channel.rb
      create  app/channels/application_cable/connection.rb
      create  app/controllers/application_controller.rb
      .
      .
      .
      create  tmp/cache/assets
      create  vendor/assets/javascripts
      create  vendor/assets/javascripts/.keep
      create  vendor/assets/stylesheets
      create  vendor/assets/stylesheets/.keep
      remove  config/initializers/cors.rb
         run  bundle install
Fetching gem metadata from https://rubygems.org/……….
Fetching additional metadata from https://rubygems.org/..
Resolving dependencies…
Installing rake 11.1.2
Using concurrent-ruby 1.0.2
.
.
.
Your bundle is complete!
Use `bundle show [gemname]` to see where a bundled gem is installed.
         run  bundle exec spring binstub —all
* bin/rake: spring inserted
* bin/rails: spring inserted
 
```

아래부분을 주목해주세요. `rails new`를 실행하면 파일 작성 후에 `bundle install` 커맨드가 자동적으로 실행됩니다. 이 `bundle install`  에 대해서는 1.3.1에서 설명드리겠습니다.

보시는 것과 같이, `rails` 커맨드를 실행하면 대량의 파일과 디렉토리가 생성됩니다. Web어플리케이션의 디렉토리를 어떻게 구성할지는 본래 임의(자유)입니다만, Rails와 같은 Web 프레임워크에서는 디레토리와 파일 구조는 표준화되어 있습니다. 파일/디렉토리 구조가 모두 Rails 어플리케이션에서 표준화되어진 덕분에 다른 개발자들이 쓴 Rails 코드를 쉽게 읽을 수 있습니다. 이것은 Web프레임워크를 도입하여 얻을 수 있는 큰 메리트입니다.

Rails에서 사용되는 기본 파일에 대해서는 아래의 내용을 확인해주세요. 이것들의파일이나 디렉토리의 의미나 목적에 ㅐ해서는 본 튜토리얼 전체에 걸쳐 설명하겠습니다. 특히 5.2.1 이후에는 Rails 3.1이후에 탑재된 `Asset Pipeline` 의 일부인 `app/assets` 의 디렉토리에 대해 상세히 설명해드립니다. 에셋 파이프라인에 의해 CSS이나 Javascript 파일 등의 에셋을 간단하게 구성하거나 배포할 수 있습니다.

![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/directory_structure_rails_4th_edition.png)
	- app/ : 모델, 뷰, 컨트롤러, 헬퍼등을 포함한 주요 어플리케이션 코드
	- app/assets : 어플리케이션에서 쓰이는 CSS, Javascript파일, 영상 등의 에셋
	- bin/ : 바이너리 실행가능 파일
	- config/ : 어플리케이션의 설정
	- db/ : 데이터베이스 관련 파일
	- doc/ : 메뉴얼등 어플리케이션의 도큐먼트
	- lib/ : 라이브러리 모듈
	- lib/assets : 라이브러리 CSS, Javascript, 영상 파일
	- log/ : 어플리케이션의 로그파일
	- public/ : 에러페이지, 일반적으로 직접 공개되는 데이터
	- bin/rails : 코드의 생성, 콘솔의 기동, 로컬의 웹서버 기동 등에 쓰이는 Rails script
	- test/ : 어플리케이션의 테스트
	- tmp/ : 일시 파일
	- vendor/ : 서드파티의 플러그인이나 gem
	- vendor/assets : 서드파티의 플러그인이나 gem에서 쓰이는 CSS, Javascript, 영상 파일 등
	- README.md : 어플리케이션의 간단한 설명
	- Rakefile : rake커맨드에서 쓰이는 task
	- Gemfile : 해당 어플리케이션에서 필요한 gem의 정의 파일
	- Gemfile.lock : 어플리케이션에서 쓰이는 gem의 버전을 확인하기 위한 리스트 파일
	- config.ru :  [Rack 미들웨어](http://rack.github.io/) 용의 설정 파일
	- .gitignore : Git에 포함시키고 싶지 않은 파일들을 지정하기 위한 파일

### 1.3.1 Bundler
Rails 어플리케이션을 신규작성한다면, 다음은 *Bundler* 를 실행하여 어플리케이션에 필요한 gem을 인스톨해봅시다. 1.3에서도 간단히 설명드렸듯, Bundler는 `rails` 에 의해 자동적으로 실행 (이 경우에는 `bundle install` ) 됩니다. 여기서는 기본 어플리케이션 gem을 변경하여 Bundler를 다시 실행해봅니다. 그러기 위해서 텍스트에디터에서 Gemfile을 엽니다. (클라우드IDE 에서는 파일 네비게이터에서 화살표 모양을 클릭하여 샘플 어플리케이션의 디렉토리를 열고, `Gemfile` 이라고 하는 파일을 클릭합니다.) Gemfile의 내용은 대강 아래와 같은 내용으로 구성되어 있습니다. 버전등의 자세한 부분에서는 다소 차이가 있을 수 있습니다.
Gemfile의 내용은 Ruby코드입니다만 여기서 문법은 신경쓰지 않아도 됩니다. Ruby에 대해서는 제4장에서 설명하겠습니다. 파일이나 디렉토리가 제대로 표시되지 않을때는 네비게이터의 톱니바퀴 모양을 클릭하여 *Refresh File Tree* 를 선택합니다. (보통 파일이나 디렉토리가 제대로 표시되지 않을 때에는 파일트리를 닫았다가 열어보세요. )

![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/cloud9_gemfile_aws.png)
```ruby
source 'https://rubygems.org'

git_source(:github) do |repo_name|
  repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
  "https://github.com/#{repo_name}.git"
end

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '~> 5.1.6'
# Use sqlite3 as the database for Active Record
gem 'sqlite3'
# Use Puma as the app server
gem 'puma', '~> 3.7'
# Use SCSS for stylesheets
gem ‘sass-rails’, ‘~> 5.0’
# Use Uglifier as compressor for JavaScript assets
gem ‘uglifier’, ‘>= 1.3.0’
# Use CoffeeScript for .coffee assets and views
gem ‘coffee-rails’, ‘~> 4.2’
# See https://github.com/rails/execjs#readme
# for more supported runtimes
# gem ‘therubyracer’, platforms: :ruby

# Use CoffeeScript for .coffee assets and views
gem ‘coffee-rails’, ‘~> 4.2’
# Turbolinks makes navigating your web application faster.
# Read more: https://github.com/turbolinks/turbolinks
gem ‘turbolinks’, ‘~> 5.x’
# Build JSON APIs with ease.
# Read more: https://github.com/rails/jbuilder
gem ‘jbuilder’, ‘~> 2.0’
# Use Redis adapter to run Action Cable in production
# gem ‘redis’, ‘~> 3.0’
# Use ActiveModel has_secure_password
# gem ‘bcrypt’, ‘~> 3.1.7’

# Use Capistrano for deployment
# gem ‘capistrano-rails’, group: :development

group :development, :test do
  # Call ‘byebug’ anywhere in the code to stop execution and get a debugger console
  gem ‘byebug’, platforms: [:mri, :mingw, :x64_mingw]
  # Adds support for Capybara system testing and selenium driver
  gem ‘capybara’, ‘~> 2.13’
  gem ‘selenium-webdriver’
end

group :development do
  # Access an IRB console on exception pages or by using
  # <%= console %> anywhere in the code.
  gem ‘web-console’
  gem ‘listen’, ‘>= 3.0.5’, ‘< 3.2’
  # Spring speeds up development by keeping your application running
  # in the background. Read more: https://github.com/rails/spring
  gem ‘spring’
  gem ‘spring-watcher-listen’, ‘~> 2.0.0’
end

# Windows環境ではtzinfo-dataというgemを含める必要があります
gem ‘tzinfo-data’, platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

대부분의 행에서는 해시심볼 # 로 코멘트아웃되어져있습니다. 이 행들은 자주 쓰이는 gem과 bundler의 문법의 예를 코멘트형식으로 설명하고 있습니다. 지금은 기본이외의 gem을 설치할 필요는 없습니다.

`gem` 커맨드로 특정 버전을 지정하지 않는한, Bundler는 자동으로 최신버전의 gem을 설치합니다. 예를 들어 다음과 같은 커맨드가 있다고 합시다.
`gem 'sqlite3`
이 sqlite3 라고 하는 gem의 버전을 지정하는 방법으로는 2가지 정도가 있습니다. Rails에서 자주 쓰이는 gem의 버전을 어느정도 제어할 수 있습니다. 첫번째 방법은
`gem 'uglifier', '>= 1.3.0'`
`uglifier` 의 버전이 1.3.0 이상이면 최신버전의 gem이 설치됩니다. 극단적으로 말하면, 버전이 7.2라도 그것이 최신 버전이라면 설치가 진행됩니다. 두번째 방법은
`gem 'coffee-rails', '~> 4.0.0'`
이렇게 지정하면 `coffee-rails` 의 버전이 4.0.0보다 크고, 4.1보다 작으면 설치가 진행됩니다. 즉, 아래의 코드를 실행하면 `>=` 라고 하는 표기에서는 항상 최신의 gem이 설치되고, `~> 4.0.0` 이라는 표기에서는 마이너버전 부분에 해당되는 업데이트가 된 gem을 설치합니다. 두 번째 방법의 경우 메이저버전의 업데이트 (4.0에서 4.1) 릴리즈는 설치되지 않습니다. 아쉽게도, 경험상 자그마한 마이너 업데이트라도 문제를 일으킬 때도 있습니다. 그렇기 때문에 본 튜토리얼에서는 구체적으로 모든 gem에서 버전을 콕 집어 지정하여, 버전을 고정하고 있습니다. 베테랑 개발자라면 `Gemfile` 에서 `~>` 을 사용하여 지정, 최신의 gem을 써서 튜토리얼을 진행해도 괜찮습니다. 다만 버전이 다르면 본 튜토리얼에서 설명하고 있는 어플리케이션의 움직임이 다를 가능성이 있기 때문에, 적절한 대응이 필요할 것입니다.

위에서의 `Gemfile` 내용을 실제로 사용하는 정확한 버전의 gem으로 바꾼 것이 아래의 리스트입니다.  또한 변경에 대해서, *sqlite3* gem을 development환경과 test환경에서만 쓸 수 있게 하는 것도 눈여겨 봐주세요. (즉 production환경에서는 사용하지 않음) 이 것은 나중에 Heroku에서 쓰일 데이터베이스와 마찰을 일으키지 않기 위한 것입니다.
```ruby
source ‘https://rubygems.org’

gem ‘rails’,        ‘5.1.6’
gem ‘puma’,         ‘3.9.1’
gem ‘sass-rails’,   ‘5.0.6’
gem ‘uglifier’,     ‘3.2.0’
gem ‘coffee-rails’, ‘4.2.2’
gem ‘jquery-rails’, ‘4.3.1’
gem ‘turbolinks’,   ‘5.0.1’
gem ‘jbuilder’,     ‘2.6.4’

group :development, :test do
  gem ‘sqlite3’,      ‘1.3.13’
  gem ‘byebug’, ‘9.0.6’, platform: :mri
end

group :development do
  gem ‘web-console’,           ‘3.5.1’
  gem ‘listen’,                ‘3.1.5’
  gem ‘spring’,                ‘2.0.2’
  gem ‘spring-watcher-listen’, ‘2.0.1’
end

# Windows環境ではtzinfo-dataというgemを含める必要があります
gem ‘tzinfo-data’, platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

어플리케이션의 `Gemfile` 내용을 위 리스트의 내용으로 수정했다면, `bundle install` 을 다시 실행하여 gem을 설치해주세요.
```
$ cd hello_app/
$ bundle install
Fetching source index for https://rubygems.org/
.
.
.
```

`bundle install` 커맨드의 실행은 조금 시간이 걸릴 수도 있습니다. 성공적으로 실행된다면 어플리케이션이 실행가능한 상태가 됩니다.

추가로, `bundle install` 을 실행하면 *일단 bundle update를 먼저 실행시켜주세요* 와 비슷한 내용의 메세지가 출력될 수도 있습니다. 그 경우에는 메세지대로 `bundle update`를 먼저 실행해주세요. (메뉴얼대로 진행되지 않을 때에는 침착하게 대응하는 것도 능력입니다. 그러한 에러메세지에는 해결대응책도 같이 기술되어있을 때도 있기 때문에 잘 읽어보세요.)

### 1.3.2 rails server
1.3의 `rails server` 커맨드와 1.3.1 의 `bundle install` 의 커맨드를 실행하는 것으로, 실제로 동작하는 어플리케이션이 만들어졌습니다. 감사하게도 Rails는 개발 머신에서만 동작되는 로컬 Web서버를 구동시키기 위한 커맨드라인 프로그램이 있기 때문에 `rails server` 라는 커맨드를 실행시키는 것만으로도 Rails 어플리케이션을 간단하게 동작시킬 수 있습니다.

```
$ cd ~/environment/hello_app/
$ rails server
=> Booting Puma
=> Rails application starting on http://localhost:3000
=> Run `rails server -h` for more startup options
=> Ctrl-C to shutdown server
```

Javascript 런타임이 설치되어있지 않다는 에러가 출력되는 경우에는,  [GitHub의execjs 페이지](https://github.com/sstephenson/execjs)  에 있는 인스톨 가능한 런타임 리스트에서 Javascript 런타임 리스트를 확인해주세요. 개인적으로는 *Node.js* 를 추천합니다.

`rails server` 커맨드의 실행은 다른 터미널탭에서 실행하는 걸 추천합니다. 이렇게하면 맨 처음 실행되어져있던 터미널 탭에서는 다른 커맨드를 실행할 수 있기 때문입니다. 아래의 그림을 확인해주세요. 이미 처음에 열린 터미널에서 서버를 실행시키고 있는 경우에는, Ctrl+C 를 누르면 서버가 종료됩니다.  Rails 어플리케이션을 표시하고 싶은 경우에는, 로컬서버의 경우에는 `http://localhost:3000/` 을 브라우저로 접속해주세요. 클라우드IDE의 경우에는 *Share* 를 클릭하여 열고싶은 어플리케이션의 주소를 입력합니다. 어떤 경우에도 브라우저에 Rails 어플리케이션이 출력됩니다.
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/new_terminal_tab_aws.png)

![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/rails_server_new_tab_aws.png)
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/share_workspace_aws.png)
(클라우드IDE에서 서버를 실행하는 경우)
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/full_browser_window_aws.png)
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/riding_rails_4th_edition_aws.png)

##### 연습
	1. 기본 Rails 페이지에 표시되는 것에 비해, 자신의 환경에서의 루비 버전은 몇인가요? 커맨드라인에서 `ruby -v`를 실행해봅시다.
	2. Rails 버전도 알아봅시다. 알아본 버전은 위의 Gemfile에서 설치한 버전과 일치하나요?

### 1.3.3 Model - View - Controller (MVC)
이제 막 걸음마를 뗀 상태이지만 지금 Rails 어플리케이션의 전체적인 구조를 알아놓는것은 나중에 도움이 됩니다. 기본 Rails 어플리케이션 구조를 보면, `app/` 이라는 디렉토리가 있고, 그 안에 *models*, *views*, *controllers* 라고 하는 세 개의 서브디렉토리가 있는 것을 눈치채신 분들이 있을 겁니다. 여기서 Rails는  [MVC (model-view-controller)](https://ja.wikipedia.org/wiki/Model_View_Controller) 라고 하는 아키텍처 패턴을 채용하는 것을 알 수 있습니다. MVC는 어플리케이션 내부의 데이터(유저의 정보 등)와 데이터를 표시하는 코드를 분리합니다. 어플리케이션의 GUI는 이렇게 데이터와 분리되어 구성되어있습니다.

Rails 어플리케이션과 통신할 때, 브라우저는 일반적으로 Web서버에 Request를 보내고, 이것은 Request를 처리하는 역할을 하는 Rails의 Controller로 전달됩니다. 컨트롤러는 경우에따라서는 바로 View를 생성하여 HTML을 브라우저에 보냅니다. 동적인 사이트에서는 보통 컨트롤러는 사이트의 요소를 나타내고, 데이터베이스와 통신을 담당하는 Ruby오브젝트인 Model과 통신합니다. 모델을 호출한 후, 컨트롤러는 뷰를 표시하고 완성된 Web페이지를 HTML로 바꾸어 브라우저에 보냅니다.

![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/mvc_schematic.png)

지금 이 설명이 아직은 추상적으로 다가올지도 모르겠습니다만, 이 장은 나중에도 계속 참고하기 때문에 안심하셔도 될 것 같습니다. 특히 1.3.4에서는 MVC를 다루는 연습용 어플리케이션을 다룹니다. 2.2.2에서는 *toy* 어플리케이션을 이용하여 MVC를 좀 더 상세히 설명하겠습니다. *Sample* 어플리케이션에서는 MVC의 3가지 요소를 모두 다룹니다. 3.2에서 일단 컨트롤러와 뷰를 다루고, 모델은 6.1부터 다루어보겠습니다. 7.1.2에서는 세 개의 요소를 모두 다루게 됩니다.

### 1.3.4 Hello world !
역사적인 첫 Rails의 MVC Framewrok Application으로써 방금 작성한 어플리케이션을 아주 약간 손보기로 합시다. *Hello world!*  라고 하는 문자열을 표시하는 정도의 컨트롤러의 액션을 추가하고, 기본 Rails페이지를 바꿔봅시다. (컨트롤러 액션에 대해서는 2.2.2에서 상세히 다룹니다.)

이름에서부터 알 수 있듯이, 컨트롤러의 액션은 컨트롤러 안에 정의합니다. 여기서는 Application이라고 하는 이름의 컨트롤러 안에 hello라고 하는 이름의 액션을 작성하도록 해봅시다. 실제로 현 시점에서 컨트롤러는 Application 하나 밖에 없습니다. 다음 커맨드를 실행하면 현재 컨트롤러를 확인할 수 있습니다.
`$ ls app/controllers/*_controller.rb`
새로운 컨트롤러의 생성은 제2장에서 다룹니다. 아래의 리스트는 `hello` 를 정의한 것입니다. 여기서 `render` 메소드에서 *hello world !* 라고 하는 텍스트를 표시하고 있습니다. 여기서 Ruby의 문법에 대해서 신경쓸 필요는 없습니다. 제 4장에서 자세히 다룹니다.
```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception

  def hello
    render html: “hello, world!”
  end
end
```

표시하고 싶은 문자열을 보내는 액션을 정의했으니, 이번에는 기본 페이지 대신에 이 액션을 사용하도록 Rails에게 알려주도록 해야합니다. Rails의 라우터(router) 를 수정해봅시다. 라우터는 컨트롤러와 브라우저 사이에서 브라우저에서 리퀘스트가 오면 어떤 컨트롤러에 보내야할지를 결정하는 역할을 합니다. 여기서 기본 페이지를 바꿔야할 필요가 있기 때문에, Root routing(어플리케이션의 홈에 접속했을때의 라우팅) 을 수정해야합니다. 예를 들어서, `http://www.example.com/` 이라고 하는 URL의 제일 끝에 “/” 가 있기 때문에, 루트 URL은 간단히 “/“  를 붙여 표현할 수도 있습니다.

Rails의 라우팅 파일(`confing/routes.rb`) 에는  [Railse의 라우팅](http://railsguides.jp/routing.html)  을 참조하라는 코멘트가 있고, 루트 라우팅의 구성방법이 적혀져 있습니다. 구체적인 라우팅문법은 다음과 같습니다.
`root 'controller_name#action_name'`
위 코드의 경우, 컨트롤러이름은 application이고 액션이름은 hello가 되겠습니다. 변경 후의 코드는 아래와 같습니다.
```ruby
Rails.application.routes.draw do
  root 'application#hello'
end
```
컨트롤러와 라우팅파일을 수정하고 나면, 홈화면(루트 디렉토리) 에서 *hello world!*가 출력되는 것을 확인할 수 있습니다.
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/hello_world_hello_app.png)

##### 연습
1. hello 액션을 조금 수정해서 *hello world!* 대신 *hola mundo!* 가 출력되게끔 수정해봅시다.
2. Rails에서는  **[ASCII](http://ja.wikipedia.org/wiki/ASCII) 코드가 아닌 문자** 도 지원합니다. *¡Hola, mundo!* 에는 스페인어 특유의 역느낌표 *¡*가 포함되어 있습니다. 이 문자를 복사해서 붙여넣어 표시해보도록 합시다.
3. `hello` 액션을 참고하여, 새로운 액션 `goodbye` 를 추가해봅시다. 이 액션은 *goodbye, world!* 라는 텍스트를 출력하도록 합니다. `routes.rb` 를 수정하여 루트디렉토리를 `hello`액션에서 `goodbye` 액션으로 수정해봅시다.
 ![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/goodbye_world.png)

## 1.4 Git을 이용한 버전관리
실제로 동작하는 Rails 어플리케이션을 처음으로 완성해보았습니다. 어플리케이션의 소스코드를 버전관리 해봅시다. 버전을 관리하지 않는다면, 어플리케이션이 동작하지 않는 것은 아니지만 대부분의 Rails개발자는 버전관리를 개발현장에서만큼은 필수불가결한 것으로 인식하고 있습니다. 또한 버전관리시스템을 사용한다면 프로젝트의 코드이력을 확인한다던지, 잘못해서 삭제하고 만 파일을 복구할 수도 있습니다.(롤백) 버전관리시스템을 숙지하는 것은 지금은 어떤 소프트웨어 개발자라던지 필수기술이라고 해도 과언은 아닐 것입니다.

버전관리시스템에도 여러가지가 있습니다만, Rails 커뮤니티에서는 Linux커널용으로 *Linus Torvalds* 에 의해 개발된 분산버전관리시스템  [Git](http://git-scm.com/) 이 주류입니다. Git은 이 자체만으로도 대단히 큰 주제이기에 전부 설명하자면 책 한 권 정도의 분량이 될 것입니다. 자세한 내용은  [Learn Enough Git to Be Dangerous](http://learnenough.com/git-tutorial) 을 참고해주세요.

소스코드의 버전관리는 *무슨 짓을 해서라도* 해야합니다. 버전관리는 Rails의 어떠한 개발현장에서라도 필요하고, 버전관리시스템을 응용하여 자신이 작성한 코드를 다른 개발자들에게 간단히 공유할 수 있습니다. 제일 첫 장에서 작성한 어플리케이션을 실제 배포환경에 배포할 수도 있습니다.

### 1.4.1 설치
추천 환경인 클라우드IDE에서는 Git이 기본적으로 설치되어 있기 때문에 추가적인 설치는 필요없습니다. 다만 따로 설치가 필요하신 분들은  [Learn Enough Git to Be Dangerous](http://learnenough.com/git-tutorial) 의[Installation and Setup](https://www.learnenough.com/git-tutorial#sec-installation_and_setup)  섹션을 참고해주세요.

#### git config의 설정
Git을 사용하기 전에, 제일 처음 한 번만 설정이 필요한 것이 있습니다. 이것은 *System setup* 이라고 하는 설정으로, 컴퓨터 1대당 1번만 해주면 됩니다.
```
$ git config —global user.name “Your Name”
$ git config —global user.email your.email@example.com
```
이 `git config` 에서 설정하는 이름이나 메일주소는, 이후 레포지토리에서 일반에게 공개되므로 주의해주세요.

#### git init으로 setup하기
이 다음으로, 레포지토리(혹은 리포지토리 Repository, 이하 Repo(레포)) 작성을 진행합니다. 일단 Rails 어플리케이션의 루트 디렉토리로 이동한 다음, 새로운 레포를 초기화해주세요.
```
$ git init
  Initialized empty Git repository in
  /home/ec2-user/environment/environment/hello_app/.git/
```

이후 `git add -A` 를 실행하여, 프로젝트의 파일을 전부 레포에 추가합니다.
위의 커맨드를 실행하면 현재 디렉토리에 있는 파일이 모두 추가됩니다. 단 `.gitignore` 에 기재되어 있는 파일은 레포에 추가되지 않습니다. `.gitignore` 파일은 `rails new` 커맨드를 실행하면 자동적으로 생성되며 Rails프로젝트에서 git의 레포지토리에 추가되면 안될 파일들이 기술되어 있습니다. 물론 자신이 직접 추가할 수도 있습니다.

Git에 프로젝트 파일을 추가하면, 처음에는 스테이징(Staging)이라고 하는 일종의 대기용 레포지토리에 추가되어 커밋대기 상태가 됩니다. 안전을 위해서 한번에 커밋되지 않기 위함입니다. 스테이징 상태를 알기 위해선 `git status` 를 실행합니다.
```
$ git status
On branch master  Initial commit

Changes to be committed:
  (use “git rm —cached <file>…” to unstage)

  new file:   .gitignore
  new file:   Gemfile
  new file:   Gemfile.lock
  new file:   README.md
  new file:   Rakefile
  .
  .
  .
```

스테이징에리어 대기중인 변경이력을 본격적으로 레포지토리에 반영(커밋, Commit) 해보도록 합시다.
```
$ git commit -m “Initialize repository”
[master (root-commit) df0a62f] Initialize repository
.
.
.
```

`-m` 옵션을 사용하면, 커밋메세지를 커맨드라인에서 직접 지정할 수 있습니다. `-m` 옵션을 사용하지 않는 경우에는 시스템의 기본 에디터가 실행되고, 커밋메세지를 입력할 수 있습니다. (본 튜토리얼에서는 기본 `-m` 옵션을 사용합니다.

여기서의 커밋에 대해 설명드리자면, Git에서의 커밋은 어디까지나 로컬머신 상에서의 조작이므로 주의해주세요. `git push` 커맨드로 변경한 내역을 리포트 레포에 푸시하는 방법은 1.4.4에서 설명합니다.

덧붙여 `log` 커맨드를 사용하면 커밋메세지의 이력을 확인할 수 있습니다.
```
$ git log
commit: af72946fbebc15903b2770f92fae9081243dd1a1
Author: Michael Hartl <michael@michaelhartl.com>
Date:   Thu May 12 19:25:07 2016 +0000

    Initialize repository
```

로그가 어느정도이상 긴 경우에는 `q`를 눌러 종료할 수 있습니다.  [Learn Enough Git to Be Dangerous](http://learnenough.com/git-tutorial) 에서도 설명하고 있습니다만, `git log`에서는 `less` 커맨드를 인터페이스로써 사용하고 있습니다. 또한  less에 대해서는 같은 책  [Less is More](https://www.learnenough.com/command-line-tutorial#sec-less_is_more) 에서도 설명하고 있습니다.

### 1.4.2 Git을 쓰면 좋은 점
지금 시점에서는 소스코드를 버전관리해야하는 이유에 대해 잘 이해가 안될 수도 있습니다. 하나의 예를 들어 설명드리겠습니다.
만약 여러분이 중요한 `app/controller/` 폴더를 전부 삭제했다고 해봅시다.
```
$ ls app/controllers/
application_controller.rb  concerns/
$ rm -rf app/controllers/
$ ls app/controllers/
ls: app/controllers/: No such file or directory
```
여기서는 Unix커맨드의 `ls`의 `app/controller`  의 내용을 표시한 후  `rm` 커맨드로 내용을 전부 삭제해버렸습니다.  또한 `-rf` 옵션은 강제로 삭제여부를 묻지않고 삭제하는 내용입니다.

현재 상태를 확인해봅시다
```
$ git status
On branch master
Changed but not updated:
  (use “git add/rm <file>…” to update what will be committed)
  (use “git checkout — <file>…” to discard changes in working directory)

      deleted:    app/controllers/application_controller.rb

no changes added to commit (use “git add” and/or “git commit -a”)
```

파일이 몇개가 삭제가 되었습니다만, 이 변경은 현재 작업트리 내부이기 때문에 아직 커밋되지 않은 상태입니다. 즉, 이전의 커밋을 `checkout` 커맨드(와 현재까지의 변경을 강제적으로 덮어쓰기를 하고 되돌리는 `-f` 옵션) 으로 체크아웃하면, 간단하게 삭제 전의 상태로 되돌릴 수 있습니다.
```
$ git checkout -f
$ git status
# On branch master
nothing to commit (working directory clean)
$ ls app/controllers/
application_controller.rb  concerns/
```
삭제한 디렉토리와 파일을 무사히 복구할 수 있었습니다. 

### 1.4.3 Bitbucket
Git을 사용하여 프로젝트를 버전을 관리하기 시작했습니다. 다음은  [Bitbucket](http://www.bitbucket.com/) 에 소스코드를 업로드해봅시다. Bitbucket은 Git레포지토리의 호스팅과 공유에 특화된 사이트입니다. ( [Learn Enough Git to Be Dangerous](http://learnenough.com/git-tutorial) 에서는 [GitHub](http://www.github.com/) 을 사용합니다만, 컬럼 1.4에서 Bitbucket을 사용하는 이유에 대해 설명합니다.) 레포지토리를 Bitbucket에 일부러 푸시하는데에는 두가지 이유가 있습니다. 첫 번째 이유로는 소스코드(와 변경내역 전부)의 완전한 백업을 하는 것입니다. 두 번째 이유로는 다른 개발자와 공동작업을 보다 간단히 하기 위해서입니다.

###### 컬럼 1.4. Github와 Bitbucket
> Github와 Bitbuck은 Git레포지토리를 다루는 두 개의 유명한 서비스입니다. 양쪽 모두 대단히 비슷한 서비스입니다. Git 레포지토리의 호스팅과 공동작업을 하는 것이 가능하며 레포지토리의 표시나 검색하기 편하게 되어있습니다. 본 튜토리얼에서 두 개중에 하나를 정할 때 참고한 중요한 차이점은, Github는 레포지토리를 일반에 공개할 때는 무료지만 공개하지 않는 경우에는 유료이지만, Bitbucket 은 공동작업자가 일정 인원 이하라면 레포지토리를 공개하지 않아도 무료이며 일정 인원 이상이라면 유료라는 점입니다. 또한 어느 쪽이던 용량제한은 없습니다. 독자분들의 목적에 맞게 서비스를 선택하면 될 것 같습니다.  
> [Learn Enough Git to Be Dangerous](http://learnenough.com/git-tutorial)  및 본 튜토리얼의 옛날 버전에서는 오픈소스의 서포트를 강조하는 Github를 채용하고 있었습니다. 그러나 본 튜토리얼에서는 Web어플리케이션의 다양한 코드를 다루기 때문에, 모든 레포지토리가 기본으로 비공개 상태인것이 보안상 좋을 것 같다고 생각했습니다. 구체적으로는 본 튜토리얼 후반에서 다루는 코드에서는 암호화키나 패스워드 등 기밀정보가 포함되어 있습니다. 이러한 정보가 공개된다면 사이트의 보안이 위협받을 수도 있습니다. 물론 .gitignore에 작성하면 적절히 다룰 수 있겠지만 그것은 그것대로 경험이 필요하며 익숙한 개발자에게도 가끔 실수를 저지르고 마는 부분입니다. 그러한 이유로 리포트 레포지토리는 기본으로 비공개로 하는 것이 좋을 것 같았습니다. 그리고 Github에서는 레포지토리를 비공개를 하는 경우에는 유료이지만 Bitbucket은 비공개라도 용량제한없이 무료로 사용할 수 있습니다. 이 때문에 본 튜토리얼에서는 Github보다 Bitbucket을 사용하고 있습니다.  
>   
> (최근에는  [GitLab](http://gitlab.com/) 이라고 하는 Git호스팅 서비스가 제 3세력으로 떠오르고 있습니다. 원래는 오픈소스의 Git 프로그램이었으나 지금은 호스팅 서비스도 제공하고 있으며 공개, 비공개형의 레포지토리도 이용할 수 있습니다. 차후에는 Github이나 Bitbucket에 Gitlab도 유력한 세력이 될 것 같습니다.)  
>   
> 갱신예고: 2019년 1월부터 Github의 비공개 레포지토리도  [무료가 되었습니다.](https://blog.github.com/2019-01-07-new-year-new-github/)  공동작업을 ㅎ라 수 있는 멤버 인원수가 제한되어 있지만 비공개형 레포지토리가 이용할 수 있게된 만큼, 본 튜토리얼에서도 Bitbucket에서 Github로 바꿀 가능성이 있습니다.  
Bitbucket에서 프로젝트를 공개하는건 간단하지만 조금 기술적으로 필요한 부분이 있기에, 다음의 순으로 진행해주세요.
	1. Bitbucket의 ID가 없는 경우에는  [회원가입을 진행해주세요](https://bitbucket.org/account/signup/) .
	2.  [공개키](https://ja.wikipedia.org/wiki/%E5%85%AC%E9%96%8B%E9%8D%B5%E6%9A%97%E5%8F%B7)  를 복사합니다. 이미 공개키가 있는 경우에는 아래의 커맨드로 공개키를 확인할 수 있을 것입니다.  아직 공개키를 등록하지 않은 경우에는, 아래 커맨드를 실행해도 *No such file or directory* 라고 출력될 것입니다. 아적 작성되지 않은 경우에는 [Git - SSH 공개키 만들기](https://git-scm.com/book/ko/v2/Git-%EC%84%9C%EB%B2%84-SSH-%EA%B3%B5%EA%B0%9C%ED%82%A4-%EB%A7%8C%EB%93%A4%EA%B8%B0)를 참고해주세요.
	3. Bitbucket에 공개키를 추가하기 위해서는, 왼쪽아래의 아바타 그림을 클릭하여 Bitbucket 설정, SSH키 순으로 선택합니다.

`$ cat ~/.ssh/id_rsa.pub`
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/add_public_key.png)
공개키의 추가가 끝났으면 *Add key* 를 클릭하여  [새로운 레포지토리를 작성](https://bitbucket.org/repo/create) 합니다. 프로젝트의 정보를 입력한 후, *This is a private repository* 를 체크합니다. 또한 *Include a README?* 가 *No* 로 되어있는 것을 확인해주세요. *Create repository* 를 눌러 다음 화면의 지시에 따라주세요. 

![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/create_first_repository_bitbucket_4th_ed.png)
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/add_repository.png)

마지막으로, 아래의 커맨드를 실행해주세요. 제대로 실행되지 않으면 공개키가 제대로 추가되지 않았을 수 있으니, 다시 한 번 진행해보시길 바랍니다.
아래의 커맨드를 실행하는 것으로 로컬환경의 레포지토리를 Bitbucket으로 푸시할 수 있습니다. 푸시 할 떄에는 *Are you sure you want to continue connecting (yes/no)?* 라고 출력될 때도 있습니다만, 이때는 *yes*를 입력해주세요.

```
$ git remote add origin git@bitbucket.org:유저이름/hello_app.git
$ git push -u origin —all
```

위 커맨드의 첫 줄은 Bitbucket를 레포지토리를 origin 이라고 하는 이름으로 Git의 설정파일에 추가하기 위한 것입니다. 그 다음 커맨드는 로컬 레포지토리를 리모트의 origin에 푸시하는 것입니다.(`-u` 의 옵션은 신경안쓰셔도 됩니다. 알고 싶으신 분은 *git set upstream* 에 대해 검색해주세요) 또한 유저이름 부분에는 실제 유저 이름을 바꿔넣을 필요가 있습니다. 예를들어 저자의 경우 실행하는 커맨드는 다음과 같습니다.
`git remote add origin git@bitbucket.org:railstutorial/hello_app.git`
위 커맨드를 실행하면 *hello_app* 의 레포지토리의 페이지가 Bitbucket에 작성됩니다. 이 페이지에서는 파일의 참조, 모든 커밋이력의 확인 등 많은 다른 기능을 이용할 수 있습니다.

![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/bitbucket_repository_page_4th_ed.png)

### 1.4.4 Branch, Edit, Commit, Merge
위의 순서에 따라 진행한다면, `README` 파일의 내용이 자동적으로 표시되는 걸 알아차리셨을 겁니다. 이 `README.md` 파일은 `rails new` 를 실행한다면 자동생성됩니다. 파일의 확장자가 `.md` 로 되어있는 파일은 *Markdown* 이라고 하는 , 사람이 읽기 편한 표기법으로 작성되어 있는 파일입니다. Markdown은 간단하게 HTML로 변환할 수 있어서 Bitbucket 이나 Github도 `.md` 파일을 자동으로 HTML변환하여 표시합니다.

Rails가 자동생성해주는 README파일을 그냥 그대로 써도 되고, 프로젝트에 맞추어 내용을 수정해도 상관없습니다. 본 튜토리얼에서는 README파일을 Rails 튜토리얼 고유의 내용으로 수정해봅시다. 동시에 Git에서 Branch, Edit, Commit, Merge를 실행 할 때 권해드리는 워크플로우의 실제 예를 봅시다.
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/bitbucket_default_readme.png)

#### Branch (브랜치)
Git은, 브랜치(*branch*) 를 매우 간편하고 빠르게 만들 수 있습니다. 브랜치는 기본적으로는 레포지토리의 복사판으로, 브랜치에서는 원본 파일을 수정하지 않고 새로운 코드를 작성하는 등의 자유로운 변경과 실험을 해볼 수 있습니다. 보통 원본 레포지토리를 *master* 브랜치라고 하며 토픽 브런치(단기간 사용하는 일시적인 브랜치) 는 `checkout` 과 `-b` 옵션을 이용하여 작성합니다.
```
$ git checkout -b modify-README
Switched to a new branch ‘modify-README’
$ git branch
  master
* modify-README 
```

두번째 커맨드 (`git branch`) 는 모든 로컬 브랜치의 리스트를 표시합니다. `*` 가 붙은 브랜치는 현재 사용중이라는 의미입니다. 첫번째 커맨드의 `git checkout -b modify-README` 커맨드로, 브랜치의 신규작성과 그 브랜치로의 브랜치전환을 동시에 수행하고 있습니다. `modify-README` 브랜치에 `*` 가 붙어 있는 것으로 이 브랜치가 현재 사용중이라는 것을 알 수 있습니다.

브랜치의 진가는 다른 개발자와 협업을 할 때 알 수 있습니다. 본 튜토리얼과 같은, 1인작업을 할 때도 매우 유용합니다. *master* 브랜치는 토픽브랜치에서 수행한 작업으로부터 영향받지 않기 때문에 설령 브랜치 상의 코드가 엉망진창이 되어버렸다고 하더라도 master브랜치로 체크아웃하여 토픽브랜치를 삭제하면 언제든지 변경사항을 파기할 수 있습니다. 구체적인 방법은 이번 장의 마지막에 설명드리겠습니다.

여담으로, 보통 이러한 작은 변경을 위해서 일부러 브랜치를 작성할 필요는 없습니다만, *좋은 습관을 만드는 것*에 때가 정해져 있는 것이 아니기 때문에 지금부터라도 조금씩 연습해봅시다.

#### Edit(편집)
토픽브랜치를 만들고 나면, README를 편집하여 커스텀 컨텐츠를 넣어봅시다.
	- README.md
```
# Ruby on Rails Tutorial

## “hello, world!”

This is the first application for the
[*Ruby on Rails Tutorial*](https://railstutorial.jp/)
by [Michael Hartl](http://www.michaelhartl.com/). Hello, world!
```

#### Commit (커밋)
편집이 끝났으면 브랜치의 상태를 확인해봅시다.
```
$ git status
On branch modify-README
Changes not staged for commit:
  (use “git add <file>…” to update what will be committed)
  (use “git checkout — <file>…” to discard changes in working directory)

        modified:   README.md

no changes added to commit (use “git add” and/or “git commit -a”)
```

지금 상태에서 `git add -A` 를 실행해도 되지만, `git commit` 에는 현재 디렉토리에 존재하는 모든 파일에서의 변경사항을 한번에 커밋을 하는 `-a` 옵션이 있습니다. 이 옵션도 자주 사용됩니다.
```
$ git commit -a -m “Improve the README file”
[modify-README 9dc4f64] Improve the README file
 1 file changed, 5 insertions(+), 22 deletions(-)
```

`-a` 옵션은 신중하게 사용하시길 바랍니다. 커밋한 다음 새로운 파일을 추가한 경우에는, 일단 `git add` 를 실행하여 해당 파일을 Git에서 추적할 수 잇또록 해야합니다.

커밋메세지에는  **현재형** 이면서 **[명령형](https://en.wikipedia.org/wiki/Imperative_mood#Korean)**  으로 작성하도록 합시다. Git의 모델은, 일련 패치형태로 진행됩니다. 때문에 커밋메세지를 쓸 때에는 해당 커밋이 "무엇을 했는지” 과거형으로 작성하는것 보다 “무엇을 한다” 라는 식으로 작성하는 편이 나중에 커밋이력을 확인할 때에 알기 쉬워집니다. 게다가 현재형이면서 명령형으로 쓴다면 Git커맨드 자체에 의해 작성되는 커밋메세지와 시제가 맞아떨어집니다. 자세한 것은  [Learn Enough Git to Be Dangerous](http://learnenough.com/git-tutorial) 의 [Committing to Git](https://www.learnenough.com/git-tutorial#aside-commit_messages) 을 참고해주세요. (역자: 이것은 영어의 경우이므로 참고해주세요)

#### Merge (머지)
파일의 수정작업이 끝났으므로, master브랜치로 해당 변경사항을 머지해봅시다.
```
$ git checkout master
Switched to branch ‘master’
$ git merge modify-README
Updating af72946..9dc4f64
Fast-forward
 README.md | 27 +++++———————————
 1 file changed, 5 insertions(+), 22 deletions(-)
```

Git 메세지 출력에는  `34f06b7` 과 같은 문자열(해쉬)가 포함되어 있습니다. Git은 이러한 문자열을 레포지토리 내부처리에 사용합니다. 이 문자열은 환경에 따라 위와 조금 다를 수도 있습니다만 다른 부분은 거의 비슷합니다.

변경사항을 머지한 다음에는 `git branch -d` 를 실행하여 토픽브랜치를 삭제해주세요.
```
$ git branch -d modify-README
Deleted branch modify-README (was 9dc4f64).
```

토픽브랜치의 삭제는 필수는 아닙니다. 실제로 토픽브랜치를 삭제하지 않고 그대로 두는 것은 자주 있는 일입니다. 토픽브랜치를 삭제하지 않고 남겨둔다면, 토픽브랜치와 master브랜치를 사이를 계속 변경해나가며 타이밍 좋을 때 변경사항을 머지하는 것이 가능합니다.

위에 설명한 것과 같이, `git branch -D` 를 실행하여 토픽브랜치를 삭제할 수도 있습니다.
```
# これはあくまで例です。ブランチでミスをした時以外は実行しないでください。
$ git checkout -b topic-branch
$ <ファイルを作成したり編集したり削除したり…>
$ git add -A
$ git commit -a -m “Make major mistake”
$ git checkout master
$ git branch -D topic-branch
```

`-d` 와 달리 `-D` 옵션은 변경사항을 머지하지 않고도 브랜치를 삭제할 수 있습니다.

#### Push (푸시)
`README` 파일의 수정이 끝났으므로, Bitbucket에 변경사항을 푸시해서 결과를 확인해봅시다. 이미 위에서 한 번 푸시를 했기 때문에, 대부분의 시스템에서는 `git push` 를 할 때에 `origin master` 를 생략할 수 있습니다.
`git push`	
기본 README파일과 마찬가지로, 수정된 README파일도 Bitbucket에 의해 HTML로 변환되어 표시됩니다.
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/new_readme_bitbucket_4th_ed.png)

## 1.5 배포해보자
아직 1장의 튜토리얼이 한창입니다만, 이런 별거없는 어플리케이션도 실제 배포환경에 배포하려고 마음먹으면 가능한 상태이긴 합니다. 어플리케이션의 배포는 필수는 아니지만, 빈번하게 실제환경에 배포하는 것으로 개발 사이클의 문제를 빠른 단계에서 찾아 대응할 수도 있습니다. 개발환경의 테스트를 반복하기만하고 언제까지나 실제 환경에 배포하지 않으면 어플리케이션을 공개하는 단계에서 생각지도못한 사태에 직면할수도 있습니다.

예전에는 Rails 어플리케이션의 실제환경으로의 배포작업은 무척이나 힘든 작업이었으나 요근래 들어서는 매우 간편해졌습니다. 다양한 실제환경을 선택할 수 있습니다.  [Phusion Passenger](https://www.phusionpassenger.com/) (Apache나 Nginx등의 웹서버용 모듈)을 실행할 수 있는 다양한 공유호스트나 가상 프라이빗 서버(VPS) 외에도 간편한 배포환경을 제공하는  [Engine Yard](http://engineyard.com/) 나 [Rails Machine](http://railsmachine.com/) , 클라우드 서비스를 제공하는 [Engine Yard Cloud](http://cloud.engineyard.com/) 나 [Heroku](http://heroku.com/) 등이 있습니다.

저자의 취향은 Heroku인데, Rails를 포함한 Ruby Web어플리케이션 용의 호스팅플랫폼입니다. Heroku는 소스코드의 버전관리에 Git을 사용하고 있다면, Rails 어플리케이션을 간단하게 실제환경에 배포할 수 있습니다. (사실 Git을 쓰는 것은 Heroku를 위해서라도 할 정도입니다.) 게다가 Heroku의 Free tier에서는 튜토리얼 용도를 포함한 다양한 용도를 위한 기능을 충분히 사용할 수 있습니다.

본 장에서는 첫 어플리케이션을 Heroku에 배포해봅니다. 작업내용의 일부에는 조금 어려운 내용도 있습니다만, 지금 당장 이해할 필요는 없습니다. 중요한 것은 이번 장이 끝날 때 즈음이면 작성한 어플리케이션을 실제 웹서비스로써 배포한다는 것입니다.

### 1.5.1 Heroku 설치

Heroku에서는  [PostgreSQL](http://www.postgresql.org/) 를 데이터베이스로써 사용합니다. (여담으로, 발음은 *post-gres-cue-ell*이며, *Postgres* 라고 칭하기도 합니다.)  실제(Production)환경에 `pg gem`을 설치하여 Rails가 PostgreSQL과 통신할 수 있도록 해야합니다.
```ruby
group :production do
  gem ‘pg’, ‘0.20.0’
end
```

Heroku는 SQLite를 지원하지 않기 때문에 아래와 같은 수정을 하여  sqlite3 gem이 실제환경에 적용되지 않도록 해야합니다.
```ruby
group :development, :test do
  gem ‘sqlite3’, ‘1.3.13’
  gem ‘byebug’,  ‘9.0.6’, platform: :mri
end
```

결과적으로 `Gemfile`의 내용은 아래처럼 됩니다.
```ruby
source ‘https://rubygems.org’

gem ‘rails’,        ‘5.1.6’
gem ‘puma’,         ‘3.9.1’
gem ‘sass-rails’,   ‘5.0.6’
gem ‘uglifier’,     ‘3.2.0’
gem ‘coffee-rails’, ‘4.2.2’
gem ‘jquery-rails’, ‘4.3.1’
gem ‘turbolinks’,   ‘5.0.1’
gem ‘jbuilder’,     ‘2.7.0’

group :development, :test do
  gem ‘sqlite3’, ‘1.3.13’
  gem ‘byebug’,  ‘9.0.6’, platform: :mri
end

group :development do
  gem ‘web-console’,           ‘3.5.1’
  gem ‘listen’,                ‘3.1.5’
  gem ‘spring’,                ‘2.0.2’
  gem ‘spring-watcher-listen’, ‘2.0.1’
end

group :production do
  gem ‘pg’, ‘0.20.0’
end

# Windows環境ではtzinfo-dataというgemを含める必要があります
gem ‘tzinfo-data’, platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

실제환경용의 gem(이번의 경우에는 pg gem)을 로컬환경에서 인스톨하지 않기 위해서는 `bundle install` 에 특수한 옵션인 `--without production` 옵션을 추가해서 실행해줍니다.
`$ bundle install --without production`

위 `Gemfile` 내용에서 추가한 gem은 실제환경에서밖에 사용하지 않는 gem이기 때문에, 위의 옵션을 추가한 커맨드를 실행한다면 로컬환경에는 실제환경용 gem이 설치되지 않습니다. 그럼 왜 위의 커맨드를 실행했냐하면, pg gem을 추가한 것과 Ruby의 버전을 지정한 것을 `Gemfile.lock` 에 반영하지 않으면, 실제 환경으로의 배포가 되지 않기 때문입니다. 지금부터 작업할 실제환경으로의 배포를 성공시키기 위해서는 변경한 내용을 커밋하는 것도 까먹지 않도록 해줍시다.
`$ git commit -a -m "Update Gemfile for Heroku"`

다음으로 Heroku 의 아이디를 만들어서 설정해봅니다.  [Heroku 회원가입](http://signup.heroku.com/) 을 진행합니다. 그 다음으로 자신의 시스템에 Heroku 커맨드라인 클라이언트가 설치되어 있는지 확인해봅니다.
`$ heroku --version`

Heroku가 설치되어 있다면, 버전번호와 함께 `heroku` 커맨드라인 인터페이스(CLI)가 이용가능하다는 메세지가 표시될 것입니다. 단, 대부분의 경우에는 설치되어있지 않기 때문에,  [Heroku Toolbelt](https://toolbelt.heroku.com/) 을 이용하여 설치할 필요는 있습니다. 클라우드IDE를 사용하는 경우라면 아래의 커맨드를 실행하여 Heroku CLI가 설치될 것입니다. (꽤나 상급 커맨드로, 내용은 크게 신경쓰지 않아도 됩니다.)
`$ source <(curl -sL https://cdn.learnenough.com/heroku_install)`
위 커맨드라인으로 설치되었는지는 다음 커맨드로 확인할 수 있습니다. 제대로 설치되었다면 버전의 번호가 같이 출력될 것입니다. (버전이 본 예제와 달라도 상관없습니다.)
```
$ heroku —version
heroku-cli/6.15.5 (linux-x64) node-v9.2.1
```

Heroku의 커맨드라인 인터페이스(CLI)가 설치된 것을 확인했다면, 드디어 `heroku` 커맨드로 로그인하여 SSH키를 추가합니다.
```
$ heroku login —interactive
$ heroku keys:add
```

끝으로 `heroku create` 커맨드를 실행하여 Heroku서버에 예제 어플리케이션의 실행장소를 만듭니다.

```
$ heroku create
Creating app… done, fathomless-beyond-39164
https://damp-fortress-5769.herokuapp.com/ |
https://git.heroku.com/damp-fortress-5769.git
```

이 `heroku` 커맨드를 실행하면, Rails 어플리케이션의 전용 서브도메인이 작성되어 지금 당장 브라우저에서 표시가능한 상태가 됩니다. 지금은 아직 암구서도 없지만, 바로 배포하여 Web페이지에서 확인해보도록 해봅시다.

### 1.5.2 Heroku에 배포해보자 (1)
Rails 어플리케이션을 실제로 Heroku에 배포하기 위해선 일단 Git을 이용하여 Heroku 레포지토리에 푸시합니다.
` git push heroku master`

(경고 메세지가 약간 출력될 수도 있지만 지금은 무시하셔도 될 수준입니다. 자세한 내용은 7.5에서 다룹니다.)

### 1.5.3 Heroku에 배포해보자 (2)
사실 위의 커맨드로 배포작업은 끝입니다. 배포된 어플리케이션을 웹에서 확인하기 위해선, `heroku create` 를 실행했을 때 생성된 어드레스를 브라우저로 접속하여 확인하면 됩니다. (물론 예제에서 표시된 주소가 아닌, 여러분께서 작성한 주소로 접속해주세요.)  클라우드IDE가 아닌 로컬 컴퓨터에서 작업하고 계신 경우에는, `heroku open` 커맨드로 브라우저에서 표시할 수 있습니다. 실행 결과는 아래와 같습니다. 페이지 내용은 로컬환경에서 작업한 내용과 다를 것이 없습니다만, 지금은 그 내용이 인터넷 상의 실제 환경에 하나의 웹페이지로써 당당하게 공개되어 있습니다.
![](/Users/Yoo/Documents/Github/Ruby On Rails/Rails_Tutorials_Translation/image/제 1장 제로부터 배포까지/heroku_app_hello_world.png)

##### 연습
	1. 실제 환경에서도 *hola, mundo!* 라고 출력되도록 해보세요.
	2.  루트 라우팅을 변경하여 `goodbye` 액션의 결과가 출력되도록 해보세요. 또한 배포시의 Git push의 `master` 를 일부러 생략하고, `git push heroku` 로만 배포하는 방법을 찾아보세요.

### 1.5.4  Heroku  커맨드
 [Heroku의 커맨드](http://devcenter.heroku.com/heroku-command) 는 엄청 많기 때문에, 여기서는 간단한 설명만 드리겠으나, 조금은 써보도록 해봅시다. 어플리케이션의 이름은 수정해봅시다.
`$ heroku rename rails-tutorial-hello`
주의 : 이 이름은 저자의 예제 어플리케이션에서 이미 쓴 이름이기 때문에, **반드시 다른이름을 사용해주**세요. 실제로 Heroku에서 생성된 기본 어드레스도 충분합니다.진짜 어플리케이션의 이름을 바꿔보고 싶은 경우에는 다음과 같은 랜덤한 서브도메인명을 설정하여 이번 장의 앞머리에 설명한 어플리케이션의 안전성(Security)를 구현해보는 방법도 있습니다.
```
hwpcbmze.herokuapp.com
seyjhflo.herokuapp.com
jhyicevg.herokuapp.com
```
이렇게 대충대충 되어져 있는 도메인이기 때문에, URL을 알려주지 한 누군가가 접근할 걱정은 안하셔도 될 것 같습니다. 여담으로 Ruby의 한 기능을 보여드리기 위해, 서브도메인용으로 랜덤한 문자열을 생성하는 간단한 코드를 작성해 봅시다.
`('a'..'z').to_a.shuffle[0..7].join`
정말 간단하네요!

Heroku에서는 서브도메인 이외에도 고유 도메인도 사용할 수 있습니다.  하나의 예를 들자면,  [본 튜토리얼](https://railstutorial.jp/)  또한 Heroku에 배포되어있습니다. 따라서 본 튜토리얼을 온라인으로 읽고 계시다면, 그야말로 지금 Heroku에 호스팅되어있는 웹사이트를 보는 것입니다. 커스텀도메인이나 Heroku의 다른 정보에 대해서는  [Heroku Document](http://devcenter.heroku.com/) 를 참고해주세요.

##### 연습
	1. `heroku help` 커맨드를 실행하여 Heroku의 커맨드 리스트를 출력해보세요. Heroku 어플리케이션의 로그를 표시하는 커맨드는 무엇일까요?
	2. 위 연습에서 찾은 커맨드를 이용하여 Heroku 어플리케이션의 최근 로그를 확인해봅시다. 제일 최근에 발생한 이벤트는 무엇이었나요? (이 로그를 알아보는 커맨드를 알아놓으면, 실제 환경에서의 버그를 찾아낼때 꽤나 도움됩니다.)

## 1.6  마지막으로
이번 장에서는 설치, 개발환경의 설정, 버전관리, 실제환경으로의 배포 등 많은 과업을 이루었습니다. 다음 장에서는 이번 장에서 배운 것을 기초로 데이터베이스를 갖춘 *toy* 어플리케이션을 만들고, Rails에서는 어떠한 것이 가능한지 좀 더 자세히 배워봅니다.

여기까지의 진척을 Twitter에 트윗해보지 않겠습니까? 해시태그 @railstutorial! 를 이용하여 투고해보세요. 다른사람들에게 자신이 Rails를 공부하고 있다는 것을 알릴 수 있습니다. 
 [Rails Tutorial email list](https://www.railstutorial.org/#email) 로의 참가는 어떠신가요? Rails 튜토리얼의 업데이트를 재빠르게 알 수 있는 한 편, 좋은 캠페인이 있다면 알려드릴 수도 있습니다.

### 1.6.1 1장의 정리
	- Ruby on Rails는, Web개발을 위한 프레임워크로 Ruby프로그래밍 언어로 작성되었습니다.
	- 사전설정이 완료되어 있는 클라우드환경을 이용하여 Rails의 설치, 어플리케이션의 생성, 생성된 파일의 수정등을 간단히 진행하였습니다.
	- Rails는 `rails` 라고 하는 이름의 커맨드라인 커맨드가 있으며, `rails new` 로 새로운 어플리케이션을 생성할 수 있고, `rails server` 로 로컬 서버를 실행할 수 있습니다.
	- 컨트롤러의 액션을 추가하고 루트 라우팅 을 변경하는 것만로도 *hello world* 어플리케이션을 작성할 수 있습니다.
	- Git으로 버전을 관리하여 Bitbucket의 비공개 레포지토리에 푸시하는 이유는 데이터의 손실을 방지하고 다른 개발자와의 공동작업을 진행하기 위함입니다.
	- 작성한 어플리케이션을 Heroku의 실제 환경에 배포했습니다.

#Ruby On Rails/Ruby on Rails Tutorial PJ#