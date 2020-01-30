### 13.3.4 Micropost를 삭제해보자

마지막 기능으로는 micropost resource에 post를 삭제하는 기능을 추가해봅니다. 이것은 유저 삭제와 마찬가지로 ([10.4.2](Chapter10.md#1042-destroy액션)) "delete" 링크로 실현해봅시다. 유저의 삭제는 관리자 유저만 가능할 수 있도록 제한한 것에 비해, 이번에는 자신이 작성한 micropost에 대해서만 삭제링크를 동작하도록 해봅시다.

![](../image/Chapter13/micropost_delete_links_mockup_3rd_edition.png)

제일 처음으로는 micropost의 partial 에 삭제링크를 추가해봅니다. 생성한 코드는 아래와 같습니다.

```erb
<!-- app/views/microposts/_micropost.html.erb -->
<li id="micropost-<%= micropost.id %>">
  <%= link_to gravatar_for(micropost.user, size: 50), micropost.user %>
  <span class="user"><%= link_to micropost.user.name, micropost.user %></span>
  <span class="content"><%= micropost.content %></span>
  <span class="timestamp">
    Posted <%= time_ago_in_words(micropost.created_at) %> ago.
    <!-- add -->
    <% if current_user?(micropost.user) %>
      <%= link_to "delete", micropost, method: :delete,
                                       data: { confirm: "You sure?" } %>
    <% end %>
    <!-- add -->
  </span>
</li>
```

다음으로는, Microposts 컨트롤러의 `destrory` 액션을 정의해봅시다. 이것 또한 유저의 삭제와 대강 비슷합니다. 큰 차이로는 `admin_user` 필터에서 `@user` 변수를 사용하는 것이 아닌, 관계를 사용하여 micropost를 검색할 수 있다는 점입니다. 이것으로 어떤 유저가 다른 유저의 micropost를 삭제하려고 해도 자동적으로 실패해버릴 것입니다. 구체적으로는 `correct_user` 필터 내에서 `find` 메소드를 호출하는 것으로, 현재 유저가 삭제대상의 micropost를 보유하고 있는지를 확인합니다. 완성된 코드는 아래와 같습니다.

```ruby
# app/controllers/microposts_controller.rb
class MicropostsController < ApplicationController
  before_action :logged_in_user, only: [:create, :destroy]
  before_action :correct_user,   only: :destroy #new
  .
  .
  .
   #new
  def destroy
    @micropost.destroy
    flash[:success] = "Micropost deleted"
    redirect_to request.referrer || root_url
  end

  private

    def micropost_params
      params.require(:micropost).permit(:content)
    end
		#new
    def correct_user
      @micropost = current_user.microposts.find_by(id: params[:id])
      redirect_to root_url if @micropost.nil?
    end
end
```

이 때 위 코드의 `destory` 메소드에서 redirect를 사용하고 있는 점을 주목해주세요.

`request.referrer || root_url`

여기서는 `request.referrer` 라고 하는 메소드를 사용하고 있습니다. 이 메소드는 Friendly forwarding의 `request.url` 변수 ([10.2.3](Chapter10.md#1023-friendly-forwarding)) 와 비슷하게, 바로 이전 URL을 리턴합니다. (이 경우, Home페이지가 될 것 입니다.) 때문에 micropost가 Home페이지에서 삭제된 경우에도 프로필 페이지로부터 삭제된 경우에도, `request.referrer` 를 사용하는 것으로 DELETE Request가 실행된 페이지로 되돌아가기 때문에 매우 편리합니다. 여담으로, 이전으로 돌아가는 URL이 없다고 하더라도, (예를들어 테스트에서는 `nil` 이 리턴되는 경우도 있습니다.) 위 코드에서 `||` 연산자에 의하여 `root_url` 을 디폴트로 설정하고 있기 때문에 괜찮습니다. 



위 코드로 인하여 위에서부터 2번재 micropost 를 삭제하면 아래와 같이 동작할 것 입니다.

![](../image/Chapter13/home_post_delete_3rd_edition.png)

##### 연습

1. micropost를 작성하고, 그 다음 작성한 micropost를 삭제해봅시다. 다음으로 Rails 서버의 로그를 확인하여 `DELETE` 문의 내용을 확인해봅시다.
2. `redirect_to request.referrer || root_url`의 행을 `redirect_back(fallback_location: root_url)` 으로 바꾸어도 제대로 동작하는지, 브라우저를 통해 확인해봅시다. (이 메소드는 Rails 5부터 새롭게 추가되었습니다.)

### 13.3.5 Feed 화면의 Micropost 를 테스트해보자

[13.3.4](#1334-micropost를-삭제해보자) 의 코드에서, MIcropost 모델과 해당 인터페이스를 완성시켰습니다. 남은 내용으로는 Micropost 컨트롤러의 허가를 체크하는 짧은 테스트와 그것들을 정리하는 통합테스트 코드를 작성해볼 것 입니다.



우선은 micropost용의 fixture에 제각각의 유저와 연결되어진 micropost를 추가합니다. (지금은 이 중 1개만 사용합니다만 나중에는 다른 micropost도 사용하게 됩니다.)

```yml
# test/fixtures/microposts.yml
.
.
.
ants:
  content: "Oh, is that what you want? Because that's how you get ants!"
  created_at: <%= 2.years.ago %>
  user: archer

zone:
  content: "Danger zone!"
  created_at: <%= 3.days.ago %>
  user: archer

tone:
  content: "I'm sorry. Your words made sense, but your sarcastic tone did not."
  created_at: <%= 10.minutes.ago %>
  user: lana

van:
  content: "Dude, this van's, like, rolling probable cause."
  created_at: <%= 4.hours.ago %>
  user: lana
```

 다음으로 자신 이외의 유저의 micropost 는 삭제를 하려하면 적절하게 redirect하는 처리를 테스트에서 확인해봅시다.

```ruby
# test/controllers/microposts_controller_test.rb
require 'test_helper'

class MicropostsControllerTest < ActionDispatch::IntegrationTest

  def setup
    @micropost = microposts(:orange)
  end

  test "should redirect create when not logged in" do
    assert_no_difference 'Micropost.count' do
      post microposts_path, params: { micropost: { content: "Lorem ipsum" } }
    end
    assert_redirected_to login_url
  end

  test "should redirect destroy when not logged in" do
    assert_no_difference 'Micropost.count' do
      delete micropost_path(@micropost)
    end
    assert_redirected_to login_url
  end

  # new
  test "should redirect destroy for wrong micropost" do
    log_in_as(users(:michael))
    micropost = microposts(:ants)
    assert_no_difference 'Micropost.count' do
      delete micropost_path(micropost)
    end
    assert_redirected_to root_url
  end
end
```

 마지막으로 통합 테스트를 작성해봅니다. 이번 통합테스트에서는 로그인, micropost의 페이지 분할의 확인, 무효한 micropost의 작성, 유효한 micropost의 작성, micropost 삭제, 그리고 다른 유저의 micropost는 "Delete" 링크가 없는 것을 확인 하는 순서로 해봅시다. 언제나 처럼 통합 테스트를 생성해봅시다.

```
$ rails generate integration_test microposts_interface
      invoke  test_unit
      create    test/integration/microposts_interface_test.rb
```

앞의 순서대로 작성한 통합테스트는 아래와 같습니다. 이전에 작성한 코드가 살짝 섞여있는 점을 주의해주세요.

```ruby
# test/integration/microposts_interface_test.rb
require 'test_helper'

class MicropostsInterfaceTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:michael)
  end

  test "micropost interface" do
    log_in_as(@user)
    get root_path
    assert_select 'div.pagination'
    # 무효한 작성
    assert_no_difference 'Micropost.count' do
      post microposts_path, params: { micropost: { content: "" } }
    end
    assert_select 'div#error_explanation'
    # 유효한 작성
    content = "This micropost really ties the room together"
    assert_difference 'Micropost.count', 1 do
      post microposts_path, params: { micropost: { content: content } }
    end
    assert_redirected_to root_url
    follow_redirect!
    assert_match content, response.body
    # micropost를 삭제
    assert_select 'a', text: 'delete'
    first_micropost = @user.microposts.paginate(page: 1).first
    assert_difference 'Micropost.count', -1 do
      delete micropost_path(first_micropost)
    end
    # 다른 유저의 프로필로의 액세스 (삭제링크가 없는 것을 확인)
    get user_path(users(:archer))
    assert_select 'a', text: 'delete', count: 0
  end
end
```

이미 어플리케이션의 코드는 작성해놓았기 때문에, 이 테스트는 통과될 것 입니다.

`$ rails test`

##### 연습

1. 위 코드의 4개의 코멘트의 각각에 대해 테스트가 제대로 동작하는지를 확인해봅시다. 구체적으로는 대응하는 application 의 코드를 코멘트아웃하고 테스트가 실패하는 것을 확인하고, 원래대로 되돌린 후 테스트가 다시 통과되는지를 확인해봅시다.

2. 사이드 바에 있는 micropost의 합계 작성 개수를 테스트해봅시다. 이 때 단수형(micropost) 와 복수형 (microposts) 가 제대로 표시되는지도 테스트해봅시다. _Hint_ : 위 테스트 코드를 확인해봅시다

   ```ruby
   # test/integration/microposts_interface_test.rb
   require 'test_helper'
   
   class MicropostInterfaceTest < ActionDispatch::IntegrationTest
   
     def setup
       @user = users(:michael)
     end
     .
     .
     .
     # new
     test "micropost sidebar count" do
       log_in_as(@user)
       get root_path
       assert_match "#{FILL_IN} microposts", response.body
       # 아직 micropost를 작성하지 않은 유저
       other_user = users(:malory)
       log_in_as(other_user)
       get root_path
       assert_match "0 microposts", response.body
       other_user.microposts.create!(content: "A micropost")
       get root_path
       assert_match FILL_IN, response.body
     end
   end
   ```



## 13.4 Micropost의 image 첨부

여기까지 micropost에 관한 기본적인 조작은 모두 구현해보았습니다. 이번 섹션에서는 응용편으로써, 이미지를 첨부한 micropost를 작성할 수 있도록 해보겠습니다. 순서로는 일단 개발환경용의 베타판을 구현하고, 그 후 몇가지의 개선을 거쳐 실제 배포환경용의 완성판을 구현해보겠습니다.



임지ㅣ 업로드 기능을 추가하기 위해서는 2개의 시각적인 요소가 필요합니다. 하나는 이미지를 업로드하기 위한 form, 또 다른 하나는 작성한 이미지 그 자체입니다. [Upload image] 버튼과 이미지가 첨부되어있는 micropost의 목업은 아래와 같습니다.

![](../image/Chapter13/micropost_image_mockup.png)



### 13.4.1 기본적인 Image Upload

업로드한 이미지를 다루거나 해당 이미지를 micropost 모델과 관계맺기를 하기 위해서, 이번에는 [CarrierWave](https://github.com/carrierwaveuploader/carrierwave) 라고 하는 Image Uploader를 사용해보겠습니다. 우선 _carrierwave gem_ 을 `Gemfile` 에 추가해봅시다. 이 때, 아래 코드에서는 _mini_magick gem_ 과 _fog gems_ 도 같이 포함되어있는 점을 주목해주세요. 이 gem들은 Resize하거나(13.4.3) 실제 배포환경에서 이미지를 업로드하기 위해 사용될 것 입니다. (13.4.4)

```ruby
gem 'rails',                   '5.1.6'
gem 'bcrypt',                  '3.1.12'
gem 'faker',                   '1.7.3'
gem 'carrierwave',             '1.2.2' #new
gem 'mini_magick',             '4.7.0' #new
gem 'will_paginate',           '3.1.5'
gem 'bootstrap-will_paginate', '1.0.0'
.
.
.
group :production do
  gem 'pg',  '0.20.0'
  gem 'fog', '1.42' #new
  end
.
.
.
```

다음으로는 언제나처럼 `bundle install` 을 실행해봅니다.

` $ bundle install`

CarrierWave를 도입하면, Rails의 제네레이터로 이미지 업로더를 생성할 수 있게 됩니다. 다음 커맨드를 실행해봅시다. (이미지를 image라고 하는 것은 너무 평범하기 때문에, `picture` 라고 하겠습니다.)

`$ rails generate uploader Picture`

 CarrierWave로 업로드된 이미지는, Active Record 모델의 속성과 관계를 맺어야할 것 입니다. 관계맺어진 속성에는 이미지의 파일명이 저장되기 때문에 String형으로 해놓습니다. 확장된 micropost의 데이터 모델은 아래와 같습니다.

![](../image/Chapter13/micropost_model_picture.png)

필요한 `picture` 속성을 Micropost모델에 추가하기 위해, migration 파일을 생성하고, 개발환경의 데이터베이스에 적용합니다.

```
$ rails generate migration add_picture_to_microposts picture:string
$ rails db:migrate
```

CarrierWave에 이미지와 관계지어놓은 모델을 입력하기 위해선, `mount_uploader` 라고 하는 메소드를 사용합니다. 이 메소드는 파라미터에 속성명의 심볼과 생성되어진 업로더의 클래스이름을 취합니다.

`mount_uploader :picture, PictureUploader`

(`picture_uploader.rb` 라고 하는 파일에 `PictureUploader` 클래스가 정의되어 있습니다. 13.4.2에서 수정합니다만, 지금은 디폴트인 상태로도 상관없습니다. ) Micropost 모델에 업로더를 추가한 결과는 아래와 같습니다.

```ruby
# app/models/micropost.rb
# micropost모델에 이미지를 추가한다.
class Micropost < ApplicationRecord
  belongs_to :user
  default_scope -> { order(created_at: :desc) }
  mount_uploader :picture, PictureUploader #new
  validates :user_id, presence: true
  validates :content, presence: true, length: { maximum: 140 }
end
```

시스템에 따라서는 여기서 일단 Rails 서버를 재기동할 필요가 있습니다. 재기동되면 테스트코드를 실행시켜주세요. 일단 통과할 것 입니다. (단, [3.6.2](Chapter3.md#362-guard에-의한-테스트-자동화) 에서 설명한 Guard를 사용한 경우는, 재기동하는 것만으로는 제대로 동작하지 않을 수도 있습니다. 일단 그 경우라면 Terminal에서 일단 종료하고, 새로운  Terminal에서 Guard를 재실행해주세요.)



Home 페이지에 업로더를 추가하기 위해서는 micropost의 form에 `file_field` 태그를 포함할 필요가 있습니다.

```erb
<!-- app/views/shared/_micropost_form.html.erb -->
<%= form_for(@micropost) do |f| %>
  <%= render 'shared/error_messages', object: f.object %>
  <div class="field">
    <%= f.text_area :content, placeholder: "Compose new micropost..." %>
  </div>
  <%= f.submit "Post", class: "btn btn-primary" %>
  <span class="picture">
    <%= f.file_field :picture %> <!-- new -->
  </span>
<% end %>
```

마지막으로는 Web으로부터 추가할 수 있는 허가 리스트에 `picture` 속성을 추가해놓습니다. 추가해놓으면 `micropost_params` 메소드는 아래와 같이 됩니다.

```ruby
# app/controllers/microposts_controller.rb
class MicropostsController < ApplicationController
  before_action :logged_in_user, only: [:create, :destroy]
  before_action :correct_user,   only: :destroy
  .
  .
  .
  private

    def micropost_params
      params.require(:micropost).permit(:content, :picture) # new
    end

    def correct_user
      @micropost = current_user.microposts.find_by(id: params[:id])
      redirect_to root_url if @micropost.nil?
    end
end
```

한 번 이미지를 업로드해놓으면, Micropost partial의 `image_tag` 헬퍼로 해당 이미지를 표시할 수 있게 됩니다. 또한 이미지가 없는 (텍스트만 있는) micropost에서는 이미지를 표시하지않게 하기 위해, `picture?` 라고 하는 논리값을 리턴하는 메소드를 사용하는 점을 주목해주세요. 이 메소드는 이미지용의 속성명에 따라, CarrierWave가 자동적으로 생성해주는 메소드입니다. 수동으로 이미지가 포함된 micropost를 작성해보면 아래 캡쳐와 같이 됩니다. 업로드에 대한 테스트는 연습문제로 내보겠습니다.

```erb
<!-- app/views/microposts/_micropost.html.erb -->
<!-- micropost의 이미지표시를 추가한다. -->
<li id="micropost-<%= micropost.id %>">
  <%= link_to gravatar_for(micropost.user, size: 50), micropost.user %>
  <span class="user"><%= link_to micropost.user.name, micropost.user %></span>
  <span class="content">
    <%= micropost.content %>
    <%= image_tag micropost.picture.url if micropost.picture? %>
  </span>
  <span class="timestamp">
    Posted <%= time_ago_in_words(micropost.created_at) %> ago.
    <% if current_user?(micropost.user) %>
      <%= link_to "delete", micropost, method: :delete,
                                       data: { confirm: "You sure?" } %>
    <% end %>
  </span>
</li>
```

![](../image/Chapter13/microposts_with_image_4th_ed.png)

##### 연습

1. 이미지가 있는 micropost를 작성해봅시다. 혹시나 너무나도 큰 이미지를 첨부해버리진 않으셨나요? (걱정하지마세요. 이 문제는 다음 13.4.3에서 대응해봅니다.)
2. 아래 코드의 템플릿을 참고하여, [13.4](#134-micropost의-image-첨부) 에서 구현한 이미지 업로더를 테스트해보세요. 테스트의 준비로서, 먼저 sample 이미지를 fixture를 추가해보세요. (커맨드 : `cp app/assets/images/rails.png test/fixtures/`) 아래 코드에서 추가한 테스트에서는, Home 페이지에 있는 파일 업로드와, 작성에 성공했을 때 임지ㅣ가 표시되는지를 체크합니다. 또한 테스트 내부에 있는 `fixture_file_upload` 라고 하는 메소드는, fixture에서 정의된 파일을 업로드하는 특별한 메소드입니다. _Hint_ : `picture` 속성이 유효한지 아닌지를 확인하기 위해서는, [11.3.3](Chapter11.md#1133-유효화와-테스트의-Refactoring) 에서 소개한 `assigns` 메소드를 사용해주세요. 이 메소드를 사용하면 작성 성공한 후, `create` 액션 내의 micropost에 액세스하게 됩니다.

```ruby
# test/integration/microposts_interface_test.rb
# 영상업로드를 테스트하기 위한 템플릿
require 'test_helper'

class MicropostInterfaceTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:michael)
  end

  test "micropost interface" do
    log_in_as(@user)
    get root_path
    assert_select 'div.pagination'
    assert_select 'input[type=FILL_IN]'
    # 무효한 송신
    post microposts_path, params: { micropost: { content: "" } }
    assert_select 'div#error_explanation'
    # 유효한 송신
    content = "This micropost really ties the room together"
    picture = fixture_file_upload('test/fixtures/rails.png', 'image/png')
    assert_difference 'Micropost.count', 1 do
      post microposts_path, params: { micropost:
                                      { content: content,
                                        picture: FILL_IN } }
    end
    assert FILL_IN.picture?
    follow_redirect!
    assert_match content, response.body
    # 작성한 글을 삭제한다.
    assert_select 'a', 'delete'
    first_micropost = @user.microposts.paginate(page: 1).first
    assert_difference 'Micropost.count', -1 do
      delete micropost_path(first_micropost)
    end
    # 다른 유저의 프로필에 액세스해본다.
    get user_path(users(:archer))
    assert_select 'a', { text: 'delete', count: 0 }
  end
  .
  .
  .
end
```

### 13.4.2 Image의 검증

[13.4.1](#1341-기본적인-image-upload) 의 업로더도 나쁘지는 않습니다만, 몇가지 눈에 띄는 결점이 있습니다. 예를 들어, 업로드된 임지ㅣ에 대한 제한이 없기 때문에, 만약 유저가 엄청 큰 파일을 업로드한다거나 무효한 파일을 업로드하면 문제가 발생하게 됩니다. 이런 결점을 보완하기 위해 이미지 용량이나 포맷에 대한 validation을 구현하고 서버용과 클라이언트 (브라우저) 용의 양쪽에 추가해봅시다.



제일 첫 validation에서는 유효한 이미지의 종류를 제한해봅시다. 이것은 CarrierWave의 업로더 안에 힌트가 존재합니다. 생성된 업로더의 안에 주석처리된 코드가 있습니다. 이 주석처리된 코드를 원래대로 되돌리는 것으로, 이미지의 파일명으로부터 유효한 확장자 (PNG/GIF/JPEG 등)을 검증할 수 있게 됩니다.

```ruby
# app/uploaders/picture_uploader.rb
# Image format validation
class PictureUploader < CarrierWave::Uploader::Base
  storage :file

  # Upload파일의 저장폴더는 덮어쓰기 가능
  # 아래는 기본 저장 폴더
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  # 업로드가 가능한 확장자의 리스트 
  def extension_whitelist
    %w(jpg jpeg gif png)
  end
end
```

2번째 valiadtion에서는 이미지의 용량을 제한합니다. 이것은 `Micropost` 모델에 추가해보겠습니다. 방금 전 validation과는 다르게, 파일 용량에 대한 validation은  Rails의 기존 옵션 (`presence`나 `length` 등) 에는 없습니다. 따라서 이번에는 수동으로 `picture_size` 라고하는 독자적인 validation을 정의해봅시다. 결과는 아래와 같습니다. 독자적인 validation을 사용하기 위해 지금까지 사용하고 있던 `validates` 메소드가 아닌 `validate` 메소드를 사용하고 있는 점을 주목해주세요.

```ruby
# app/models/micropost.rb
# Add imageSize Validation
class Micropost < ApplicationRecord
  belongs_to :user
  default_scope -> { order(created_at: :desc) }
  mount_uploader :picture, PictureUploader
  validates :user_id, presence: true
  validates :content, presence: true, length: { maximum: 140 }
  validate  :picture_size #new

  private

    # 업로드된 이미지 용량을 validation한다. 
    def picture_size
      if picture.size > 5.megabytes
        errors.add(:picture, "should be less than 5MB")
      end
    end
```

이 `validate` 메소드는 파라미터로 심볼 (`:picture_size`) 를 전달받는다. 이 심볼명에 대응하는 메소드를 호출합니다. 또한 호출된 `picture_size` 메소드에서는 5MB를 상한으로 하여 (문법은 [컬럼 9.1](Chapter9.md#컬럼91-cookies는-지금으로부터-20년후에-만료된다-20.years.from_now) 을 참조) 해당 용량을 넘은 경우에는 커스터마이즈한 에러메세지를 `errors` 컬렉션에 추가합니다. (`errors` 에 대해서는 [6.2.2](Chapter6.md#622-존재성을-검증해보자) 에서 설명했습니다.)



위 두 개의 코드에서 정의한 이미지의 validation을 view에서 구현하기 위해, 클라이언트 측에 2개의 처리를 추가해봅시다. 일단은 format의 validation을 반영하기 위해서는 `file_field` 태그에 `accept` 파라미터를 부여하여 사용합니다.

`<%= f.file_field :picture, accept: 'image/jpeg,image/gif,image/png' %>`

이 때 accept파라미터에서는 위 첫 번째 코드에서 허가한 파일 형식을, [MIME 타입](https://ja.wikipedia.org/wiki/Multipurpose_Internet_Mail_Extensions#Content-Type) 으로 지정할 수 있습니다.



다음으로는 매우 큰 파일 사이즈에 대해 경고를 출력하기 위해, 간단한 JavaScript (정확히는 jQuery) 를 추가해봅니다. 이렇게하는 것으로 너무 긴 업로드 시간을 막을 수 있으며 서버의 부담을 줄일 수 있습니다.

```javascript
$('#micropost_picture').bind('change', function() {
  var size_in_megabytes = this.files[0].size/1024/1024;
  if (size_in_megabytes > 5) {
    alert('Maximum file size is 5MB. Please choose a smaller file.');
  }
});
```

jQuery는 본 튜토리얼의 주제가 아니기 때문에 상세한 설명은 하지 않습니다만, 위 코드에서는 (해시마크 `#` 으로부터 알 수 있듯) CSS id `micropost_picture` 를 포함한 요소를 찾아내어, 해당 요소를 감시합니다. 그리고 이 id를 가진 요소는 micropost form을 지칭합니다. (또한 브라우저 상에서는 화면을 오른쪽 클릭하여 inspector로 요소를 확인할 수 있습니다.) 즉, 이 CSS id를 가진 요소가 변화하였을때, 이 jQuery 함수가 동작합니다. 그리고 만약 파일 사이즈가 너무 큰 경우에는 `alert` 메소드로 경고를 내보냅니다.



이러한 추가적인 체크기능을 정리하자면 아래와 같이 됩니다.

```erb
<!-- app/views/shared/_micropost_form.html.erb -->

<%= form_for(@micropost) do |f| %>
  <%= render 'shared/error_messages', object: f.object %>
  <div class="field">
    <%= f.text_area :content, placeholder: "Compose new micropost..." %>
  </div>
  <%= f.submit "Post", class: "btn btn-primary" %>
  <span class="picture">
    <%= f.file_field :picture, accept: 'image/jpeg,image/gif,image/png' %>
  </span>
<% end %>

<script type="text/javascript">
  $('#micropost_picture').bind('change', function() {
    var size_in_megabytes = this.files[0].size/1024/1024;
    if (size_in_megabytes > 5) {
      alert('Maximum file size is 5MB. Please choose a smaller file.');
    }
  });
</script>
```

이미 눈치챈 독자분들도 있을 수도 있겠습니다만, 위와 같은 코드에서는 큰 파일의 업로드를 완전하게 막을 수 없습니다. 예를 들어, 유저는 alert를 무시하고 업로드를 강행할 수도 있을 것입니다. 만약 본 튜토리얼이 jQuery 튜토리얼이었다면 이 문제점과 해결책을 상냥하게 설명했을 것입니다만, 본 튜토리얼은 Rails 튜토리얼이기 때문에 이번에는 "위와 같은 코드으로는 아직 구현이 불완전하다" 라는 점을 알아놓는다면 충분합니다. 또한 만약 송신 form을 사용하여 작성을 제한할 수 있다하여도, 브라우저의 inspector 기능으로 JavaScript를 뜯어고친다거나 `curl` 을 사용하여 직접 POST 리퀘스트를 송신하는 경우에는 대응할 수 없습니다. 이러한 경우에도 대응할 수 있도록 하기 위해, 서버측 validation은 중요한 것 입니다.

##### 연습

1. 5MB이상의 이미지 파일을 등록하고자 하는 경우에는 어떻게 됩니까?
2. 무효한 확장자의 파일을 등록하고자하는 경우에는 어떻게 됩니까?

