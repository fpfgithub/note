#### PHPUnit 支持共享建立基境的代码。在运行某个测试方法前，会调用一个名叫 setUp() 的模板方法。setUp() 是创建测试所用对象的地方。当测试方法运行结束后，不管是成功还是失败，都会调用另外一个名叫 tearDown() 的模板方法。tearDown() 是清理测试所用对象的地方。

` (1)Strict Standards:  Declaration of Test\UnitTest::setUp() should be compatible with UnitTestCase::setUp(Phalcon\DiInterface $di = NULL, Phalcon\Config $config = NULL) in /data/cap/V3/Gitdoc/digital-model/tests/testsTestUnitTest.php on line 9 `

` (2)PHP Fatal error:  Class name must be a valid object or a string in /data/cap/V3/Gitdoc/digital-model/vendor/phalcon/incubator/Library/Phalcon/Test/UnitTestCase.php on line 87`

    处理:https://docs.phalconphp.com/en/latest/reference/unit-testing.html

```php
     /**
     * Class UnitTest
     */
    class UnitTest extends \UnitTestCase
    {
        protected $stack;

        public function setUp()
        {
            parent::setUp();
            $this->stack = array();
        }
```

#### 测试执行时长的超时限制
如果安装了 PHP_Invoker 包并且 pcntl 扩展可用，那么可以对测试的执行时长进行限制。此时间限制可以用命令行选项 --enforce-time-limit 或在 PHPUnit 的 XML 配置文件中设置 beStrictAboutTestSize="true" 来启用。

带有 @large 标注的测试如果执行时间超过60秒将视为失败。此超时限制可以通过XML配置文件中的 timeoutForLargeTests 属性进行配置。

带有 @medium 标注的测试如果执行时间超过10秒将视为失败。此超时限制可以通过XML配置文件中的 timeoutForMediumTests 属性进行配置。

没有 @medium 或 @large 标注的测试都将视同为带有 @small标注，这类测试如果执行时间超过1秒将视为失败。此超时限制可以通过XML配置文件中的 timeoutForSmallTests 属性进行配置。

实测 低版本的@small标注不能省略处理 如果加了时间限制必须标注过才会生效

```php
Time: 1.29 seconds, Memory: 12.75MB

There was 1 error:

1) Test\UnitTest::testALL
   PHP_Invoker_TimeoutException: Execution aborted after 1 second

/data/cap/V3/Gitdoc/digital-model/tests/testsTestUnitTest.php:26

FAILURES!
Tests: 1, Assertions: 0, Errors: 1.
```
