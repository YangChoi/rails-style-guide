# 시작하며

> 롤 모델이 중요하다. <br/>
> -- Officer Alex J. Murphy / RoboCop

이 가이드는 루비 온 레일스 4 개발을 위한 베스트 프렉티스와 코딩 스타일
규칙을 전달하고자 작성되었으며,
커뮤니티 기반 [루비 코딩 스타일 가이드](https://github.com/bbatsov/ruby-style-guide)를
보완하는 가이드입니다.

일부 예제는 레일스 4.0 이상의 버전에서만 적용됩니다.

[Pandoc](http://pandoc.org/)를 사용해서 이 가이드를 PDF나 HTML형식으로 변환할
수 있습니다.

이 가이드는 다음 언어들로 번역되어있습니다.

* [Chinese Simplified](https://github.com/JuanitoFatas/rails-style-guide/blob/master/README-zhCN.md)
* [Chinese Traditional](https://github.com/JuanitoFatas/rails-style-guide/blob/master/README-zhTW.md)
* [German](https://github.com/arbox/de-rails-style-guide/blob/master/README-deDE.md)
* [Japanese](https://github.com/satour/rails-style-guide/blob/master/README-jaJA.md)
* [Russian](https://github.com/arbox/rails-style-guide/blob/master/README-ruRU.md)
* [Turkish](https://github.com/tolgaavci/rails-style-guide/blob/master/README-trTR.md)
* [Korean](https://github.com/pureugong/rails-style-guide/blob/master/README-koKR.md)
* [Vietnamese](https://github.com/CQBinh/rails-style-guide/blob/master/README-viVN.md)

# 레일스 스타일 가이드

이 가이드는 레일스 개발자가 코드를 작성할 때, 다른 레일스 개발자들도 유지보수
가능하도록, 베스트 프렉티스를 적용할 것을 권장합니다. 실제 사용방법을 반영하는
가이드와 너무 이상적으로 만들어져 사람들이 생각하기에 적용이 어렵다고 느껴지는
가이드가 있다면, 내용이 아무리 좋다고 하더라도 적용하지 않는 것이 좋습니다.

이 가이드는 서로 관련있는 규칙들을 섹션들로 나누었고, (자명한 것을 제외하고는)
규칙을 적용해야 하는 근거를 덧붙이려 노력하였습니다.

이 모든 규칙들이 하루 아침에 만들어지지는 않았습니다. 대부분의 규칙은 저의
소프트웨어 엔지니어로서의 수많은 경험과 레일스 커뮤니티의 피드백과 제안을
비롯해 높은 평가를 받고 있는 레일스 프로그래밍 리소스를 기반으로 만들어졌습니다.

## 목차

* [설정(configuration)](#configuration)
* [라우팅(Routing)](#routing)
* [컨트롤러(Controllers)](#controllers)
  * [랜더링(Rendering)](#rendering)
* [모델(Models)](#models)
  * [엑티브 레코드(ActiveRecord)](#activerecord)
  * [엑티브 레코드 쿼리(ActiveRecord Queries)](#activerecord-queries)
* [마이그레이션(Migrations)](#migrations)
* [뷰(Views)](#views)
* [국제화(Internationalization)](#internationalization)
* [에셋(Assets)](#assets)
* [메일러(Mailers)](#mailers)
* [액티브 서포트 코어 확장(Active Support Core Extensions)](#active-support-core-extensions)
* [시간(Time)](#time)
* [번들러(Bundler)](#bundler)
* [프로세스 관리(Managing processes)](#managing-processes)

## 설정(Configuration)
<a name="configuration"></a>

* <a name="config-initializers"></a>
  사용자 정의 초기 설정은 `config/initializers`에 둔다. 여기에 있는 코드들은
  애플리케이션이 처음 구동될 때 실행된다.
<sup>[[link](#config-initializers)]</sup>

* <a name="gem-initializers"></a>
  각각의 젬(gem)에 대해 젬과 같은 이름으로 초기 설정 파일을 따로 만든다.
  예를 들어 CarrierWave에 대한 설정은 `carrierwave.rb`에 저장하고, Active
  Admin에 대한 설정은 `active_admin.rb`에 저장한다.
<sup>[[link](#gem-initializers)]</sup>

* <a name="dev-test-prod-configs"></a>
  개발(development), 테스트(test) 그리고 배포(production) 환경에 대한 설정들은
  `config/environments/`아래에 각 환경의 이름으로 구분하여 저장한다.
<sup>[[link](#dev-test-prod-configs)]</sup>

  * 사전 컴파일해야하는 파일은 추가적인 에셋으로 표시한다.

    ```Ruby
    # config/environments/production.rb
    # Precompile additional assets (application.js, application.css,
    # all non-JS/CSS are already added)
    config.assets.precompile += %w( rails_admin/rails_admin.css rails_admin/rails_admin.js )
    ```

* <a name="app-config"></a>
  모든 환경에 적용되어야 하는 설정은 `config/application.rb` 파일에 둔다.
<sup>[[link](#app-config)]</sup>

* <a name="staging-like-prod"></a>
  실제 `production` 환경과 아주 유사한 `staging` 환경을 추가로 만든다.
<sup>[[link](#staging-like-prod)]</sup>

* <a name="yaml-config"></a>
  그 외의 설정들은 `config/` 디렉토리 아래의 YAML파일을 만들어 저장한다.
<sup>[[link](#yaml-config)]</sup>

  레일스 4.2부터 추가된 `config_for` 메소드를 통해 YAML 설정 파일들은 쉽게 읽어들일 수 있다.

  ```Ruby
  Rails::Application.config_for(:yaml_file)
  ```

## 라우팅(Routing)
<a name="routing"></a>

* <a name="member-collection-routes"></a>
  RESTful 리소스에 더 많은 액션을 추가할 필요가 있다면 (정말로 그게 다
  필요한가?) `member` 와 `collection` 라우트를 사용한다.
<sup>[[link](#member-collection-routes)]</sup>

  ```Ruby
  # 나쁜 예
  get 'subscriptions/:id/unsubscribe'
  resources :subscriptions

  # 좋은 예
  resources :subscriptions do
    get 'unsubscribe', on: :member
  end

  # 나쁜 예
  get 'photos/search'
  resources :photos

  # 좋은 예
  resources :photos do
    get 'search', on: :collection
  end
  ```

* <a name="many-member-collection-routes"></a>
  여러 `member/collection` 라우트를 정의해야 한다면 블록 구문을 사용한다.
<sup>[[link](#many-member-collection-routes)]</sup>

  ```Ruby
  resources :subscriptions do
    member do
      get 'unsubscribe'
      # more routes
    end
  end

  resources :photos do
    collection do
      get 'search'
      # more routes
    end
  end
  ```

* <a name="nested-routes"></a>
  엑티브 레코드(ActiveRecord) 모델 간의 관계를 더 분명하게 표현하기 위해서 중첩
  라우트를 사용한다.
<sup>[[link](#nested-routes)]</sup>

  ```Ruby
  class Post < ActiveRecord::Base
    has_many :comments
  end

  class Comments < ActiveRecord::Base
    belongs_to :post
  end

  # routes.rb
  resources :posts do
    resources :comments
  end
  ```

* <a name="namespaced-routes"></a>
  1 단계 이상의 중첩 라우트가 필요할 때 `shallow: true` 옵션을 사용한다. 이는
  사용자를 `posts/1/comments/5/versions/7/edit`같은 긴 url에서 구하고
  `edit_post_comment_version`같은 긴 url 핼퍼를 사용하지 않아도 되게 한다.

  ```Ruby
  resources :posts, shallow: true do
    resources :comments do
      resources :versions
    end
  end
  ```

* <a name="namespaced-routes"></a>
  관련된 액션을 묶기위해 `namespace`를 사용한다.
<sup>[[link](#namespaced-routes)]</sup>

  ```Ruby
  namespace :admin do
    # Directs /admin/products/* to Admin::ProductsController
    # (app/controllers/admin/products_controller.rb)
    resources :products
  end
  ```

* <a name="no-wild-routes"></a>
  예전 레일스에서 사용하던 전역 라우팅 설정(legacy wild controller route)은
  절대로 사용하지 않는다. 이 라우트는 GET 요청으로 모든 컨트롤러의 모든
  액션(actions)에 접근할 수 있게 만든다.
<sup>[[link](#no-wild-routes)]</sup>

  ```Ruby
  # 아주 나쁜 예
  match ':controller(/:action(/:id(.:format)))'
  ```

* <a name="no-match-routes"></a>
  `match` 메소드를 통한 라우트는 `[:get, :post, :patch, :put, :delete]` 중 두 종류 이상의 요청을 액션에 맵핑할 필요가 있는 경우에만 `via` 옵션과 함께 사용하고, 그 외에는 사용하지 않는다.
<sup>[[link](#no-match-routes)]</sup>

## 컨트롤러(Controllers)
<a name="controllers"></a>

* <a name="skinny-controllers"></a>
  컨트롤러는 최대한 간결하게 유지한다. 컨트롤러는 단지 뷰 레이어를 위한 데이터를 전달하는 역할을 하고 어떠한 비즈니스 로직도 포함해서는 안 된다(모든 비즈니스 로직은 마땅히 모델 안에서 구현되어야 한다).
<sup>[[link](#skinny-controllers)]</sup>

* <a name="one-method"></a>
  각 컨트롤러의 액션은 (원칙적으로는) 초기 검색과 생성을 제외하고 단 하나의 메소드만을 호출해야한다.
<sup>[[link](#one-method)]</sup>

* <a name="shared-instance-variables"></a>
  하나의 컨트롤러와 하나의 뷰 사이에서 두 개 이상의 인스턴스 변수들을 공유하지 않는다.
<sup>[[link](#shared-instance-variables)]</sup>

### 랜더링(Rendering)
<a name="rendering"></a>

* <a name="inline-rendering"></a>
  인라인(inline) 랜더링보다는 템플릿을 사용한다.
<sup>[[link](#inline-rendering)]</sup>

```Ruby
# 매우 나쁜 예
class ProductsController < ApplicationController
  def index
    render inline: "<% products.each do |p| %><p><%= p.name %></p><% end %>", type: :erb
  end
end

# 좋은 예
## app/views/products/index.html.erb
<%= render partial: 'product', collection: products %>

## app/views/products/_product.html.erb
<p><%= product.name %></p>
<p><%= product.price %></p>

## app/controllers/foo_controller.rb
class ProductsController < ApplicationController
  def index
    render :index
  end
end
```

* <a name="plain-text-rendering"></a>
  `render text:`대신 `render plain:`을 사용한다.
<sup>[[link](#plain-text-rendering)]</sup>

```Ruby
# 나쁜 예 - MIME 타입을 `text/html`로 설정
...
render text: 'Ruby!'
...

# 나쁜 예 - 정확한 MIME 타입을 선언해야한다.
...
render text: 'Ruby!', content_type: 'text/plain'
...

# 좋은 예 - 간결하고 정확함
...
render plain: 'Ruby!'
...
```

* <a name="http-status-code-symbols"></a>
  숫자로 이루어진 HTTP상태 코드보단 [corresponding symbols](https://gist.github.com/mlanett/a31c340b132ddefa9cca)을 사용하면, 잘 알려지지 않은 HTTP 상태코드가 "magic" 숫자처럼 보이지 않고 그 의미를 명확하게 알 수 있다.
<sup>[[link](#http-status-code-symbols)]</sup>

```Ruby
# 나쁜 예
...
render status: 500
...

# 좋은 예
...
render status: :forbidden
...
```

## 모델(Models)
<a name="models"></a>

* <a name="model-classes"></a>
  엑티브 레코드를 사용하지 않는 모델은 자유롭게 사용한다.
<sup>[[link](#model-classes)]</sup>

* <a name="meaningful-model-names"></a>
  모델 이름에는 축약어를 쓰지않고 의미를 가진 짧은 이름을 사용한다.
<sup>[[link](#meaningful-model-names)]</sup>

* <a name="activeattr-gem"></a>
  엑티브 레코드의 데이터베이스 기능을 제외한 validation과 같은 기능만 필요한 모델 오브젝트가 필요하다면 [ActiveAttr](https://github.com/cgriego/active_attr) 젬을 사용한다.
<sup>[[link](#activeattr-gem)]</sup>

  ```Ruby
  class Message
    include ActiveAttr::Model

    attribute :name
    attribute :email
    attribute :content
    attribute :priority

    attr_accessible :name, :email, :content

    validates :name, presence: true
    validates :email, format: { with: /\A[-a-z0-9_+\.]+\@([-a-z0-9]+\.)+[a-z0-9]{2,4}\z/i }
    validates :content, length: { maximum: 500 }
  end
  ```

  더 많은 예제는 다음 링크를 참조.
  [RailsCast on the subject](http://railscasts.com/episodes/326-activeattr).

* <a name="model-business-logic"></a>
  비즈니스 영역(business domain)에서 어떤 의미를 가지고 있지 않는 한, HTML을
  생성하는 코드와 같이 데이터가 화면에서 어떻게 보여질지를 결정하는 메소드를
  모델에서 사용하지 않는다. 그러한 메소드들은 대부분 뷰 층에서만 사용되기
  때문에, 헬퍼(helpers)로 구현되어야 한다. 모델은 비즈니스 로직과
  데이터-영속성(persistance)과 관련이 있을 경우에만 사용하도록 하자.
<sup>[[link](#model-business-logic)]</sup>


### 엑티브 레코드(ActiveRecord)
<a name="activerecord"></a>

* <a name="keep-ar-defaults"></a>
  데이터베이스 소유권을 가지고 있지 않은 경우와 같이 특별한 이유가 있는 게
  아니라면 엑티브 레코드의 기본 설정(테이블 이름, 기본키 등)을 가능하면
  변경하지 않는다.
<sup>[[link](#keep-ar-defaults)]</sup>

  ```Ruby
  # 나쁜 예 - 데이터베이스 스키마 변경 권한이 있다면 이렇게 하지 말 것!
  class Transaction < ActiveRecord::Base
    self.table_name = 'order'
    ...
  end
  ```

* <a name="macro-style-methods"></a>
  매크로 성격의 메소드(`has_many`, `validates` 등)들은 클래스 상단에 모아둔다.
<sup>[[link](#macro-style-methods)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    # keep the default scope first (if any)
    default_scope { where(active: true) }

    # constants come up next
    COLORS = %w(red green blue)

    # afterwards we put attr related macros
    attr_accessor :formatted_date_of_birth

    attr_accessible :login, :first_name, :last_name, :email, :password

    # followed by association macros
    belongs_to :country

    has_many :authentications, dependent: :destroy

    # and validation macros
    validates :email, presence: true
    validates :username, presence: true
    validates :username, uniqueness: { case_sensitive: false }
    validates :username, format: { with: /\A[A-Za-z][A-Za-z0-9._-]{2,19}\z/ }
    validates :password, format: { with: /\A\S{8,128}\z/, allow_nil: true }

    # next we have callbacks
    before_save :cook
    before_save :update_username_lower

    # other macros (like devise's) should be placed after the callbacks

    ...
  end
  ```

* <a name="has-many-through"></a>
  `has_and_belongs_to_many`보다 `has_many :through`를 사용한다.
  `has_many :through` 사용하면 중간 모델(join model)에서 추가적인 속성이나
  validation을 사용할 수 있다.
<sup>[[link](#has-many-through)]</sup>

  ```Ruby
  # 좋지 않은 예 - has_and_belongs_to_many를 사용한 예
  class User < ActiveRecord::Base
    has_and_belongs_to_many :groups
  end

  class Group < ActiveRecord::Base
    has_and_belongs_to_many :users
  end

  # 선호되는 방식 - has_many :through를 사용한 예
  class User < ActiveRecord::Base
    has_many :memberships
    has_many :groups, through: :memberships
  end

  class Membership < ActiveRecord::Base
    belongs_to :user
    belongs_to :group
  end

  class Group < ActiveRecord::Base
    has_many :memberships
    has_many :users, through: :memberships
  end
  ```

* <a name="read-attribute"></a>
  `read_attribute(:attribute)`보다 `self[:attribute]`를 사용한다.
<sup>[[link](#read-attribute)]</sup>

  ```Ruby
  # 나쁜 예
  def amount
    read_attribute(:amount) * 100
  end

  # 좋은 예
  def amount
    self[:amount] * 100
  end
  ```

* <a name="write-attribute"></a>
  `write_attribute(:attribute, value)`보다 `self[:attribute] = value`를 사용한다.
<sup>[[link](#write-attribute)]</sup>

  ```Ruby
  # 나쁜 예
  def amount
    write_attribute(:amount, 100)
  end

  # 좋은 예
  def amount
    self[:amount] = 100
  end
  ```

* <a name="sexy-validations"></a>
  항상 새로운 ["섹시한" validations](http://thelucid.com/2010/01/08/sexy-validation-in-edge-rails-rails-3/)을 사용한다.
<sup>[[link](#sexy-validations)]</sup>

  ```Ruby
  # 나쁜 예
  validates_presence_of :email
  validates_length_of :email, maximum: 100

  # 좋은 예
  validates :email, presence: true, length: { maximum: 100 }
  ```

* <a name="custom-validator-file"></a>
  사용자 정의 검증(validation)을 한 번 이상 사용하거나 정규표현식을 사용한다면,
  사용자 정의 검증을 담은 파일을 작성한다.
<sup>[[link](#custom-validator-file)]</sup>

  ```Ruby
  # 나쁜 예
  class Person
    validates :email, format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i }
  end

  # 좋은 예
  class EmailValidator < ActiveModel::EachValidator
    def validate_each(record, attribute, value)
      record.errors[attribute] << (options[:message] || 'is not a valid email') unless value =~ /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i
    end
  end

  class Person
    validates :email, email: true
  end
  ```
* <a name="app-validators"></a>
  사용자 정의 validator는 `app/validators`아래에 둔다.
<sup>[[link](#app-validators)]</sup>

* <a name="custom-validators-gem"></a>
  여러 애플리케이션에서 사용되거나, 범용적인 사용자 정의 validator를 공유 젬으로
  만드는 것을 고려한다.
<sup>[[link](#custom-validators-gem)]</sup>

* <a name="named-scopes"></a>
  이름 있는 스코프(named scope)는 자유롭게 사용한다.
<sup>[[link](#named-scopes)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    scope :active, -> { where(active: true) }
    scope :inactive, -> { where(active: false) }

    scope :with_orders, -> { joins(:orders).select('distinct(users.id)') }
  end
  ```

* <a name="named-scope-class"></a>
  매개변수가 있는 람다 함수로 만들어진 이름 있는 스코프가 너무 복잡해질 때는
  이름 있는 스코프처럼 `ActiveRecord::Relation`을 반환하는 클래스 메소드를
  정의하는 것을 고려해볼 만 하다. 분명 아래와 같이 더 단순하게 스코프를 정의할
  수 있을 것이다.
<sup>[[link](#named-scope-class)]</sup>

  ```Ruby
  class User < ActiveRecord::Base
    def self.with_orders
      joins(:orders).select('distinct(users.id)')
    end
  end
  ```

* <a name="beware-update-attribute"></a>
  [`update_attribute`](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-update_attribute) 메소드의 동작에 조심해야 한다.
  (`update_attributes`와는 달리) 모델 validation를 실행하지 않기 때문에 모델의 상태가 손상되기 쉽다.
<sup>[[link](#beware-update-attribute)]</sup>

* <a name="user-friendly-urls"></a>
  사용자 친화적인 URL을 사용한다. URL에 `id`보다 모델의 특징을 잘 나타내는 속성을 사용한다.
  이를 위한 여러가지 방법들이 있다.
<sup>[[link](#user-friendly-urls)]</sup>

  * 모델의 `to_param` 메소드를 오버라이드한다. 이 메소드는 레일스에서 대상
    객체에 대한 URL을 생성하기 위해 사용된다. 기본적으로 레코드의 `id`를
    String 객체로 반환한다. 다른 사람이 읽기 좋은 속성을 포함하도록 오버라이드할
    수 있다.

      ```Ruby
      class Person
        def to_param
          "#{id} #{name}".parameterize
        end
      end
      ```
  이 값을 URL에서 사용하려면 문자열에 `parameterize`를 호출해야한다.
  객체의 id가 앞부분에 있어야만 엑티브 레코드의 `find` 메소드로 찾을 수 있다.

  * `friendly_id` 젬을 사용한다. 이를 사용하면 `id` 대신에 모델의 특징을 잘
    반영한 속성들을 사용해 사람이 읽기 쉬운 URL을 만들 수 있다.

      ```Ruby
      class Person
        extend FriendlyId
        friendly_id :name, use: :slugged
      end
      ```

  사용법에 대한 더 많은 정보는 [문서](https://github.com/norman/friendly_id)를
  참고하기 바란다.

* <a name="find-each"></a>
  엑티브 레코드 객체의 컬렉션을 반복할 때는 `find_each`를 사용한다.
  (예를 들면 `all` 메소드를 사용해) 데이터베이스에서 가져온 레코드 컬렉션에
  대해서 반복 작업을 수행하는 일은 모든 객체의 객체화를 한 번에 하기 때문에 매우
  비효율적이다. 이 때는 배치 작업(batch process) 메소드를 통해 레코드들이
  배치에서 처리되도록 하면 메모리 소비를 크게 줄일 수 있다.
<sup>[[link](#find-each)]</sup>

  ```Ruby
  # 나쁜 예
  Person.all.each do |person|
    person.do_awesome_stuff
  end

  Person.where('age > 21').each do |person|
    person.party_all_night!
  end

  # 좋은 예
  Person.find_each do |person|
    person.do_awesome_stuff
  end

  Person.where('age > 21').find_each do |person|
    person.party_all_night!
  end
  ```

* <a name="before_destroy"></a>
  [레일스는 모델 의존 관계에 대한 콜백을
  생성하기](https://github.com/rails/rails/issues/3458) 때문에, 항상
  validation을 수행하는 `before_destroy` 콜백은 `prepend: true`와 함께 호출한다.
<sup>[[link](#before_destroy)]</sup>

  ```Ruby
  # 나쁜 예 - roles는 super_admin?이 true라도 자동적으로 삭제된다.
  has_many :roles, dependent: :destroy

  before_destroy :ensure_deletable

  def ensure_deletable
    fail "Cannot delete super admin." if super_admin?
  end

  # 좋은 예
  has_many :roles, dependent: :destroy

  before_destroy :ensure_deletable, prepend: true

  def ensure_deletable
    fail "Cannot delete super admin." if super_admin?
  end
  ```
* <a name="has_many-has_one-dependent-option"></a>
  `has_many`와 `has_one` 관계에 대해서는 `dependent` 설정을 하자.
<sup>[[link](#has_many-has_one-dependent-option)]</sup>

  ```Ruby
  # 나쁜 예
  class Post < ActiveRecord::Base
    has_many :comments
  end

  # 좋은 예
  class Post < ActiveRecord::Base
    has_many :comments, dependent: :destroy
  end
  ```

* <a name="save-bang"></a>
  AR 객체를 남길 때에는 항상 예외가 발생하는 느낌표! 메소드를 사용하거나 반환
  값을 처리한다. 이는 `create`, `save`, `update`, `destroy`, `first_or_create`,
  `find_or_create_by`에 적용된다.
<sup>[[link](#save-bang)]</sup>

  ```Ruby
  # 나쁜 예
  user.create(name: 'Bruce')

  # 나쁜 예
  user.save

  # 좋은 예
  user.create!(name: 'Bruce')
  # 또는
  bruce = user.create(name: 'Bruce')
  if bruce.persisted?
    ...
  else
    ...
  end

  # 좋은 예
  user.save!
  # 또는
  if user.save
    ...
  else
    ...
  end
  ```

### 엑티브 레코드 쿼리(ActiveRecord Queries)
<a name="activerecord-queries"></a>

* <a name="avoid-interpolation"></a>
  SQL injection 공격에 취약할 수 있으므로, 쿼리에서 문자열 보간(string interpolation)을 사용하지 않는다.
<sup>[[link](#avoid-interpolation)]</sup>

  ```Ruby
  # 나쁜 예 - 어떠한 매개변수든지 들어갈 수 있음
  Client.where("orders_count = #{params[:orders]}")

  # 좋은 예 - 적절한 매개변수만 들어갈 수 있음
  Client.where('orders_count = ?', params[:orders])
  ```

* <a name="named-placeholder"></a>
  쿼리에 하나 이상의 플레이스홀더를 사용할 때는
  위치로 구분되는 플레이스홀더 대신 이름을 붙여 사용하는 것을 고려해본다.
<sup>[[link](#named-placeholder)]</sup>

  ```Ruby
  # 괜찮은 예
  Client.where(
    'created_at >= ? AND created_at <= ?',
    params[:start_date], params[:end_date]
  )

  # 좋은 예
  Client.where(
    'created_at >= :start_date AND created_at <= :end_date',
    start_date: params[:start_date], end_date: params[:end_date]
  )
  ```

* <a name="find"></a>
  id를 통해 하나의 값을 조회할 때는 `where` 보다 `find`를 사용한다.
<sup>[[link](#find)]</sup>

  ```Ruby
  # 나쁜 예
  User.where(id: id).take

  # 좋은 예
  User.find(id)
  ```

* <a name="find_by"></a>
  특정 속성을 통해 하나의 값을 조회할 때는 `where`과 `find_by_attribute`보단 `find_by`를 사용한다.
<sup>[[link](#find_by)]</sup>

  ```Ruby
  # 나쁜 예
  User.where(first_name: 'Bruce', last_name: 'Wayne').first

  # 나쁜 예
  User.find_by_first_name_and_last_name('Bruce', 'Wayne')

  # 좋은 예
  User.find_by(first_name: 'Bruce', last_name: 'Wayne')
  ```

* <a name="where-not"></a>
  SQL을 직접 사용하기보다 'where.not'을 사용한다.
<sup>[[link](#where-not)]</sup>

  ```Ruby
  # 나쁜 예
  User.where("id != ?", id)

  # 좋은 예
  User.where.not(id: id)
  ```

* <a name="squished-heredocs"></a>
  `find_by_sql`처럼 메소드에서 명시적인 쿼리를 작성할 때, `squish`와 함께
  히어독을 사용한다. 이렇게 하면, SQL을 줄바꿈과 들여쓰기로 읽기 쉬운 형식으로
  할 수 있을 뿐만 아니라, GitHub, Atom, RubyMine을 포함한 많은 툴에서 구문
  하일라이트를 해준다.
<sup>[[link](#squished-heredocs)]</sup>

  ```Ruby
  User.find_by_sql(<<-SQL.squish)
    SELECT
      users.id, accounts.plan
    FROM
      users
    INNER JOIN
      accounts
    ON
      accounts.user_id = users.id
    # further complexities...
  SQL
  ```

  [`String#squish`](http://apidock.com/rails/String/squish)는 들여쓰기와
  줄바꿈을 지워준다. 그래서 서버 로그에서 다음과 같이 보이지 않고 유려한
  SQL 문자열을 표시할 수 있다.

  ```
  SELECT\n    users.id, accounts.plan\n  FROM\n    users\n  INNER JOIN\n    acounts\n  ON\n    accounts.user_id = users.id
  ```

## 마이그레이션(Migrations)
<a name="migrations"></a>

* <a name="schema-version"></a>
  `schema.rb` (또는 `structure.sql`) 파일을 VCS(버전 관리 시스템)에 포함시킨다.
<sup>[[link](#schema-version)]</sup>

* <a name="db-schema-load"></a>
  빈 database를 초기화할 때는 `rake db:migrate` 대신 `rake db:schema:load`를 사용한다.
<sup>[[link](#db-schema-load)]</sup>

* <a name="default-migration-values"></a>
  기본 설정 값들은 애플리케이션에서 지정하기보다, 마이그레이션 자체에 포함시킨다.
<sup>[[link](#default-migration-values)]</sup>

  ```Ruby
  # 나쁜 예 - 애플리케이션에서 기본설정 값을 지정하는 예
  def amount
    self[:amount] or 0
  end
  ```

  테이블의 기본 설정 값을 레일스 애플리케이션에서만 지정하는 것은 많은 레일스
  개발자들이 사용하지만, 이는 데이터를 어플리케이션 버그에 취약하게 만드는 아주
  불안정한 접근방법이다. 그리고 대부분의 중요한 애플리케이션들은 하나의
  데이터베이스를 다른 애플리케이션과 공유하기 때문에, 레일스 애플리케이션을 통해
  데이터 무결성을 보장하는 것은 불가능하다는 사실을 고려해야한다.

* <a name="foreign-key-constraints"></a>식
  외래키 제약을 사용한다. 레일스 4.2부터 엑티브 레코드는 외래키 제약을
  기본적으로 지원한다.
  <sup>[[link](#foreign-key-constraints)]</sup>

* <a name="change-vs-up-down"></a>
  (테이블이나 컬럼을 추가하는) 구조적인 마이그레이션을 작성할 때는 `up`과 `down` 메소드 대신 `change` 메소드를 정의한다.
  <sup>[[link](#change-vs-up-down)]</sup>

  ```Ruby
  # 예전 방식
  class AddNameToPeople < ActiveRecord::Migration
    def up
      add_column :people, :name, :string
    end

    def down
      remove_column :people, :name
    end
  end

  # 새로운 방식
  class AddNameToPeople < ActiveRecord::Migration
    def change
      add_column :people, :name, :string
    end
  end
  ```

* <a name="no-model-class-migrations"></a>
  마이그레이션에서 모델 클래스를 사용하지 않는다. 모델 클래스들은 계속해서
  변하기 때문에, 마이그레이션에서 사용한 모델이 변화하게 되면 마이그레이션
  작업이 정상적으로 수행되지 않을 수 있다.
<sup>[[link](#no-model-class-migrations)]</sup>

* <a name="meaningful-foreign-key-naming"></a>
  레일즈의 자동생성된 외래 키 이름 대신 외래 키 이름을 명시적으로 지정한다.
  (http://edgeguides.rubyonrails.org/active_record_migrations.html#foreign-keys)

  ```Ruby
  # 나쁜 예
  class AddFkArticlesToAuthors < ActiveRecord::Migration
    def change
      add_foreign_key :articles, :authors
    end
  end

  # 좋은 예
  class AddFkArticlesToAuthors < ActiveRecord::Migration
    def change
      add_foreign_key :articles, :authors, name: :articles_author_id_fk
    end
  end
  ```

<sup>[[link](#meaningful-foreign-key-naming)]</sup>

## 뷰(Views)
<a name="views"></a>

* <a name="no-direct-model-view"></a>
  뷰에서 직접적으로 모델을 사용하지 않는다.
<sup>[[link](#no-direct-model-view)]</sup>

* <a name="no-complex-view-formatting"></a>
  뷰에서는 절대 복잡한 포맷팅을 만들지 말고, 이러한 포맷팅은 뷰 헬퍼 메소드나 모델로 분리한다.
<sup>[[link](#no-complex-view-formatting)]</sup>

* <a name="partials"></a>
  부분 템플릿(partial template)과 레이아웃을 이용하여 코드 중복을 줄인다.
<sup>[[link](#partials)]</sup>

## 국제화(Internationalization)
<a name="internationalization"></a>

* <a name="locale-texts"></a>
  뷰, 모델, 컨트롤러에서는 지역(locale) 관련 설정이나 문자열을 바로 사용하지
  않는다. 이러한 문자열들은 `config/locales` 디렉터리 아래의 로케일 파일로 옮겨
  관리한다.
<sup>[[link](#locale-texts)]</sup>

* <a name="translated-labels"></a>
  엑티브 레코드 모델의 레이블에 대한 번역이 필요할 때는 `activerecord` 스코프
  아래에 작성한다.
<sup>[[link](#translated-labels)]</sup>

  ```
  en:
    activerecord:
      models:
        user: Member
      attributes:
        user:
          name: 'Full name'
  ```

  이 때 `User.model_name.human`은 "Member"를 반환하고
  `User.human_attribute_name("name")`은 "Full name"을 반환한다.
  이러한 속성들에 대한 번역은 뷰에서 레이블로 사용된다.

* <a name="organize-locale-files"></a>
  뷰에서 사용되는 엑티브 레코드 속성들에 대한 번역은 분리한다.
  `locales/models` 디렉터리에 모델을 위한 로케일 파일들을 저장하고,
  뷰에서 사용하는 텍스트는 `locales/views`에 저장한다.
<sup>[[link](#organize-locale-files)]</sup>

  * 로케일(locale) 파일들을 적절한 위치에 저장하기 위해 디렉터리를 추가로
    만들었다면, 이 파일들을 읽어들일 수 있도록 `application.rb` 파일에 설정해야
    한다.

      ```ruby
      # config/application.rb
      config.i18n.load_path += dir[rails.root.join('config', 'locales', '**', '*.{rb,yml}')]
      ```

* <a name="shared-localization"></a>
  날짜나 통화 형식과 같은 공유해서 사용할 수 있는 지역화 옵션들은
  `locale` 바로 아래에 저장한다.
<sup>[[link](#shared-localization)]</sup>

* <a name="short-i18n"></a>
  i18n의 짧은 형식의 메소드를 사용한다.
  `i18n.translate` => `i18n.t`
  `i18n.localize` => `i18n.l`
<sup>[[link](#short-i18n)]</sup>

* <a name="lazy-lookup"></a>
  뷰에서 사용되는 텍스트에 대해 게으른 참조(lazy lookup)를 사용한다.
  게으른 참조란 번역 텍스트의 구조를 뷰 디렉터리 구조와 같게 하여,
  뷰에서 간단히 번역 텍스트를 참조하는 방법이다.
  예를 들어 다음과 같은 구조가 있다고 하자.
<sup>[[link](#lazy-lookup)]</sup>

  ```
  en:
    users:
      show:
        title: 'user details page'
  ```

  `users.show.title`의 값은 `app/views/users/show.html.haml` 템플릿에서 다음과 같이 사용될 수 있다.

  ```ruby
  = t '.title'
  ```

* <a name="dot-separated-keys"></a>
  컨트롤러와 모델에서 `:scope` 옵션을 사용하기보다, 점으로 분리된 키를 사용한다.
  읽기도 쉽고 계층 구조를 파악하기도 쉽다.
<sup>[[link](#dot-separated-keys)]</sup>

  ```Ruby
  # 나쁜 예
  I18n.t :record_invalid, scope: [:activerecord, :errors, :messages]

  # 좋은 예
  I18n.t 'activerecord.errors.messages.record_invalid'
  ```

* <a name="i18n-guides"></a>
  레일스 I18n과 관련된 더 자세한 정보는 [레일스 가이드(Rails
  Guides)](http://guides.rubyonrails.org/i18n.html)를 참고하라.
<sup>[[link](#i18n-guides)]</sup>

## 에셋 (Assets)
<a name="assets"></a>

[에셋 파이프라인](http://guides.rubyonrails.org/asset_pipeline.html)을 사용하라. 이는 애플리케이션 배포에 필요한 에셋 파일들을 조직해줄 것이다.

* <a name="reserve-app-assets"></a>
  사용자 정의 스타일시트, 자바스크립트, 이미지는 `app/assets` 디렉터리 아래에 저장한다.
<sup>[[link](#reserve-app-assets)]</sup>

* <a name="lib-assets"></a>
  애플리케이션에 포함되지 않는 직접 작성한 라이브러리들은 `lib/assets`에 저장한다.
<sup>[[link](#lib-assets)]</sup>

* <a name="vendor-assets"></a>
  [jQuery](http://jquery.com/)나
  [bootstrap](http://twitter.github.com/bootstrap/) 같은
  서드파티 라이브러리는 `vendor/assets`에 둔다.
<sup>[[link](#vendor-assets)]</sup>

* <a name="gem-assets"></a>
  가능하다면 젬으로 만들어진 에셋을 사용한다(예를 들어,
  [jquery-rails](https://github.com/rails/jquery-rails),
  [jquery-ui-rails](https://github.com/joliss/jquery-ui-rails),
  [bootstrap-sass](https://github.com/thomas-mcdonald/bootstrap-sass),
  [zurb-foundation](https://github.com/zurb/foundation)).
<sup>[[link](#gem-assets)]</sup>

## 메일러(Mailers)
<a name="mailers"></a>

* <a name="mailer-name"></a>
  메일러의 이름은 `SomethingMailer` 형식을 따른다.
  이러한 접미사가 없다면 메일러 클래스인지 바로 파악하기가 어렵고, 어떠한 뷰에 연결되어 있는지 찾아내기 어렵다.
<sup>[[link](#mailer-name)]</sup>

* <a name="html-plain-email"></a>
  HTML과 텍스트(plain text) 기반의 두 가지 템플릿을 각각 준비한다.
<sup>[[link](#html-plain-email)]</sup>

* <a name="enable-delivery-errors"></a>
  개발 환경에서 메일 전송에 실패하면 에러가 발생하도록 설정한다.
  기본 설정 값은 에러가 발생하지 않도록 설정되어 있다.
<sup>[[link](#enable-delivery-errors)]</sup>

  ```Ruby
  # config/environments/development.rb

  config.action_mailer.raise_delivery_errors = true
  ```

* <a name="local-smtp"></a>
  개발 환경에서는 [Mailcatcher](https://github.com/sj26/mailcatcher)와 같은 로컬 SMTP 서버를 사용한다.
<sup>[[link](#local-smtp)]</sup>

  ```Ruby
  # config/environments/development.rb

  config.action_mailer.smtp_settings = {
    address: 'localhost',
    port: 1025,
    # more settings
  }
  ```

* <a name="default-hostname"></a>
  호스트의 이름을 기본 설정 값을 지정한다.
<sup>[[link](#default-hostname)]</sup>

  ```Ruby
  # config/environments/development.rb
  config.action_mailer.default_url_options = { host: "#{local_ip}:3000" }

  # config/environments/production.rb
  config.action_mailer.default_url_options = { host: 'your_site.com' }

  # 메일러 클래스 안에서 설정
  default_url_options[:host] = 'your_site.com'
  ```

* <a name="url-not-path-in-email"></a>
  이메일에 사이트의 링크를 넣고 싶을 때엔 항상 `_path` 대신 `_url` 메소드를 사용한다.
  `_url` 메소드는 호스트 이름을 같이 반환하고,  `_path` 메소드는 그렇지 않다.
<sup>[[link](#url-not-path-in-email)]</sup>

  ```Ruby
  # 나쁜 예
  You can always find more info about this course
  <%= link_to 'here', course_path(@course) %>

  # 좋은 예
  You can always find more info about this course
  <%= link_to 'here', course_url(@course) %>
  ```

* <a name="email-addresses"></a>
  보내는 사람(from)과 받는 사람(to)의 이메일 형식을 적절하게 지정한다. 다음 형식을 따른다.
<sup>[[link](#email-addresses)]</sup>

  ```Ruby
  # 메일러 클래스 안에서 설정한다
  default from: 'Your Name <info@your_site.com>'
  ```

* <a name="delivery-method-test"></a>
  테스트 환경에서는 이메일 전송 메소드를 `test`로 설정한다.
<sup>[[link](#delivery-method-test)]</sup>

  ```Ruby
  # config/environments/test.rb

  config.action_mailer.delivery_method = :test
  ```

* <a name="delivery-method-smtp"></a>
  개발 및 배포 환경에서는 이메일 전송 메소드가 `smtp`로 설정되어 있어야 한다.
<sup>[[link](#delivery-method-smtp)]</sup>

  ```Ruby
  # config/environments/development.rb, config/environments/production.rb

  config.action_mailer.delivery_method = :smtp
  ```

* <a name="inline-email-styles"></a>
  html 형식의 이메일을 전송할 때, 일부 클라이언트에서는 외부 스타일시트를 참조할
  때 문제가 발생할 수 있기 때문에 css는 모두 인라인으로 작성되어야 한다.
  하지만 인라인 스타일을 사용하면 유지보수가 힘들고 코드 중복이 발생하게 된다.
  스타일과 html을 자동적으로 결합해주는 아래 두 가지 젬이 존재한다.
  [premailer-rails](https://github.com/fphilipe/premailer-rails)와
  [roadie](https://github.com/Mange/roadie).
<sup>[[link](#inline-email-styles)]</sup>

* <a name="background-email"></a>
  컨트롤러에서 요청에 대한 응답을 처리하는 도중에 이메일을 보내서는 안 된다.
  이는 페이지 로딩을 지연시키고, 여러 메일을 동시에 발송할 때 타임아웃이 될 수도
  있다. 이메일 전송은 [sidekiq](https://github.com/mperham/sidekiq)과 같은
  백그라운드 작업을 지원하는 젬을 사용해 이루어져야 한다.
<sup>[[link](#background-email)]</sup>

## 액티브 서포트 코어 확장(Active Support Core Extensions)
<a name="active-support-core-extensions"></a>

* <a name="try-bang"></a>
  `ActiveSupport#try!`보단 루비 2.3의 안전한 내비게이션 연산자인 `&.`을 사용하자
<sup>[[link](#try-bang)]</sup>

```ruby
# 나쁜 예
obj.try! :fly

# 좋은 예
obj&.fly
```

* <a name="active_support_aliases"></a>
  `ActiveSupport`보다 루비의 표준 라이브러리를 사용하자.
<sup>[[link](#active_support_aliases)]</sup>

```ruby
# 나쁜 예
'the day'.starts_with? 'th'
'the day'.ends_with? 'ay'

# 좋은 예
'the day'.start_with? 'th'
'the day'.end_with? 'ay'
```

* <a name="active_support_extensions"></a>
  흔하지 않은 ActiveSupport 확장보단 루비 표준 라이브러리를 사용하자.
<sup>[[link](#active_support_extensions)]</sup>

```ruby
# 나쁜 예
(1..50).to_a.forty_two
1.in? [1, 2]
'day'.in? 'the day'

# 좋은 예
(1..50).to_a[41]
[1, 2].include? 1
'the day'.include? 'day'
```

* <a name="inquiry"></a>
  ActiveSupport의 `Array#inquiry`, `Numeric#inquiry` 그리고 `String#inquiry` 보다는 루비의 비교 연산자를 사용하자.
<sup>[[link](#inquiry)]</sup>

```ruby
# 나쁜 예 - String#inquiry
ruby = 'two'.inquiry
ruby.two?

# 좋은 예
ruby = 'two'
ruby == 'two'

# 나쁜 예 - Array#inquiry
pets = %w(cat dog).inquiry
pets.gopher?

# 좋은 예
pets = %w(cat dog)
pets.include? 'cat'

# 나쁜 예 - Numeric#inquiry
0.positive?
0.negative?

# 좋은 예
0 > 0
0 < 0
```

## 시간(Time)
<a name="time"></a>

* <a name="tz-config"></a>
  `application.rb`에 타임존을 적절히 설정한다.
<sup>[[link](#tz-config)]</sup>

  ```Ruby
  config.time_zone = 'Eastern European Time'
  # 아래 옵션에는 :utc나 :local만을 지정할 수 있다. (기본 설정 값은 :utc)
  config.active_record.default_timezone = :local
  ```

* <a name="time-parse"></a>
  `Time.parse`를 사용하지 않는다.
<sup>[[link](#time-parse)]</sup>

  ```Ruby
  # 나쁜 예
  Time.parse('2015-03-02 19:05:37') 
  # => 이 메소드는 시스템 타임존에서 시간이 주어진 것으로 가정함

  # 좋은 예
  Time.zone.parse('2015-03-02 19:05:37') 
  # => Mon, 02 Mar 2015 19:05:37 EET +02:00
  ```

* <a name="time-now"></a>
  `Time.now`를 사용하지 않는다.
<sup>[[link](#time-now)]</sup>

  ```Ruby
  # 나쁜 예
  Time.now # => 타임존 설정과 무관하게 시스템의 시간을 반환한다

  # 좋은 예
  Time.zone.now # => Fri, 12 Mar 2014 22:04:47 EET +02:00
  Time.current # 위와 같지만 더 짧은 방법
  ```

## 번들러(Bundler)
<a name="bundler"></a>

* <a name="dev-test-gems"></a>
  `Gemfile`에는 개발 또는 테스트 환경에 필요한 젬들의 목록을 그룹별로 기술한다.
<sup>[[link](#dev-test-gems)]</sup>

* <a name="only-good-gems"></a>
  신뢰할만한 젬들만을 사용한다.
  잘 알려지지 않은 젬을 사용하고자 한다면 우선 소스 코드와 리뷰들을 살펴보자.
<sup>[[link](#only-good-gems)]</sup>

* <a name="os-specific-gemfile-locks"></a>
  다른 OS를 사용하는 개발자들과 함께 프로젝트를 진행하게 되면 지속적으로 `Gemfile.lock`이 변경될 것이다.
  따라서 Gemfile의 `darwin` 그룹에는 OS X 기반의 젬을 명시하고, `linux`그룹에는 리눅스 기반의 젬을 두어라.
<sup>[[link](#os-specific-gemfile-locks)]</sup>

  ```Ruby
  # Gemfile
  group :darwin do
    gem 'rb-fsevent'
    gem 'growl'
  end

  group :linux do
    gem 'rb-inotify'
  end
  ```

  특정 환경에서 적절한 젬을 사용하기 위해서 `config/application.rb`에 아래와 같이 추가한다.

  ```Ruby
  platform = RUBY_PLATFORM.match(/(linux|darwin)/)[0].to_sym
  Bundler.require(platform)
  ```

* <a name="gemfile-lock"></a>
  `Gemfile.lock` 파일은 버전 관리에 포함한다.
  이 파일은 무작위로 생성된 것이 아니므로, 같은 프로젝트를 진행하는 여러 개발자들이 `bundle install` 명령으로 같은 버전의 젬을 설치할 수 있게 도와준다.
<sup>[[link](#gemfile-lock)]</sup>

## 프로세스 관리(Managing processes)
<a name="managing-processes"></a>

* <a name="foreman"></a>
  프로젝트가 다양한 외부 프로세스에 의존적이라면 [foreman](https://github.com/ddollar/foreman)을 사용하여 관리한다.
<sup>[[link](#foreman)]</sup>

# 리소스

레일스 스타일과 관련된 반드시 읽어야할 훌륭한 자료들이 더 많이 있다. 아래 자료들을 참고해주세요.

* [The Rails 4 Way](http://www.amazon.com/The-Rails-Addison-Wesley-Professional-Ruby/dp/0321944275)
* [Ruby on Rails Guides](http://guides.rubyonrails.org/)
* [The RSpec Book](http://pragprog.com/book/achbd/the-rspec-book)
* [The Cucumber Book](http://pragprog.com/book/hwcuc/the-cucumber-book)
* [Everyday Rails Testing with RSpec](https://leanpub.com/everydayrailsrspec)
* [Rails 4 Test Prescriptions](https://pragprog.com/book/nrtest2/rails-4-test-prescriptions)
* [Better Specs for RSpec](http://betterspecs.org)

# 기여하기

이 문서는 아직 완성된 것이 아닙니다. 저는 이 가이드를 레일스 코딩 스타일에 관심이 있는  사람들과 함께 만들어 가면서, 모든 루비 커뮤니티에 유용한 자료가 되었으면 합니다.

가이드 개선을 위한 풀리퀘스트(pull requests)를 마음껏 보내주세요. 이에 대해 미리 감사의 말씀을 드립니다.

또한 이 프로젝트(와 RuboCop)에 기부를 원하시는 분은 [gittip](https://www.gittip.com/bbatsov)을 통해 참여할 수 있습니다.

[![Support via Gittip](https://rawgithub.com/twolfson/gittip-badge/0.2.0/dist/gittip.png)](https://www.gittip.com/bbatsov)

## 이 문서에 어떻게 기여할 수 있나요?

[기여하기](https://github.com/bbatsov/rails-style-guide/blob/master/CONTRIBUTING.md) 문서를 참고해주세요.

# 라이센스

이 문서는 ![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
[Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/deed.en_US)를 따릅니다.

# 공유해주세요

이 문서의 존재가 알려지지 않는다면 커뮤니티 기반의 스타일 가이드는 아무런 의미가 없습니다.
이 가이드에 대하여 트윗하거나, 친구와 동료들에게 공유해주세요. 댓글, 의견, 제안을 주시면 조금 더 나은 가이드를 만드는데 도움이 될 것입니다. 더 좋은 가이드를 만들고 싶지 않으신가요?

화이팅<br/>
[Bozhidar](https://twitter.com/bbatsov)
