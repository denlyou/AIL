# Laravel Study

## 마이그레이션 (Migrations)

> ### 참고
> - [라라벨 공식 메뉴얼 (v5.5)](https://laravel.com/docs/5.5/migrations)
> - [라라벨 번역 메뉴얼 (v5.4)](https://laravel.kr/docs/5.4/migrations)

### 소개
- 데이터베이스를 위한 버전 컨트롤 역할
- 데이터베이스 스키마를 제어
- 보통 Schema와 쌍을 이룸

---
### 제어 명령 (artisan 명령어)

#### 마이그레이션 생성하기 ( [ENv5.5](https://laravel.com/docs/5.5/migrations#generating-migrations) | [KRv5.4](https://laravel.kr/docs/5.4/migrations#generating-migrations) )

```shell
php artisan make:migration [마이그레이션명]
```

> - [라라벨]/database/migrations/[마이그레이션명_타임스템프].php 파일이 생성
> - 타임 스템프에 의해 순차적으로 관리

- 클래스의 구조
  - up()과 down()메소드를 가짐
  - up() 메소드는 생성, 수정 등의 변화
  - down()은 단순히 up()메소드의 동작을 취소

#### 실행하기

```shell
php artisan migrate
php artisan migrate --force  // 강제수행
```

#### 되돌리기 (Rollback)

```shell
php artisan migrate:rollback
php artisan migrate:rollback --step=5 // 5단계 뒤로
php artisan migrate:reset // 처음 단계로 돌아가기

php artisan migrate:refresh // 처음상태로 갔다가 돌아오기 (reset+migrate)
php artisan migrate:refresh --seed // 데이터베이스를 초기화 시키고 돌아오기
php artisan migrate:refresh --step=5 // 5단계만 취소했다 다시 수행
```

### 테이블 관리 ( [ENv5.5](https://laravel.com/docs/5.5/migrations#tables) | [KRv5.4](https://laravel.kr/docs/5.4/migrations#tables) )

```php
// 테이블 설정
Schema::create('users', function (Blueprint $table) {
    $table->increments('id'); // 생성
    if (Schema::hasTable('users')) { ... } // 테이블 확인
    if (Schema::hasColumn('users', 'email')) { ... } // 컬럼 확인
});
// 커넥션 변경
Schema::connection('foo')->create('users', function (Blueprint $table) {
    $table->increments('id');
})
// DB 엔진 설정
Schema::create('users', function (Blueprint $table) {
    $table->engine = 'InnoDB';
    $table->increments('id');
});
// 테이블 이름 변경
Schema::rename($from, $to); // 외래키 주의 (제약조건)
// 테이블 삭제
Schema::drop('users');
Schema::dropIfExists('users');
```

### 컬럼 관리 ( [ENv5.5](https://laravel.com/docs/5.5/migrations#columns) | [KRv5.4](https://laravel.kr/docs/5.4/migrations#columns) )

```php
Schema::table('users', function (Blueprint $table) {
    // Integer with Primary key 컬럼
    $table->increments('id');	// "UNSIGNED INTEGER"에 해당하는 Incrementing ID (프라이머리 키).
    $table->bigIncrements('id'); // "UNSIGNED BIG INTEGER"에 해당하는 Incrementing ID (프라이머리 키).
    // Integer 컬럼
    $table->integer('votes');	// 데이터베이스의 INTEGER
    $table->bigInteger('votes'); // 데이터베이스의 BIGINT
    $table->tinyInteger('numbers');	// 데이터베이스의 TINYINT.
    // 문자열 컬럼
    $table->char('name', 4); // CHAR에 해당하며 길이(length)를 가짐
    $table->string('email', 255); // VARCHAR에 해당하며 길이(length)를 가짐.
    $table->text('description'); // 데이터베이스의 TEXT.
    // 시간 컬럼
    $table->timestamps();	// created_at과 updated_at 컬럼을 추가함.
    $table->softDeletes(); // soft delete할 때 deleted_at 컬럼을 추가함.
    $table->timestamp('added_on'); // 데이터베이스의 TIMESTAMP.
    $table->nullableTimestamps(); // timestamps() 컬럼의 Null 허용이 가능한 버전.
    $table->dateTime('created_at');	// 데이터베이스의 DATETIME.

    // 기타
    $table->enum('choices', ['foo', 'bar']); // 데이터베이스의 ENUM.
    $table->ipAddress('visitor');	// ip주소 저장용
    $table->json('options'); // JSON 저장용
});
```

#### 컬럼 체인 메서드 => Modifiers

```php
Schema::table('users', function (Blueprint $table) {
    $table->string('email')
      ->nullable() // NULL 값들이 컬럼에 입력되는 것을 허용합니다
      ->unsigned() // UNSIGNED에 integer 컬럼을 설정합니다
	    ->default($value)	// 컬럼의 "기본"값을 설정합니다
      ->comment('my comment')	// 컬럼에 코멘트 추가
      // MySQL Only
      ->after('column')	// 컬럼을 다른 컬럼 "뒤"로 옮기세요 (MySQL의 경우에만)
      ->first()	// 테이블에 컬럼을 "맨 처음" 위치로 옮기세요 (MySQL의 경우에만)
      ->storedAs($expression)	// Create a stored generated column (MySQL의 경우에만)
      ->virtualAs($expression) // Create a virtual generated column (MySQL의 경우에만)
    ;
});
```

### 컬럼 수정하기

#### ** 필수 사항 ** :  Doctrine DBAL 라이브러리 필요

```shell
composer require doctrine/dbal
```

#### 속성 변경
- 컬럼 속성 변경 : `->change()`
- 컬럼 이름 변경 : `->renameColumn($from, $to)`
- 컬럼 삭제 : `->dropColumn($column)`

```php
Schema::table('users', function (Blueprint $table) {
    $table->string('name', 50)->change();
    $table->string('name', 50)->nullable()->change();
    $table->renameColumn('from', 'to');
    $table->dropColumn('votes');
    $table->dropColumn(['votes', 'avatar', 'location']);
});
```

#### 인덱스(Indexes) ( [ENv5.5](https://laravel.com/docs/5.5/migrations#indexes) | [KRv5.4](https://laravel.kr/docs/5.4/migrations#indexes) )

```php
Schema::table('users', function (Blueprint $table) {
    // 키 설정 방법
    $table->string('email')->unique(); // 필드 생성시 SuperKey(unique)
    $table->unique('email'); // 또는 생성후 지정
    $table->index(['account_id', 'created_at']); // 결합 인덱스 (AlternativeKey)
    $table->index('email', 'my_index_name'); // 두번쨰 인자는 인덱스 이름
    // 키 종류
    $table->primary('id'); // 프라이머리 키 추가.
    $table->primary(['first', 'last']); // 복합 키 추가.
    $table->unique('email'); // 유니크 인덱스 추가.
    $table->unique('state', 'my_index_name');	// 인덱스의 이름을 지정하기
    $table->unique(['first', 'last']);	// 복합키를 유니크 인덱스로 지정하기
    $table->index('state');	// 기본적인 인덱스 추가.
    // 인덱스 삭제하기
    $table->dropPrimary('users_id_primary'); // "users" 테이블에서 프라이머리 키 지우기.
    $table->dropUnique('users_email_unique'); // "users" 테이블에서 유니크 인덱스 지우기.
    $table->dropIndex('geo_state_index'); // "geo" 테이블에서 기본적인 인덱스 지우기.
    $table->dropIndex(['state']); // Drops index 'geo_state_index' : 배열 사용시 이름 추정
    // 외래키 설정
    $table->integer('user_id')->unsigned();
    $table->foreign('user_id')->references('id')->on('users') // foreignKey 설정
      ->onUpdate('cascade')->onDelete('restrict'); // constraint 설정
});
```

#### 주의 : 인덱스 길이 & MySQL / MariaDB
> 라라벨은 기본적으로 "emojis"를 데이터베이스에 저장할 수 있는 utf8mb4 캐릭터셋을 사용합니다. 5.7.7 이전의 MySQL 이나 10.2.2 이전의 MariaDB를 사용하는 경우, MySQL이 인덱스를 구성할 수 있게 하기 위해서 마이그레이션으로 생성된 문자열 길이를 수동으로 조정해야 할 수도 있습니다. AppServiceProvider 파일 안에서 Schema::defaultStringLength 메소드를 호출하여 이를 설정할 수 있습니다.

```php
// AppServiceProvider.php
use Illuminate\Support\Facades\Schema;
public function boot(){
    Schema::defaultStringLength(191);
}
```
