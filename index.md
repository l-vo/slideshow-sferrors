title: Error workflow - Logging
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
.bottom-bar[
  {{title}}
]

---

class: impact

# {{title}}
## In a Symfony application

---
<div style="text-align: center">
<img src="img/me.png" style="width: 25%"/><br />
<br />
<strong>Laurent VOULLEMIER</strong>
<br />
<br />
@Sensiolabs<br /><br />
Team P50/MiddleOffice
</div>
---

<img width="100%" src="img/exception.png" alt="Workflow diagram" />

---

# Meyclub

<div style="text-align: center">
<img style="margin-top: -40px;" width="70%" src="img/listener.png" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=text%2Fx-php&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=namespace%2520AppBundle%255CEventListener%253B%250A%250Aclass%2520HttpRequestListener%250A%257B%250A%2520%2520public%2520function%2520onKernelException%28GetResponseForExceptionEvent%2520%2524event%29%250A%2520%2520%257B%250A%2520%2520%2520%2520%252F%252F...%250A%2520%2520%2520%2520if%2520%28%2524request-%253EisXmlHttpRequest%28%29%29%2520%257B%250A%2520%2520%2520%2520%2520%2520%252F%252F...%2520%2520%250A%2520%2520%2520%2520%2520%2520%2524response%2520%253D%2520%252F*...*%252F%253B%250A%2520%2520%2520%2520%257D%2520elseif%28%252F*...*%252F%29%2520%257B%250A%2520%2520%2520%2520%2520%2520%252F%252F...%250A%2520%2520%2520%2520%2520%2520%2524response%2520%253D%2520%252F*...*%252F%253B%250A%2520%2520%2520%2520%257D%2520else%2520%257B%250A%2520%2520%2520%2520%2520%2520%252F%252F...%250A%2520%2520%2520%2520%2520%2520%2524response%2520%253D%2520%252F*...*%252F%253B%250A%2520%2520%2520%2520%2520%2520%2524logger-%253Elog%28%252F*...*%252F%29%253B%250A%2520%2520%2520%2520%257D%250A%2520%2520%2520%2520%250A%2520%2520%2520%2520%2524event-%253EsetResponse%28%2524response%29%253B%250A%2520%2520%257D%2520%2520%250A%257D%2520%2520" />
</div>

---
# Meyclub HttpRequestListener

<br /><br />
* Responsible for creating error representation and logging errors
<br /><br />
* If Logger call is forgetten, exception won't be logged
  <br /><br />  
* $event->setResponse prevent next listeners to be executed
---
# Exception listeners on Meyclub

(bin/console debug:event-dispatcher kernel.exception)

Listener | Priority
--- | ---
AppBundle\EventListener\HttpRequestListener::onKernelException() | 0
<font color="red">Meyclub\Core\TechnicalBundle\Listener\ExceptionListener::onKernelException()</font> | 0
<font color="red">Symfony\WebpackEncoreBundle\EventListener\ExceptionListener::onKernelException()</font> | 0
<font color="red">ApiPlatform\Core\Bridge\Symfony\Validator\EventListener\ValidationExceptionListener::onKernelException()</font> | 0
<font color="red">Meyclub\Api\ApiPlatformBundle\EventListener\ApiPlatformBundleExceptionSubscriber::onException()</font> | 0
<font color="red">Meyclub\Api\ApiPlatformBundle\EventListener\ResourceNotFoundExceptionSubscriber::onResourceNotFoundException()</font> | 0
<font color="red">Symfony\Component\HttpKernel\EventListener\ProfilerListener::onKernelException()</font> | 0
<font color="red">Symfony\Bundle\SwiftmailerBundle\EventListener\EmailSenderListener::onException()</font> | 0
<font color="red">Symfony\Component\HttpKernel\EventListener\RouterListener::onKernelException()</font> | -64
<font color="red">ApiPlatform\Core\EventListener\ExceptionListener::onKernelException()</font> | -96
<font color="red">Symfony\Component\HttpKernel\EventListener\ExceptionListener::onKernelException()</font> | -128

---

# Possible solutions ?

<img style="margin-top: -70px" src="img/twig.png" width="100%" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=yaml&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=twig%253A%250A%2520%2520exception_controller%253A%2520%27AppBundle%255CController%255CExceptionController%27%2520%2520" />
<img style="margin-top: -90px" src="img/controller.png" width="100%" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=text%2Fx-php&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=namespace%2520AppBundle%255CController%253B%250A%250Aclass%2520ExceptionController%250A%257B%250A%2520%2520public%2520function%2520__invoke%28Request%2520%2524request%252C%2520FlattenException%2520%2524exception%252C%2520DebugLoggerInterface%2520%2524logger%2520%253D%2520null%29%250A%2520%2520%257B%250A%2520%2520%2520%2520%252F%252F...%250A%2520%2520%257D%2520%2520%250A%257D%2520%2520" />

---

<br />
<br />
<br />
<br />
<br />
<br />
# ... but there's still no logs ...

---

# Monolog configuration on Meyclub

