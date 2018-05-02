---
title: WebDriver.js
date: 2015-01-21
---

[この記事](http://qiita.com/takatama/items/ddaf9e85c71f7f0e922a)を参考にweb自動化の練習

Selenium Server

~~~
start-selenium
~~~

実行

~~~
node webdrivertest.js
~~~

~~~

﻿var webdriver = require('selenium-webdriver');
var By = webdriver.By;
var SeleniumServer = require('selenium-webdriver/remote').SeleniumServer;
var pathToSeleniumJar = 'C:\\Program Files\\nodejs\\node_modules\\selenium-standalone\\.selenium\\2.44.0\\server.jar'
var server = new SeleniumServer(pathToSeleniumJar,{ port : 4444});

server.start();

var driver = new webdriver
  .Builder()
  .usingServer(server.address())
  .withCapabilities(webdriver.Capabilities.chrome())
  .build();

var $ = driver.findElement.bind(driver);

driver.get('url');

// Login
$(By.name('LoginID')).sendKeys('id');
$(By.name('LoginPassword')).sendKeys('passwd');
$(By.name('btnLogin')).click();

//
var timeoutMSec = 2000;
driver
  .wait(webdriver.until.elementLocated(By.name('buttonCompanySelect')), timeoutMSec)
  .then(function() {
    console.log('login success');
    $(By.id('120')).click();
    $(By.id('buttonCompanySelect')).click();

    driver
      .wait(webdriver.until.elementLocated(By.id('btnProduct_1')), timeoutMSec)
      .then(function(){
       console.log('company selected');
       $(By.id('btnProduct_1')).click();

       driver
        .wait(webdriver.until.elementLocated(By.id('btnOK')), timeoutMSec)
        .then(function(){
         console.log('list selected');
         $(By.id('btnOK')).click();

        driver
         .wait(webdriver.until.elementLocated(By.id('btnOK')), timeoutMSec)
         .then(function(){
          console.log('date selected');
          $(By.id('btnOK')).click();

        })
      })
    })
  })
  .then(function() {
    //driver.quit();
  });

~~~
