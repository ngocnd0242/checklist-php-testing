# :heavy_check_mark: [1] Self-describing test method
> Unit Test method names must be self-describing
>
> Also focus on naming style, keep the naming style consistent across all the test methods and tests.

<details>
    <summary>:point_right: Chi tiết</summary>

**Mục đích:**
- Test case là tài liệu
- Đọc vào tên method test có thể biết mục đích của test case

**Thực hiện**
- Tên test method không cần phải quá ngắn gọn
- Tên test method phải chỉ ra điều kiện và expect của test case
- Thống nhất convention trong project, mặc định visibility của 1 method trong class là `public` nên có thể loại bỏ từ `public` trong method test

Chọn một trong các convention sau:
1. **[Recommend]** Sử dụng prefix `test_`

    ```php
    function test_it_returns_false_when_input_number_is_odd()
    ```
2. Sử dụng annotation `@test` thì tên test method không cần phải bắt đầu bằng `test_`

    ```php
    /* @test */
    function it_returns_false_when_input_number_is_odd()
    ```
3. Sử dụng `camelCase` thay cho `snake_case`, chỉ nên sử dụng nếu trong project đã viết theo cách này trước đó

    ```php
    function testItReturnsFalseWhenInputNumberIsOdd()
    ```

**FAQ**
1. Q: Tên test method có cần bao gồm tên method của class đang test không?

    A: Có thể. Nhưng tốt hơn là xem class là Unit cần test, khi đó ta đang đi test chức năng hay hành vi của class, khi thực hiện refactor có thể thay đổi tên method nhưng không cần thay đổi tên test method
</details>

# :heavy_check_mark: [2] A3 (Arrange, Asset, Act)
A3 (Arrange, Asset, Act)
- Arrange: thiết lập trạng thái, khởi tạo object, giả lập mock
- Act: Chạy unit đang cần test (method under test)
- Assert: So sánh expected với kết quả trả về

<details>
    <summary>:point_right: Chi tiết</summary>

**Mục đích:**
- Nội dung test method rõ ràng dễ đọc, dễ viết

**Thực hiện**
- Chia nội dung test làm 3 phần
    ```php
    function test_validation_failed_when_value_exceed_max_length()
    {
        // Arrange
        $username = str_pad('a', UsernameValidation::MAX_LENGTH + 1);

        // Act
        $validation = new UsernameValidation;
        $isValidUsername = $validation->isValid($username);

        // Assert
        $this->assertFalse($isValidUsername);
    }
    ```
- Ngoại lệ khi test một method throw exception, do vấn đề kỹ thuật nên phải gọi `expectionException()` trước khi gọi method:
    ```php
    function test_it_throws_exception_when_input_is_not_a_number()
    {
        // Assert that
        $this->expectException(InvalidArgumentException::class);

        // Arrange
        $calculator = new Calculator;
        $input1 = 'i am a string';
        $input2 = 100;

        // Act
        $calculator->add($input1, $input2);
    }
    ```
</details>

# :heavy_check_mark: [3] Use sematic/proper assert method
> Keep assert method descriptive. Use proper assert method to improve the readability of code and the error log.

<details>
    <summary>:point_right: Chi tiết</summary>

Thực tế ta có thể chỉ dùng `assertTrue()`:

```php
// assertEquals
$this->assertTrue($expected == $actual);
// Failed asserting that false is true.
// vs. Failed asserting that $actual matches expected $expected.

// assertSame
$this->assertTrue($expected === $actual);

// assertContains
$this->assertTrue(in_array($actual, $expected);

// assertCount
$this->assertTrue(count($actual) == $expected);

// assertInstanceOf
$this->assertTrue($actual instanceOf ExpectedClass);
```

Nhưng việc dùng method assert thích hợp giúp cho việc đọc hiểu dễ hơn (không phải thực hiện phép so sánh) và message được generate dễ hiểu hơn nếu test case failed.
</details>

# :heavy_check_mark: [4] If you write code, write tests

<details>
    <summary>:point_right: Chi tiết</summary>

**Thực hiện**

Mọi PR đều phải chú ý đến test
- PR thêm feature => viết test cho feature mới
- PR fix bug => viết test để tránh bug xảy ra 1 lần nữa
- PR refactor => chạy, update test để đảm bảo không phát sinh ảnh hưởng
- Nên tích hợp CI để chạy test