<img style="margin-top: -120px" src="img/monolog.png" width="100%" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=yaml&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=monolog%253A%250A%2520%2520channels%253A%2520%255B%27bpm_manager%27%255D%250A%2520%2520handlers%253A%250A%2520%2520%2520%2520main%253A%250A%2520%2520%2520%2520%2520%2520type%253A%2520stream%250A%2520%2520%2520%2520%2520%2520path%253A%2520%2522%2525kernel.logs_dir%2525%252F%2525kernel.environment%2525.log%2522%250A%2520%2520%2520%2520P50_catalog_broadcast%253A%250A%2520%2520%2520%2520%2520%2520type%253A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520buffer%250A%2520%2520%2520%2520%2520%2520channels%253A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%255B%27app%27%255D%250A%2520%2520%2520%2520%2520%2520action_level%253A%2520%2520%2520%2520%2520%2520%2520debug%250A%2520%2520%2520%2520%2520%2520handler%253A%2520main%250A%2520%2520%2520%2520meyclub_services_info%253A%250A%2520%2520%2520%2520%2520%2520%2523...%250A%2520%2520%2520%2520%2523...%2520%2520" />

---

# Monolog configuration in Symfony recipe

<img width="100%" src="img/recipe.png" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=yaml&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=monolog%253A%250A%2520%2520handlers%253A%250A%2520%2520%2520%2520main%253A%250A%2520%2520%2520%2520%2520%2520type%253A%2520stream%250A%2520%2520%2520%2520%2520%2520path%253A%2520%2522%2525kernel.logs_dir%2525%252F%2525kernel.environment%2525.log%2522%250A%2520%2520%2520%2520%2520%2520level%253A%2520debug%250A%2520%2520%2520%2520%2520%2520channels%253A%2520%255B%2522%21event%2522%255D" /> 

---

# Log efficiently

Don't do:

<img src="img/badlog.png" style="width: 100%; margin-top: -100px; margin-bottom: -100px" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=text%2Fx-php&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=try%2520%257B%250A%2520%2520%2524beneficiaries%2520%253D%2520%2524this-%253Eclient-%253EgetBeneficiaries%28%29%253B%250A%257D%2520catch%2520%28%255CException%2520%2524e%29%2520%257B%250A%2520%2520%2524logger-%253Eerror%28%27Error%2520BPM%253A%2520%27.%2524e-%253EgetMessage%28%29%29%253B%250A%2520%2520%250A%2520%2520throw%2520new%2520%255CRuntimeException%28%27Error%2520calling%2520BPM%27%29%253B%250A%257D%2520%2520" />  
* Log doesn't bring extra information
* throwing a new exception leads to lose the original exception line

---

# Keep the original exception

<br /><br />
* Don't catch at all, let the exception bubble  
<br />
* Or rethrow the original exception (original line is kept)  
<br />
* Or pass the original exception as previous:  
<br />
`throw new \RuntimeException('Error calling BPM', 0, $e);`
  
---

# Get more tracing

<br /><br />
* Add debug/info logs along your code **before the exception is thrown**  
(you can use *finger-crossed* handler type in production to minimize the log files size)
<br /><br /><br />  
* or/and add the exception trace:  
`include_stacktraces: true` in Monolog configuration

---

# Leverage context and psr-3 format

<br />
<img src="img/psr3.png" width="100%" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=text%2Fx-php&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=%2524logger-%253Ewarning%28%250A%2520%2520%27Can%255C%27t%2520retrieve%2520beneficiary%2520%257BbeneficiaryId%257D%2520from%2520BPM.%2520Using%2520data%2520from%2520database%2520instead.%27%252C%250A%2520%2520%255B%250A%2520%2520%2520%2520%27beneficiaryId%27%2520%253D%253E%2520%2524beneficiaryId%252C%250A%2520%2520%2520%2520%27iri%27%2520%253D%253E%2520%2524iri%252C%250A%2520%2520%2520%2520%252F*%2520Other%2520useful%2520info%2520*%252F%252C%250A%2520%2520%255D%252C%250A%29%253B" />

---

# Multiple channels can be used

<img src="img/channels.png" style="margin-top: -90px" width="100%" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=yaml&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=monolog%253A%250A%2520%2520channels%253A%2520%255B%27bpm%27%255D%2520%2520%2523%2520%27monolog.logger.bpm%27%2520service%2520created" />  
<div style="margin-top: -110px; text-align: center">OR</div>
<img src="img/tag.png" style="margin-top: -110px" width="100%" alt="https://carbon.now.sh/?bg=rgba%28243%2C243%2C243%2C0%29&t=a11y-dark&wt=none&l=yaml&ds=true&dsyoff=10px&dsblur=26px&wc=false&wa=true&pv=56px&ph=56px&ln=true&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=2x&wm=false&code=services%253A%250A%2520%2520AppBundle%255CClient%255CBPMClient%253A%250A%2520%2520%2520%2520arguments%253A%2520%255B%27%2540logger%27%255D%250A%2520%2520%2520%2520tags%253A%250A%2520%2520%2520%2520%2520%2520-%2520%257B%2520name%253A%2520monolog.logger%252C%2520channel%253A%2520bpm%257D" />

---

# Look at Monolog documentation

<br /><br />
* **Handlers:** where and how logs are sent

* **Processors:** add extra information to logs

* **Formatters:** logs format

---

<br /><br />
<br /><br />
<br /><br />
# Questions ?