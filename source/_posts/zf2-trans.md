---
title: Zendframework2
date: 2015-10-11 23:23:09
categories: php
tags: [ZendFramework2,translate,多语言配置]
---
# Zend framework 2 多语言配置
---
&#160; &#160; &#160; &#160;企业网站一般常用的语言就是中文，有的为了高大上的与国际接轨再加个英文版（好吧，实际需要嘛，人家肯定不是为了装逼的嘛。)在开发中菜鸟的我一般都是把页面写两份，就把字翻译一下，好Low有木有。。。现在又需要日文了，我要复制三份了，我去！有这个时间不如研究一下多语言的配置喽。下面说一下自己通过搜索与实践配置ZF2的多语言吧。
&#160; &#160; &#160; &#160;这里我就当看文章的人都知道ZF2的目录了，不了解ZF2也没必要看额，ZF2和ZF不一样哦。因为本人也是菜鸟，对ZF2学习很浅，这里不多介绍，直接拿出代码和说明供你解决问题就行。
<!--more-->
&#160; &#160; &#160; &#160;先看module下面默认的Application模块下的配置文件
![img](/img/module-config.png)

&#160; &#160; &#160; &#160;在module.config.php内加入下面两段代码，具体修改看图
![img](/img/translator.png)
![img](/img/set-language.png)

&#160; &#160; &#160; &#160;接下来配置一下module.php
![img](/img/module.png)

&#160; &#160; &#160; &#160;Module.php多语言配置，直接在Zend Studio里建的工程，也是默认就开启了多语言配置。这里不说了，想省事的我下面会贴出代码。基本上不用你干什么， 你也可以不用理解代码是干啥的。完成了上面的工作之后基本上就可以了，什么？逗我？具体怎么用嘛，现在具体说一下。

