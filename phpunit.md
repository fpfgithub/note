#### PHPUnit 支持共享建立基境的代码。在运行某个测试方法前，会调用一个名叫 setUp() 的模板方法。setUp() 是创建测试所用对象的地方。当测试方法运行结束后，不管是成功还是失败，都会调用另外一个名叫 tearDown() 的模板方法。tearDown() 是清理测试所用对象的地方。

` (1)Strict Standards:  Declaration of Test\UnitTest::setUp() should be compatible with UnitTestCase::setUp(Phalcon\DiInterface $di = NULL, Phalcon\Config $config = NULL) in /data/cap/V3/Gitdoc/digital-model/tests/testsTestUnitTest.php on line 9 `

` (2)PHP Fatal error:  Class name must be a valid object or a string in /data/cap/V3/Gitdoc/digital-model/vendor/phalcon/incubator/Library/Phalcon/Test/UnitTestCase.php on line 87`

    处理:https://docs.phalconphp.com/en/latest/reference/unit-testing.html

````
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
````
