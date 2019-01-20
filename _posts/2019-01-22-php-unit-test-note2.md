---
layout: post
title: "Dependence and Data Provider in PHPUnit" 
description: "Dependence and Data Provider in PHPUnit"
---

### 牛人的话(翻译后)

Adrian Kuhn et.al:
	
	写单元测试是一个非常好的做法，帮助开发定位和修复bug，重构代码并作为测试软件的一个单元的文档。
	为了能达到这个效果，单元测试要考虑到程序中所有路径。一个方法或函数通常只覆盖一个特殊的路径。
	然而，一个测试方法并不是一个封闭的，独立的实体。通常在测试方法之间的隐式依赖，就藏在某个测试场景的实现中。


### Test Dependencies

- producer: 返回值给其他方法使用的方法
- consumer: 依靠别的方法和它们的返回值的方法
- 一个方法，可以兼顾这个两个角色
- consumer方法需要在方法注释中标注@depends testFunc
- 一个consumer方法可以一个或多个@depends,当有多个@depends时，@depends的声明顺序就是consumer参数顺序
	- 参数的传递，默认是引用传参
	- @depends clone 就是深复制，传值传参
	- @depends shallowClone 浅复制
- consumer方法有多个@depends时，要确保这些producer方法要写在consumer的前面，不然就会出现skip

代码如下：

	<?php
	use PHPUnit\Framework\TestCase;

	class MultipleDependenciesTest extends TestCase
	{
		public function testProducerFirst()
		{
			$this->assertTrue(true);
			return 'first';
		}

		public function testProducerSecond()
		{
			$this->assertTrue(true);
			return 'second';
		}

		/**
		 * @depends testProducerFirst
		 * @depends testProducerSecond
		 */
		public function testConsumer($a, $b)
		{
			$this->assertSame('first', $a);
			$this->assertSame('second', $b);
		}
	}

### Data Providers

- 某个方法的注释中用 <code>@dataProvider </code>这个特殊注释 标识了某个方法，则这个方法就是data provider
	- 如<code>@dataProvider testFunc</code> testFunc就是data provider
- data provider返回值必须要是n维数组(n>=2),或可以迭代的对象(Iterator),并且这个对象每一次迭代都要返回一个数组
- 测试方法的参数可以由一个或多个data provider 提供
- 当测试方法同时有@dataProvider和@depends时，@dataProvider的参数要先于@depends

- demo

```php
<?php
use PHPUnit\Framework\TestCase;

class DependencyAndDataProviderComboTest extends TestCase
{
	public function provider()
	{
		return [['provider1'], ['provider2']];
	}

	public function testProducerFirst()
	{
		$this->assertTrue(true);
		return 'first';
	}

	public function testProducerSecond()
	{
		$this->assertTrue(true);
		return 'second';
	}

	/**
	 * @depends testProducerFirst
	 * @depends testProducerSecond
	 * @dataProvider provider
	 */
	public function testConsumer()
	{
		$this->assertSame(
			['provider1', 'first', 'second'],
			func_get_args()
			/**
			func_get_args()依次是
			['provider1', 'first', 'second'],
			['provider2', 'first', 'second']
			因为 provider迭代了两次，第一次返回['provider1'], 字符串'provider1'就传递给了testConsumer，作为第一个参数，
			**/
		);
	}
}
```