&#160; &#160; &#160; &#160;先说一下语言包怎么写。上图Application目录下有个language文件夹，里面就包含很多语言包，很多国家啦，不过他是默认gettext类型的，也就是.mo文件，不过这里不采用那个，咱们用更简单的方式，php数组。所以上面配置类型的时候要写phparray额。由于上面配置的语言包位置在Application下的language文件夹里，所以在language下面新建个中文的语言包，**注意：这里语言包命名规则要和.mo命名一样哦，否则上面配置文件会匹配不着。**中文的是zh_CN,新建个zh_CN.php文件，内容我就简单写一下啦。
![img](/img/zh-cn.png)
---
&#160; &#160; &#160; &#160;OK，到这里就差什么了？访问呀有木有。语言初始化在框架加载的时候就做了，所以不用我们干什么了，直接用就行啦，在view文件里面，比如index.phtml里面直接输出下面的就行啦！
```php
<h1><?php echo $this->translate('Arthur');?></h1>
```
<font color="#00FFFF">这里说明一下：不是说多语言吗，我写了中英两个语言包，访问的时候他调用的是哪个呀？这个就是Module.php的作用啦，还记得上面跟大家说的不用看懂的代码了么，他的代码实际实现就是调动哪个语言包的功能。他们是有优先级的。</font>
1. 最高的是用户主动设置，也就是在你的访问网址后加上?Language=zh-CN，那他肯定显示中文。
2. 用户主动设置过，已经被保存在session里面了
3. 这个优先级最低，用户什么都没做的情况下，程序会从http响应头里面获取Accept-Language，这个就好像是自适应，根据用户地方语言来判断显示什么语言，比方你在用英文的国家，访问的话肯定调用你的英文语言包显示英文。
水平太次，写的不好大家见谅，可能有点太过墨迹，主要是为了像我这种菜鸟能看懂嘛。大家有问题的可以回复，我看到一定回答，大家一起学习。下面贴上代码：
---
Module.php
```php
<?php
namespace Application;

use Zend\Mvc\ModuleRouteListener;
use Zend\Mvc\MvcEvent;
use Application\Model\Project;
use Application\Model\ProjectTable;
use Application\Model\Accout;
use Application\Model\AccoutTable;
use Application\Model\Company;
use Application\Model\CompanyTable;
use Zend\Db\ResultSet\ResultSet;
use Zend\Db\TableGateway\TableGateway;
use Zend\Session\Container;
use Zend\Session\SessionManager;
use Zend\Session\Config\SessionConfig;

class Module
{
    public function getConfig()
    {
        return include __DIR__ . '/config/module.config.php';
    }

    public function getAutoloaderConfig()
    {
        return array(
            'Zend\Loader\StandardAutoloader' => array(
                'namespaces' => array(
                    __NAMESPACE__ => __DIR__ . '/src/' . __NAMESPACE__,
                ),
            ),
        );
    }
	public function getServiceConfig()
     {
         return array(
             'factories' => array(
                 'Application\Model\ProjectTable' =>  function($sm) {
                     $tableGateway = $sm->get('ProjectTableGateway2');
                     $table = new ProjectTable($tableGateway);
                     return $table;
                 },
                 'ProjectTableGateway2' => function ($sm) {
                     $dbAdapter = $sm->get('Zend\Db\Adapter\Adapter');
                     $resultSetPrototype = new ResultSet();
                     $resultSetPrototype->setArrayObjectPrototype(new Project());
                     return new TableGateway('projects', $dbAdapter, null, $resultSetPrototype);
                 },
                 
                 'Application\Model\AccoutTable' =>  function($sm) {
                 	$tableGateway = $sm->get('LoginTableGateway2');
                 	$table = new AccoutTable($tableGateway);
                 	return $table;
                 },
                 'LoginTableGateway2' => function ($sm) {
                 	$dbAdapter = $sm->get('Zend\Db\Adapter\Adapter');
                 	$resultSetPrototype = new ResultSet();
                 	$resultSetPrototype->setArrayObjectPrototype(new Accout());
                 	return new TableGateway('users', $dbAdapter, null, $resultSetPrototype);
                 },
                 'Application\Model\CompanyTable' =>  function($sm) {
                 	$tableGateway = $sm->get('CompanyTableGateway');
                 	$table = new CompanyTable($tableGateway);
                 	return $table;
                 },
                 'CompanyTableGateway' => function ($sm) {
                 	$dbAdapter = $sm->get('Zend\Db\Adapter\Adapter');
                 	$resultSetPrototype = new ResultSet();
                 	$resultSetPrototype->setArrayObjectPrototype(new Company());
                 	return new TableGateway('companies', $dbAdapter, null, $resultSetPrototype);
                 },
             ),
             
         );
     }

    public function onBootstrap(MvcEvent $e)
    {
        $eventManager = $e->getApplication()->getEventManager();
        $moduleRouteListener = new ModuleRouteListener();
        $moduleRouteListener->attach($eventManager);
 
        //调用 multiLanguageSupport 设置语言
        $this->multiLanguageSupport($e);
    }
 
    public function multiLanguageSupport(MvcEvent $e)
    {
        
        // 获取MvcTranslator，是关键一步
        $MvcTranslator = $e->getApplication()
            ->getServiceManager()
            ->get('MvcTranslator');
        
        /**
         * Get http request header
         * 获取头部信息，便于获取 Accept-Language
         */
        $headers = $e->getApplication()
            ->getRequest()
            ->getHeaders();
        
        /**
         * Get correnpond from module.config.php
         * 获得配置中的 correspond，也就是我们允许切换的语言
         */
        $correspond = $e->getApplication()
            ->getServiceManager()
            ->get('config');
        $correspond = $correspond['translator']['correspond'];
        
        /**
         * Session Language, Priority: 2
         * 设置一个Session容器，在设置了语言后，将语言保存在Session里。
         */
        $SessionConfig = new SessionConfig();
        $sessionTimeout = 60 * 60 * 24 * 365;
        $SessionConfig->setOptions(array(
            'gc_maxlifetime' => $sessionTimeout, //php gc 回收时间
            'remember_me_seconds' => $sessionTimeout, // 记住时间
            'cache_expire' => $sessionTimeout, // 缓存时间
            'cookie_lifetime' => $sessionTimeout //发送到浏览器的过期时间
        ));
        //获取Session容器里的语言，优先级介于浏览器和用户设置之间
        $Container = new Container('ModuleLanguageSetting', new SessionManager($SessionConfig));
        $sessionLanguage = $Container->offsetGet('lang');
        
        /**
         * Browser Language, Priority: 1 min
         * 获取浏览器的语言，优先级最小
         */
        $FiledValParts = array();
        if ($headers->has('Accept-Language')) {
            $FiledValParts = $headers->get('Accept-Language')->getPrioritized();
        }
        
        /**
         * Url Language, Priority: 3 max
         * 在Url中获取?Language=zh-CN，这个优先级最高，因为他可能是用户主动设置的语言
         */
        $requestLanguage = $e->getRequest()->getQuery('language');
        
        /**
         * Setting language
         * 通过各种优先级的判断得出最后需要使用的语言和语言缩写
         */
        $resultLanguage = '';
        $resultLanguageAbbr = '';
        if (! empty($requestLanguage) || ! empty($sessionLanguage)) {
            
            if (! empty($requestLanguage)) {
                $readyLanguge = $requestLanguage;
            } elseif (! empty($sessionLanguage)) {
                $readyLanguge = $sessionLanguage;
            }
            
            foreach ($correspond as $lang => $langItem) {
                if (in_array($readyLanguge, $langItem['include'])) {
                    
                    $resultLanguage = $lang;
                    $resultLanguageAbbr = $langItem['abbr'];
                    
                    if (! empty($requestLanguage))
                        $Container->offsetSet('lang', $requestLanguage);
                    break;
                }
            }
        } elseif (! empty($FiledValParts)) {
            $FiledValParts = $headers->get('Accept-Language')->getPrioritized();
            if (count($FiledValParts) > 0) {
                foreach ($FiledValParts as $part) {
                    foreach ($correspond as $lang => $langItem) {
                        $browserLanguage = $part->getLanguage();
                        if (in_array($browserLanguage, $langItem['include'])) {
                            $resultLanguage = $lang;
                            $resultLanguageAbbr = $langItem['abbr'];
                            break 2;
                        }
                    }
                }
            }
        }
        
        if (! empty($resultLanguage) && ! empty($resultLanguageAbbr)) {
            // 设置语言
            $MvcTranslator->setLocale($resultLanguage);
            // 设置要加载的其它的语言文件
            $MvcTranslator->addTranslationFile('phpArray', __DIR__ . '/../../vendor/ZF2/resources/languages/' . $resultLanguageAbbr . '/Zend_Validate.php', 'default', $resultLanguage);
        }
        
        \Zend\Validator\AbstractValidator::setDefaultTranslator($MvcTranslator);
    }
}
?>
```

module.config.php
```php
'translator' => array(
        'locale' => 'en_US',
        'translation_file_patterns' => array(
            array(
                //'type'     => 'gettext',
                'type'     => 'phparray',
                'base_dir' => __DIR__ . '/../language',
                //'pattern'  => '%s.mo',
                'pattern'  => '%s.php',
            ),
        ),
	'correspond' => array(
			'zh_CN' => array(
				'abbr' => 'zh',
				'include' => array(
					'zh',
					'zh-CN'
				)
			),
			'en_US' => array(
				'abbr' => 'en',
				'include' => array(
					'en',
					'en-US'
				)
			),
			'zh_TW' => array(
				'abbr' => 'zh_TW',
				'include' => array(
					'zh-TW'
				)
			)
		)
    )
```