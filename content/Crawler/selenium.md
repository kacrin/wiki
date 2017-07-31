---
title: "selenium"
layout: page
date: 2017-03-15 00:00
---

# intro

[催庆才 selenium](http://cuiqingcai.com/category/technique/python)
[URL-Team 数据挖掘](https://www.urlteam.org/tag/%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98/)


Selenium 是什么？一句话，自动化测试工具。它支持各种浏览器，包括 Chrome，Safari，Firefox 等主流界面式浏览器，如果你在这些浏览器里面安装一个 Selenium 的插件，那么便可以方便地实现Web界面的测试。换句话说叫 Selenium 支持这些浏览器驱动。话说回来，PhantomJS不也是一个浏览器吗，那么 Selenium 支持不？答案是肯定的，这样二者便可以实现无缝对接了。

然后又有什么好消息呢？Selenium支持多种语言开发，比如 Java，C，Ruby等等，有 Python 吗？那是必须的！哦这可真是天大的好消息啊。

安装下 Python 的 Selenium 库，再安装好 PhantomJS，不就可以实现 Python＋Selenium＋PhantomJS 的无缝对接了嘛！PhantomJS 用来渲染解析JS，Selenium 用来驱动以及与 Python 的对接，Python 进行后期的处理，完美的三剑客

Selenium 有两个版本，目前最新版本是 2.53.1（2016/3/22）

Selenium 2，又名 WebDriver，它的主要新功能是集成了 Selenium 1.0 以及 WebDriver（WebDriver 曾经是 Selenium 的竞争对手）。也就是说 Selenium 2 是 Selenium 和 WebDriver 两个项目的合并，即 Selenium 2 兼容 Selenium，它既支持 Selenium API 也支持 WebDriver API。

更多详情可以查看 Webdriver 的简介。

[SeleniumPython文档](http://selenium-python.readthedocs.io/index.html)

# install

```
pip install selenium
```

下载源码[https://pypi.python.org/pypi/selenium](https://pypi.python.org/pypi/selenium)

然后解压后运行下面的命令进行安装
```
python setup.py install
```

### phantomjs

使用phantomjs 无界面浏览器 , 下载对应系统版本[http://phantomjs.org/](http://phantomjs.org/)

linux 解压后在bin下 把phantomjs 
```
mv phantomjs /usr/bin/
```

### Firefox 

首先安装 Firefox 火狐浏览器

```
yun -y install firefox
```

下载驱动 [https://github.com/mozilla/geckodriver/releases](https://github.com/mozilla/geckodriver/releases) 下载对应版本驱动

windows 下 下载解压后将getckodriver.exe复制到Firefox的安装目录下，如（C:\Program Files\Mozilla Firefox），并在环境变量Path中添加路径：C:\Program Files\Mozilla Firefox

linux 下 解压后将geckodriverckod 存放至 /usr/bin/ 路径下即可

安装 界面显示大小包 问题摘自[https://stackoverflow.com/questions/39547598/selenium-common-exceptions-webdriverexception-message-connection-refused](https://stackoverflow.com/questions/39547598/selenium-common-exceptions-webdriverexception-message-connection-refused)

```
pip install pyvirtualdisplay
yum install -y Xvfb 
# debian 下
apt-get install xvfb
```

python 代码

```
from pyvirtualdisplay import Display
from selenium import webdriver

display = Display(visible=0, size=(800, 600))
display.start()

browser = webdriver.Firefox()
browser.get('http://www.python.org')

browser.close()
```

# to Use
我们先来一个小例子感受一下 Selenium，这里我们用 Chrome 浏览器来测试，方便查看效果，到真正爬取的时候换回 PhantomJS 即可。
```
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('http://www.baidu.com/')
```
运行这段代码，会自动打开浏览器，然后访问百度。
如果程序执行错误，浏览器没有打开，那么应该是没有装 Chrome 浏览器或者 Chrome 驱动没有配置在环境变量里。下载驱动，然后将驱动文件路径配置在环境变量即可。

浏览器驱动下载

比如我的是 Mac OS，就把下载好的文件放在 /usr/bin 目录下就可以了。
模拟提交

下面的代码实现了模拟提交提交搜索的功能，首先等页面加载完成，然后输入到搜索框文本，点击提交。
```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()
driver.get("http://www.python.org")
assert "Python" in driver.title
elem = driver.find_element_by_name("q")
elem.send_keys("pycon")
elem.send_keys(Keys.RETURN)
print driver.page_source
```
	
WebDriver 提供了许多寻找网页元素的方法，譬如 find_element_by_* 的方法。例如一个输入框可以通过  find_element_by_name 方法寻找 name 属性来确定。

然后我们输入来文本然后模拟点击了回车，就像我们敲击键盘一样。我们可以利用 Keys 这个类来模拟键盘输入。

最后最重要的一点

获取网页渲染后的源代码。

输出 page_source 属性即可。

这样，我们就可以做到网页的动态爬取了。
测试用例

有了以上特性，我们当然可以用来写测试样例了。
```
import unittest
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

class PythonOrgSearch(unittest.TestCase):

    def setUp(self):
        self.driver = webdriver.Chrome()

    def test_search_in_python_org(self):
        driver = self.driver
        driver.get("http://www.python.org")
        self.assertIn("Python", driver.title)
        elem = driver.find_element_by_name("q")
        elem.send_keys("pycon")
        elem.send_keys(Keys.RETURN)
        assert "No results found." not in driver.page_source

    def tearDown(self):
        self.driver.close()

if __name__ == "__main__":
    unittest.main()
```
运行程序，同样的功能，我们将其封装为测试标准类的形式。

测试用例是继承了 unittest.TestCase 类，继承这个类表明这是一个测试类。setUp方法是初始化的方法，这个方法会在每个测试类中自动调用。每一个测试方法命名都有规范，必须以 test 开头，会自动执行。最后的 tearDown 方法会在每一个测试方法结束之后调用。这相当于最后的析构方法。在这个方法里写的是 close 方法，你还可以写 quit 方法。不过 close 方法相当于关闭了这个 TAB 选项卡，然而 quit 是退出了整个浏览器。当你只开启了一个 TAB 选项卡的时候，关闭的时候也会将整个浏览器关闭。
页面操作
页面交互

仅仅抓取页面没有多大卵用，我们真正要做的是做到和页面交互，比如点击，输入等等。那么前提就是要找到页面中的元素。WebDriver提供了各种方法来寻找元素。例如下面有一个表单输入框。
```
<input type="text" name="passwd" id="passwd-id" />
```

我们可以这样获取它
```
element = driver.find_element_by_id("passwd-id")
element = driver.find_element_by_name("passwd")
element = driver.find_elements_by_tag_name("input")
element = driver.find_element_by_xpath("//input[@id='passwd-id']")
```

你还可以通过它的文本链接来获取，但是要小心，文本必须完全匹配才可以，所以这并不是一个很好的匹配方式。

而且你在用 xpath 的时候还需要注意的是，如果有多个元素匹配了 xpath，它只会返回第一个匹配的元素。如果没有找到，那么会抛出 NoSuchElementException 的异常。

获取了元素之后，下一步当然就是向文本输入内容了，可以利用下面的方法
```
element.send_keys("some text")
```

同样你还可以利用 Keys 这个类来模拟点击某个按键。
```
element.send_keys("and some", Keys.ARROW_DOWN)
```

你可以对任何获取到到元素使用 send_keys 方法，就像你在 GMail 里面点击发送键一样。不过这样会导致的结果就是输入的文本不会自动清除。所以输入的文本都会在原来的基础上继续输入。你可以用下面的方法来清除输入文本的内容。
```
element.clear()
```

这样输入的文本会被清除。
填充表单

我们已经知道了怎样向文本框中输入文字，但是其它的表单元素呢？例如下拉选项卡的的处理可以如下
```
element = driver.find_element_by_xpath("//select[@name='name']")
all_options = element.find_elements_by_tag_name("option")
for option in all_options:
    print("Value is: %s" % option.get_attribute("value"))
    option.click()
```

首先获取了第一个 select 元素，也就是下拉选项卡。然后轮流设置了 select 选项卡中的每一个 option 选项。你可以看到，这并不是一个非常有效的方法。

其实 WebDriver 中提供了一个叫 Select 的方法，可以帮助我们完成这些事情。
```
from selenium.webdriver.support.ui import Select
select = Select(driver.find_element_by_name('name'))
select.select_by_index(index)
select.select_by_visible_text("text")
select.select_by_value(value)
```
	
如你所见，它可以根据索引来选择，可以根据值来选择，可以根据文字来选择。是十分方便的。

全部取消选择怎么办呢？很简单
```
select = Select(driver.find_element_by_id('id'))
select.deselect_all()
```
这样便可以取消所有的选择。

另外我们还可以通过下面的方法获取所有的已选选项。
```
select = Select(driver.find_element_by_xpath("xpath"))
all_selected_options = select.all_selected_options
```

获取所有可选选项是
```
options = select.options
```

如果你把表单都填好了，最后肯定要提交表单对吧。怎吗提交呢？很简单
```
driver.find_element_by_id("submit").click()
```
	
这样就相当于模拟点击了 submit 按钮，做到表单提交。

当然你也可以单独提交某个元素
```
element.submit()
```
	
方法，WebDriver 会在表单中寻找它所在的表单，如果发现这个元素并没有被表单所包围，那么程序会抛出 NoSuchElementException 的异常。
元素拖拽

要完成元素的拖拽，首先你需要指定被拖动的元素和拖动目标元素，然后利用 ActionChains 类来实现。
```
element = driver.find_element_by_name("source")
target = driver.find_element_by_name("target")

from selenium.webdriver import ActionChains
action_chains = ActionChains(driver)
action_chains.drag_and_drop(element, target).perform()
```

这样就实现了元素从 source 拖动到 target 的操作。
页面切换

一个浏览器肯定会有很多窗口，所以我们肯定要有方法来实现窗口的切换。切换窗口的方法如下
```
driver.switch_to_window("windowName")
```

另外你可以使用 window_handles 方法来获取每个窗口的操作对象。例如
```
for handle in driver.window_handles:
    driver.switch_to_window(handle)
```

另外切换 frame 的方法如下
```
driver.switch_to_frame("frameName.0.child")
```
	
这样焦点会切换到一个 name 为 child 的 frame 上。
弹窗处理

当你出发了某个事件之后，页面出现了弹窗提示，那么你怎样来处理这个提示或者获取提示信息呢？
```
alert = driver.switch_to_alert()
```

通过上述方法可以获取弹窗对象。
历史记录

那么怎样来操作页面的前进和后退功能呢？
```
driver.forward()
driver.back()
```

嗯，简洁明了。
Cookies处理

为页面添加 Cookies，用法如下
	
```
# Go to the correct domain
driver.get("http://www.example.com")
 
# Now set the cookie. This one's valid for the entire domain
cookie = {‘name’ : ‘foo’, ‘value’ : ‘bar’}
driver.add_cookie(cookie)

获取页面 Cookies，用法如下
# Go to the correct domain
driver.get("http://www.example.com")

# And now output all the available cookies for the current URL
driver.get_cookies()
```
以上便是 Cookies 的处理，同样是非常简单的。
元素选取

关于元素的选取，有如下的API
单个元素选取

```
find_element_by_id
find_element_by_name
find_element_by_xpath
find_element_by_link_text
find_element_by_partial_link_text
find_element_by_tag_name
find_element_by_class_name
find_element_by_css_selector
```

多个元素选取

```
find_elements_by_name
find_elements_by_xpath
find_elements_by_link_text
find_elements_by_partial_link_text
find_elements_by_tag_name
find_elements_by_class_name
find_elements_by_css_selector
```

另外还可以利用 By 类来确定哪种选择方式

```
from selenium.webdriver.common.by import By
driver.find_element(By.XPATH, '//button[text()="Some text"]')
driver.find_elements(By.XPATH, '//button')
```

By 类的一些属性如下
```
ID = "id"
XPATH = "xpath"
LINK_TEXT = "link text"
PARTIAL_LINK_TEXT = "partial link text"
NAME = "name"
TAG_NAME = "tag name"
CLASS_NAME = "class name"
CSS_SELECTOR = "css selector"
```

更详细的元素选择方法参见官方文档

元素选择
页面等待

这是非常重要的一部分，现在的网页越来越多采用了 Ajax 技术，这样程序便不能确定何时某个元素完全加载出来了。这会让元素定位困难而且会提高产生 ElementNotVisibleException 的概率。

所以 Selenium 提供了两种等待方式，一种是隐式等待，一种是显式等待。

隐式等待是等待特定的时间，显式等待是指定某一条件直到这个条件成立时继续执行。
显式等待

显式等待指定某个条件，然后设置最长等待时间。如果在这个时间还没有找到元素，那么便会抛出异常了。

```
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Chrome()
driver.get("http://somedomain/url_that_delays_loading")
try:
    element = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, "myDynamicElement"))
    )
finally:
    driver.quit()
```

程序默认会 500ms 调用一次来查看元素是否已经生成，如果本来元素就是存在的，那么会立即返回。

下面是一些内置的等待条件，你可以直接调用这些条件，而不用自己写某些等待条件了。

```
title_is
title_contains
presence_of_element_located
visibility_of_element_located
visibility_of
presence_of_all_elements_located
text_to_be_present_in_element
text_to_be_present_in_element_value
frame_to_be_available_and_switch_to_it
invisibility_of_element_located
element_to_be_clickable – it is Displayed and Enabled.
staleness_of
element_selection_state_to_be
element_located_selection_state_to_be
alert_is_present
```

```
from selenium.webdriver.support import expected_conditions as EC

wait = WebDriverWait(driver, 10)
element = wait.until(EC.element_to_be_clickable((By.ID,'someid')))
```

隐式等待

隐式等待比较简单，就是简单地设置一个等待时间，单位为秒。
```
from selenium import webdriver

driver = webdriver.Chrome()
driver.implicitly_wait(10) # seconds
driver.get("http://somedomain/url_that_delays_loading")
myDynamicElement = driver.find_element_by_id("myDynamicElement")
```

当然如果不设置，默认等待时间为0。




