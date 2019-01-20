---
layout: post
title: "PHP unit test demo"
description: "unit test is the fundmental"
---

### 大牛的话(翻译后)

Martin Fowler:

	无论什么时候,当你试着在print或其他debugger表达式里输入内容时，你应该把它写成一个测试用例


单元测试是代码质量的第一道关口。做好了单元测试，有利于尽早发现问题，解决问题。对CI/CD来说也是很重要的一个环节。当然，写单元测试会增加编写代码的工作量，所以一些公司或项目为了赶工期，单元测试就被忽略了，尽管大家都知道这个很重要。出来混，迟早要还的。欠下的，会加倍偿还。特别是随着产品的迭代，这个问题会逐渐凸显出来。

先来說一下php的单元测试,使用的是PHPUnit
### Install

参考文档：https://phpunit.readthedocs.io/en

linux下简单的几个步骤如下


	$wget https://phar.phpunit.de/phpunit-7.5.2.phar
	$chmod +x phpunit-7.5.2.phar
	$sudo mv phpunit-7.5.2.phar /usr/local/bin/phpunit
	$phpunit --version
	PHPUnit 7.5.2 by Sebastian Bergmann and contributors.

### 小试牛刀

创建一个文件比如test.php，内容如下

	<?php
	use PHPUnit\Framework\TestCase;

	class StackTest extends TestCase
	{
		public function testPushAndPop()
		{
			$stack = [];
			$this->assertSame(0, count($stack));

			array_push($stack, 'foo');
			$this->assertSame('foo', $stack[count($stack)-1]);
			$this->assertSame(1, count($stack));

			$this->assertSame('foo', array_pop($stack));
			$this->assertSame(1, count($stack));
		}

		/**
		* @test
		*/
		public function num()
		{
			$this->assertSame(1,1);
			$this->assertEquals(1,'1');
			$this->assertEquals(1,1.0);
		}
	}

执行测试phpunit ./test.php,返回结果如下
	
	
	PHPUnit 7.5.2 by Sebastian Bergmann and contributors.

	F.                                                                  2 / 2 (100%)

	Time: 183 ms, Memory: 10.00MB

	There was 1 failure:

	1) StackTest::testPushAndPop
	Failed asserting that 0 is identical to 1.

	/home/wm/code/php/phpunit/test.php:16

	FAILURES!
	Tests: 2, Assertions: 8, Failures: 1.

测试结果告诉我们，有两个测试方法，有8处断言，有一处失败，失败的原因是断言0和1相等，在代码的第16行。

我们修改16行为: $this->assertSame(0, count($stack));再执行测试

	PHPUnit 7.5.2 by Sebastian Bergmann and contributors.

	..                                                                  2 / 2 (100%)

	Time: 205 ms, Memory: 10.00MB

	OK (2 tests, 8 assertions)

通过了测试^_^

### 几点说明
	
	1.测试的类应该命名格式为ClassTest,大驼峰，而且以Test结尾,文件名和类名相同（我个人推测）
	2.测试的类要继承PHPUnit\Framework\TestCase(大多数情况下)
	3.测试的方法都要是public,声明一个方法是否是测试方法有如下两个方法
		- 方法名称的格式：test*
		- 或则在方法的注释中添加@test,来标识该方法是测试方法,如上
	4.在测试方法里有很多断言方法,如上assertSame(),assertEquals()
	5.出现失败后，就不会继续执行了。



