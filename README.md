reCAPTCHA
===============

[![Build Status](https://travis-ci.org/minetro/reCAPTCHA.svg?branch=master)](https://travis-ci.org/minetro/reCAPTCHA)
[![Code coverage](https://img.shields.io/coveralls/minetro/reCAPTCHA.svg?style=flat-square)](https://coveralls.io/r/minetro/reCAPTCHA)
[![Downloads this Month](https://img.shields.io/packagist/dm/minetro/recaptcha.svg?style=flat)](https://packagist.org/packages/minetro/recaptcha)
[![Latest stable](https://img.shields.io/packagist/v/minetro/recaptcha.svg?style=flat)](https://packagist.org/packages/minetro/recaptcha)
[![Code Climate](https://codeclimate.com/github/minetro/reCAPTCHA/badges/gpa.svg)](https://codeclimate.com/github/minetro/reCAPTCHA)
[![HHVM Status](https://img.shields.io/hhvm/minetro/reCAPTCHA.svg?style=flat)](http://hhvm.h4cc.de/package/minetro/reCAPTCHA)

Google reCAPTCHA implementation for Nette Framework.

# Pre-install

Add your site to the sitelist in reCAPTCHA administration.

https://www.google.com/recaptcha/admin#list

# Install

## Nette >=2.3
```sh
$ composer require minetro/recaptcha:~1.5.0
```

## Nette 2.2 
```sh
$ composer require minetro/recaptcha:~1.4.0
```

![reCAPTCHA](https://raw.githubusercontent.com/minetro/recaptcha/master/recaptcha.png)

# Configuration

## Automatic
```yaml
extensions:
    recaptcha: Minetro\Forms\reCAPTCHA\ReCaptchaExtension
    
recaptcha:
    secretKey: ***
    siteKey: ***
```
## Manual

### NEON - parameters
```yaml
# reCAPTCHA
parameters:
    reCAPTCHA:
        siteKey: ***key**
        secretKey: ***key**
```

### NEON - services
```yaml
services:
    reCAPTCHA.validator:
        class: Minetro\Forms\reCAPTCHA\ReCaptchaValidator
        implement: Minetro\Forms\reCAPTCHA\IReCaptchaValidatorFactory
        arguments: [%reCAPTCHA.secretKey%]
    
    reCAPTCHA.holder:
        factory: Minetro\Forms\reCAPTCHA\ReCaptchaHolder::factory(%reCAPTCHA.siteKey%)
        tags: [run]
```

You should call `ReCaptchaBinding::bind(%reCAPTCHA.siteKey%)`, if you want use native `$form->addReCaptcha()` method.

# Usage 

## Forms
```php
use Minetro\Forms\reCAPTCHA\ReCaptchaField;
use Minetro\Forms\reCAPTCHA\ReCaptchaHolder;
use Nette\Application\UI\Form;

class MyForm extends Form
{

    /**
     * @return ReCaptchaField
     */
    public function addReCaptcha($name = 'recaptcha')
    {
        return $this[$name] = new ReCaptchaField(ReCaptchaHolder::getSiteKey());
    }

}
```

## Presenter/Control
```php
use Minetro\Forms\reCAPTCHA\ReCaptchaField;
use Minetro\Forms\reCAPTCHA\IReCaptchaValidatorFactory;
use Nette\Application\UI\Form;

/** @var IReCaptchaValidatorFactory @inject */
public $reCaptchaValidatorFactory;

/**
 * @return Form
 */
protected function createComponentForm() 
{
    $form = new Form();
    
    $form['recaptcha'] = $recaptcha = new ReCaptchaField($this->siteKey); 
    
    $validator = $this->reCaptchaValidatorFactory->create();
    $recaptcha->addRule([$validator, 'validateControl'], 'Vypadá to, že nejste člověk.');
    
    // ...
}
```

## JavaScript

Before `</body>` element.

```html
<!-- re-Captcha -->
<script src='https://www.google.com/recaptcha/api.js'></script>
```

