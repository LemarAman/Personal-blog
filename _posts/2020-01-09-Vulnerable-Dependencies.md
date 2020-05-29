---

classes: wide
title: <center>vulnerable dependencies</center>
excerpt: 
---

![](/assets/pics/gears.jpg)


Building modern software incorporates tons of code. We want sophisticated systems with great functionality and incorporating open source packages helps create them. Unfortunately in doing so, if a package that is implemented has itself a vulnerability, **we're introducing a vulnerability to potentially the entire system through that dependency.**  A good example of this is Struts, a framework for Java, which was the library responsible for [the Equifax breach](https://www.wired.com/story/equifax-breach-no-excuse/){:target="blank"}, with over  143 million consumers affected.


## The Exploit
{: .text-center}
```java

// ContentTypeHandler Java class in Struts
  class ContentTypeHandler extends Interface {
  ContentTypeHandler() {
    this.hasQualifiedName("org.apache.struts2.rest.handler", "ContentTypeHandler")
  }
}

//'toObject' method
  class ToObjectDeserialzer extends Method {
  ToObjectDeserializer() {
    this.getDeclaringType().getASuperType*() instanceof ContentTypeHandler and
    this.getSignature = "toObject(java.io.Reader,lang.Object)"
  }
}

```
**Sample code used to detect vulnerability**


## **[Pentest credits to Riyaz Walikar](https://twitter.com/riyazwalikar){:target="blank"}**
{: .text-center}

Triggered by sending a malicious XML POST payload with the “Content-Type” header set to “application/xml”.

<script src="https://gist.github.com/riyazwalikar/473054c92463f24dfa763d263d95fca5.js"></script>


Equipped with adequate payload the vulnerability allows code execution on the server. Code executed doesn't block the parent thread as it uses the `java.lang.ProcessBuilder` class. Executing time delay commands dont help.

`(ping -n 20 127.0.0.1, sleep 5 etc.)`

So, send network packets to a self-controlled server. Linux, `wget` or `curl`. Windows, `ping` your server and use `tcpdump` to check for incoming ICMP packets.


The `ping` command in this payload will send 10 ICMP requests to the server running `tcpdump`.

<script src="https://gist.github.com/riyazwalikar/1d8c73bcfe1ddea3d299dd8a8d0c431a.js"></script>

Run the following tcpdump command on your server.

`tcpdump -nni yourinterface icmp`

![Tcpdumpshowing2ICMPecho](/assets/pics/Tcpdumpshowing2ICMPecho.jpg)

So it's established the application is vulnerable, open up calc via shell commands to the server.

<script src="https://gist.github.com/riyazwalikar/ae4e235d1cbe0b3eb9bf9d7b37c31450.js"></script>

![Calc](/assets/pics/processexplorer.jpg){: .align-center}
<center>Java process popped calc. voilà.</center>




## Richer Targets
{: .text-center}
Be cognizant of dependencies, don't assume all supporting software products are flawless. Even if our system isn't as attractive to attacks, we become a target by using a vulnerable framework since **every organization using that framework is going to be vulnerable**. Of course, the downside to an open source package is that anyone can read/reverse engineer it.

## It's not all rain, my friend.
{: .text-center}
In any case, tools are available to check for vulnerable dependencies like [OWASP dependency check](https://www.owasp.org/index.php/OWASP_Dependency_Check){:target="blank"}, [npm audit](https://blog.npmjs.org/post/173719309445/npm-audit-identify-and-fix-insecure){:target="blank"}, [Retire.js](https://retirejs.github.io/retire.js/){:target="blank"}, and more. More importantly, [those dependencies should be updated and vetted on a consistent basis](https://blogs.apache.org/foundation/entry/apache-struts-statement-on-equifax){:target="blank"}.