**FAQ**:
1. Q: Thời điểm tốt nhất để viết test?

    A: Thời điểm tốt nhất là khi code còn mới! Thời điểm mà cả code và test đều có thể dễ dàng thay đổi. Tưởng tượng code giống như _đất sét_, khi còn mới thì nó mềm và dễ nặn, nếu để lâu thì nó sẽ cứng và dễ vỡ :smile: 
</details>

# :heavy_check_mark: [5] Unit vs Integration?

<details>
    <summary>:point_right: Chi tiết</summary>

**Simple rule**
- Unit test
  + Test từng function hoặc method của một class
  + Không thực hiện những việc sau:
    * Truy vấn cơ sở dữ liệu (làm chậm quá trình test)
    * Sử dụng network (gửi mail, gọi api bên ngoài,...) (làm chậm, kết quả không ổn định vì phụ thuộc vào mạng)
    * Sử dụng file system (làm chậm quá trình test)
- Integration test
  + Test việc kết hợp giữa các unit (function, method) với nhau => test một nhóm Unit (ví dụ test route)
  + Có thể truy vấn cơ sở dữ liệu (thiết lập một database test riêng biệt)
  + Có thể sử dụng file system (test việc import/export file, file permission...)

**Lời khuyên**

Với người mới bắt đầu, bạn có thể bắt đầu đi từ integration test bằng việc test từng route.
Trong quá trình viết integration test cố gắng split ra unit test nhỏ hơn nếu được.

Quá nhiều integration test sẽ khiến thời gian chạy test lâu hơn, việc truy vết lỗi cũng khó khăn hơn do 1 feature chạy qua nhiều lớp, layer code.
</details>

# :heavy_check_mark: [6] My tests are fast!

<details>
    <summary>:point_right: Chi tiết</summary>

**Thực hiện**
- Ngoài việc chú trọng vào việc viết test case đúng, cần chú ý đến thời gian chạy test
- Hạn chế test database (integration), và nếu có thể thì dùng sqlite in-memory làm database test
- Test không gọi network hay api service ngoài
- Khi test làm việc với file, cân nhắc sử dụng [vfsStream](https://github.com/bovigo/vfsStream)
- Khi chạy phpunit để generate code coverage, sử dụng 1 trong 3 driver sau theo thứ tự ưu tiên từ trên xuống
    + `pcov` nếu PHPUnit version >= 8
        ```sh
        php -dextension=pcov.so -dpcov.enabled=1 -dpcov.directory=app ./vendor/bin/phpunit --coverage-text
        ```
        NOTE: `pcov.directory=app`, trong đó `app` là thư mục chứa source code
    + `phpdbg`
        ```sh
        phpdbg -qrr ./vendor/bin/phpunit --coverage-text
        ```
    + XDebug
        ```sh
        php -dzend_extension=xdebug.so ./vendor/bin/phpunit --coverage-text
        ```

**Tại sao?**
- Bạn sẽ phải chạy tests thường xuyên, lặp lại => Nếu tests chạy quá chậm sẽ làm ảnh hưởng đến tiến độ, tinh thần làm việc
- Dự án áp dụng CI để build, test và deploy => Nếu tests chạy quá chậm sẽ dẫn đến việc tích hợp cho cả team bị chậm. Thời gian build của CI mà quá 5 phút thì khó mà chấp nhận được
</details>

# :heavy_check_mark: [7] Quality over code coverage number!

<details>
    <summary>:point_right: Chi tiết</summary>

**Sự thật về code coverage**
- Không cần viết test đúng vẫn có thể đạt 100% coverage!
- Có trường hợp đã đạt 100% coverage rồi nhưng vẫn có khả năng lọt bug vì thiếu test case

=> Tham khảo thêm cách phpunit tính coverage => [link](https://github.com/sun7pro/phpunit-training-coverage/issues/2)

**Thực hiện**
- Chú trọng vào chất lượng test case, viết sao cho đủ test case? làm sao để test chạy nhanh hơn? làm sao để viết test dễ hơn, refactor code?
- Áp dụng mutation testing vào dự án nếu có thể, để có chỉ số đánh gía tốt hơn => [link](https://medium.com/@maks_rafalko/infection-mutation-testing-framework-c9ccf02eefd1)
</details>

# Code review
Tham khảo https://github.com/sun7pro/.github/blob/master/PULL_REQUEST_TEMPLATE.md để áp dụng pull request template cho dự án.
