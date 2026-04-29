![](images/978-1-4842-9833-6_CoverFigure.jpg)

ISBN 978-1-4842-9832-9e-ISBN 978-1-4842-9833-6 [https://doi.org/10.1007/978-1-4842-9833-6](https://doi.org/10.1007/978-1-4842-9833-6) © Joseph B. Ottinger and Andrew Lombardi 2024 This work is subject to copyright. All rights are solely and exclusively licensed by the Publisher, whether the whole or part of the material is concerned, specifically the rights of translation, reprinting, reuse of illustrations, recitation, broadcasting, reproduction on microfilms or in any other physical way, and transmission or information storage and retrieval, electronic adaptation, computer software, or by similar or dissimilar methodology now known or hereafter developed. The use of general descriptive names, registered names, trademarks, service marks, etc. in this publication does not imply, even in the absence of a specific statement, that such names are exempt from the relevant protective laws and regulations and therefore free for general use. The publisher, the authors, and the editors are safe to assume that the advice and information in this book are believed to be true and accurate at the date of publication. Neither the publisher nor the authors or the editors give a warranty, expressed or implied, with respect to the material contained herein or for any errors or omissions that may have been made. The publisher remains neutral with regard to jurisdictional claims in published maps and institutional affiliations.

This Apress imprint is published by the registered company APress Media, LLC, part of Springer Nature.

The registered company address is: 1 New York Plaza, New York, NY 10004, U.S.A.

*For my wife and children, and for all the rabbits that stayed out of my line of sight so I could finish writing my parts.*

*For the four that make life whole and are always putting a smile on my face.*

Acknowledgments

Joseph Ottinger would like to think the unthinkable – oh, wait, the itheberg already did that. Instead, he’d like to thank the concept of referring to oneself in the third person, as well as whoever thought to add frets to guitars – and remove them – long run-on sentences, whoever invented the footnote, beanbags, and Snarky Puppy, as well as friends like Andrew and Tracy Snell and Darren Thornton and eden Hudson and Todd Smith and – most importantly and seriously – his family for putting up with him in the first place, and much more for putting up with him during the book-writing process. I love all of you more than I know how to express. Really. I’ve tried lots of things out.

About the Authors About the Technical Reviewer

# 1. History and Justification

Spring is an application framework providing dependency injection features for the Java Virtual Machine – features that enable testability, reliability, and flexibility to application developers. It changed how Java is developed, and here’s how and why.

## Setting the Stage for Better Development

Spring, according to Wikipedia, is one of the four temperate seasons, following winter and…​ no, no, this isn’t a book about weather. Let’s try again.

A spring is a mechanical device that stores kinetic energy, releasing it when tension stored in the…​ no, that doesn’t sound right either. This is supposed to be a book about programming.

One more try:

Spring, contextually speaking, is an application framework for the JVM. It uses a concept called “dependency injection” – described later in this chapter, we promise – as a general model for development, and it has changed how Java programs are written – even if the programs in question avoid the use of Spring itself.

Spring is also an *ecosystem* – a galaxy of extensions and modules with a library called spring-core at its center. The extensions and modules add functionality and features to cover many, many possible use cases. In general, people don’t refer to spring-core much; instead they just use “Spring” and expect others to understand that the ecosystem as a whole is being referred to.

The modules are, as mentioned, pretty extensive. Chances are, if you need to work with something in Java, there’s a Spring module for it…​ somewhere.

Spring came from somewhere, first, of course; that “somewhere” was actually “someone,” Rod Johnson.

In 2002, Rod wrote a book called *Expert One-on-One J2EE Design and Development*.^([¹](#fn1)) It was written as a reaction to certain aspects of J2EE, which was the predominant framework for enterprise application design in Java, and as such represented a bit of a revolution – or a rebirth of joy and ease, if you will – for Java programming. In it, Rod included some example code that Juergen Hoeller and others asked him to make open source, which found its way into a project, called “Spring,” which itself later found its way into *another* book, *Expert One-on-One J2EE Development without EJB*.^([²](#fn2)) *This* is the book that really got things moving.^([³](#fn3))

To understand the importance of J2EE^([⁴](#fn4)) (and Spring, really), we need to think about how programming was evolving at the time. “Real applications” tended to still be run on minicomputers or mainframes; managed resources were still king, and applications that managed to be relevant running on personal computers were surprising. Everyone knew of Lotus 1-2-3,^([⁵](#fn5)) of course, and dBase was a surprisingly functional database application, and sure, it was fine for students and others of similar low breeding to write documents on personal computers…​ but to actually run *real applications* meant running on the corporate mainframe.

Mainframe development would be horribly constraining for developers today; the concept of scheduling jobs to run – even as compilations, or tests – is foreign. We’re used to the power of being able to spin up a PostgreSQL^([⁶](#fn6)) instance in Docker,^([⁷](#fn7)) for instance, to which we connect our app for a quick integration test. Today, we think nothing of running an application for two seconds just to see if a function returns the right value…​ but in the mainframe days, that represented quite a considerable investment of time and money and disk space. You typically had a production database and one test database, both of which were valued more than gold, if only because they were very difficult to replace.

In that kind of environment, the system administrator was king. The sysadmin told you where he or she had located the databases, and how to connect to them, and – more importantly – how much of the system resources were allocated to your program. Exceed those resources, and your program would be terminated, to prevent impacting every other program negatively.

Enter Java, which could be compiled on a wimpy local desktop. Suddenly, the cost of development for applications meant to be run on the mainframe dropped, because no longer was the mainframe as important for anything except running the application. What’s more, Java was incredibly strong enough to bring the mainframe ethos (“Real programs run here”) into smaller machines like minicomputers and even the personal computer.

J2EE was created as an application framework that provided a set of standards for enterprise development. It had a presentation layer and a computation layer, among others, and incorporated formal standards for communications between those layers. It also used the concept of a “container” into which one deployed J2EE modules – like web modules and other such components – and the container represented a point of control for system administrators. An administrator could control how many connections a given container could make to a specific database, for example.

The most important word in the prior paragraph was “formal.” The next most important word was “control,” as in “what the container administrator had.”

Java had a concept that it called the “Java bean,” which was meant to be a deployable unit of functionality; you might have a “bean” that followed certain standards and represented some kind of relevant operation; you might have a bean that, for example, served to calculate the points along a curve when provided sample data, or a bean that performed matrix computations.

Likewise, J2EE had the concept of an “Enterprise Java Bean,” or “EJB,” which was a Java Bean with additional restrictions and capabilities, mostly centering around transactions and the ability to be called remotely. An EJB, when called, represented an interprocess communication, even if it was co-located with its caller.

In practice, this meant that every J2EE programmer was enabled right out of the gate to design working distributed architectures. This is not a small accomplishment at all; distributed architectures are difficult to get right, and J2EE made invoking processes on separate machines trivial.

There’s a cost to all of this, of course.

The nature of EJB, especially early in J2EE’s lifecycle, meant that when every call could be remote, every call had to be treated as if it were a remote call, with a specific deployment and development cycle (early on, you had a separate packaging compiler for EJB, called `ejbc`, to build the supporting classes for EJBs to run), and because of the requirement that EJBs be considered to be remote even when they weren’t, EJBs were slow.

EJBs were slow to develop, slow to test, slow to deploy, slow to invoke. And they were everywhere.

*J2EE Development without EJB* was written as a logical reaction to the entrenched mindset. Rod Johnson and Juergen Hoeller looked at how EJBs were being used (and misused), and their actual cost to developer productivity – which was really quite high, for the value they were providing. To use an EJB, not only did you have to go through a special development and deployment cycle, but then you had to look them up at runtime, and handle the exceptions when they weren’t available.​

```
// get the JNDI context for the application
Context ctx=new InitialContext();
// Inferencer is the java interface for the EJB
// First, we have to get the "Home" object, which
// can create the EJB for the caller
// The deployer should have set up the name to point to /
// an actual deployed EJB artifact
Object ref=ctx.lookup("java:comp/env/ejb/inferencer");
InferencerHome home=
(InferencerHome) PortableRemoteObject.narrow(
ref,
InferencerHome.class
);
// now, we use the home object to actually get the EJB
Inferencer engine=home.create();
// presumably, generate an inference using inputs...
engine.generate(input);
Listing 1-1
Looking up an EJB in the olden days
```

In Listing [1-1](#471370_2_En_1_Chapter.xhtml#PC1), not only did you have to write the code to grab a component that can create the EJB from somewhere else,^([⁸](#fn8)) but you had to *configure* that reference, and then you had to go through a set of semantics to get something that can actually invoke your EJB. But why go through the development and remote semantics when they were rarely needed and desired? Those semantics were rarely important despite how much they catastrophically affected your code; at a Java Symposium in the early 2000s, attendees were asked how many used EJBs, and most hands went up; the next question was how many of those EJBs were actually invoked remotely. The answer: anecdotally, about two percent.^([⁹](#fn9))

*That* mindset is what drove *J2EE Development without EJB* a desire to return freedom to the coders, to provide support for programming with necessary complexity. If you needed to call a remote service, well, nothing stops you from doing that, but experience with actual coding showed that it was rarely necessary and, instead, served as a barrier to just getting things done.

Rod’s primary insight was that EJBs might be powerful, but they were a little like strapping a V6 engine into a child’s bicycle. Most developers didn’t need them, or want them; EJB just happened to be the language feature *du jour*, and as a result development was slowed down, deployment was more difficult,^([^(10)](#fn10)) testing was difficult, and configuration was arcane. Most developers didn’t use the “correct techniques” for looking up EJBs because they didn’t want to take the time to configure the EJB container. Configuring the container meant that reproducing conditions was more difficult, as well.

It was a mess. It was a productive mess, at times; after all, the technology was created as a response to actual user needs. But even if developers were able to use it to get real things done well, it created a lot of burden on those same developers, and the practices in place weren’t making it better. The technology didn’t even really *allow* better practices.

## Rod and Juergen Change the (Java) World

In *J2EE Development without EJB*, Rod (and Juergen, but I’ll shorten it to Rod and hope Juergen takes no offense!) mapped out a process for J2EE development that focused on six themes:

*   Simplicity

*   Productivity

*   The fundamental importance of object orientation

*   The primacy of business requirements

*   The importance of empirical process

*   The importance of testability^([^(11)](#fn11))

In addition to these themes being obviously fairly important, it’s important to recognize that these themes were *secondary* concerns for typical J2EE development.

For example, EJBs were meant to represent *generic* access to remote services, so developing an EJB meant creating something that could be invoked via complex protocols, along with handling fairly complex transactional concerns. If that’s what you *actually needed*, then EJB was fine…​ but most developers *didn’t* need to worry about CORBA,^([^(12)](#fn12)) and their transactional needs tended to be very simple. EJB represented complexity that the *applications* didn’t need.

If the applications didn’t need the complexity, but programmers had to be aware of it, then the complexity represented a direct and unavoidable sunk cost for development.

Further, EJBs tended to be non-Java-like in how they worked. They represented endpoints of functionality, not self-contained objects. Designing an EJB meant stepping outside of the very paradigms that made Java useful in the first place.

Spring was designed to change enterprise development such that it prioritized, well, all six of those themes. It was designed to be simple (and thus productive and by definition focused on actual business requirements), object oriented, and **testable**, a feature whose value is difficult to overvalue.^([^(13)](#fn13))

*J2EE Development without EJB* then went on a tear, demonstrating a self-contained application context that, despite the use of XML, was far easier for developers to work with than J2EE’s striated developer/deployer model. It returned power to the developers, and along the way provided easy ways to create objects that were used solely for testing – a feature EJB provided but only with great difficulty – and ways to change how the application objects acquired resources such that it made sense to most developers.

Note

XML is a fantastic specification, but it’s also a *formal* specification, and it’s sometimes difficult to get right. Other markup languages like JSON and YAML have largely supplanted it in common usage, because they’re so much easier to write, and the tradeoffs in reliability are generally seen as being worth it.

After all, if you need a `Widget` in your `FooFram`, one doesn’t normally think that in ordinary Java the `FooFram` should ask a remote service to provide a `Widget` somehow; normally one has a `setWidget(Widget widget)` method in the `FooFram` object.

Spring went a long way to making Java look like, well, Java again. Objective adoption rates are unclear, but it’s safe to say that Java developers were a lot more excited about developing with Spring than with traditional J2EE. However, Spring was not a formal part of the development landscape of J2EE.

Spring had an almost subversive approach; one used J2EE, and Spring was used alongside J2EE to actually get things done. It was a curious, but effective, model. Spring was becoming a standard dependency for many; it was standard idiom without being an actual part of any standards.

Better approaches were possible, with the creation of community processes such that Java application design wasn’t decided upon by cloistered architects in San Jose.

## The Lever: Dependency Injection

The core design pattern that Spring introduced to the wider Java world is currently called “Dependency Injection.”^([^(14)](#fn14)) With the “traditional J2EE approach,” objects that needed a resource deterministically acquired that resource, with a specific name. (You could, I suppose, *look up* the name you wanted, but then you have to look up the *specific name*, which isn’t much better.) Control over which resource was provided was given to the J2EE application server’s administrator.

For a developer to use J2EE, he or she had to assume the administrator role and configure the name service somehow. Name services, to borrow a phrase from Barbie, are hard; you have to make sure you have qualifying types (a low barrier, honestly, because most of the things you’d want a name service for would qualify, by design), you have to know how to make sure the objects are constructed properly, you have to know how to set up the names and resources *for your specific application server*, and you then have to know how to set up the application-specific redirected names – again, for each application server, because while most of them were similar, they weren’t quite the same, either.

It’s a very powerful idea, but it’s more than most developers wanted to do – and many of them did only the minimum necessary, and didn’t bother setting up local names for resources. It also made testing very difficult, because you couldn’t just use a `Widget` in a test, you had to **deploy** a `Widget`, set up a name for it, and have your object *look up* the `Widget` in the test. It’s doable, certainly – programmers are often inventive and driven – but it’s also a pain.

With dependency injection, however, the objects that want a `Widget` no longer have to *look up* a `Widget`. Now, when an object that needs a `Widget` is constructed, the dependency injection framework (in this book, Spring!) *provides* a `Widget` immediately.^([^(15)](#fn15)) The dependency injection framework gathers information about `Widget` in a few different ways: the simplest way (and the earliest) was simply to create an XML file that had a reference to a `Widget` and the classes that needed it. In later versions of Java and Spring, the application code can be *searched* for possible instances of injectable resources, making configuration of simple applications very lightweight.

When programmers designed code for J2EE, they indebted themselves to the J2EE mindset; everything one did with J2EE involved a lot of, well, J2EE. You couldn’t test code that used an EJB without involving yourself in JNDI configuration – a simple test would have to start up a JNDI container (and, possibly, an EJB container), along with populating the JNDI dataset and making sure the EJBs could be resolved. None of that is actually **testing the service** – it’s all gruntwork required just to get to the point where you’re *able* to test the service. It implies enduring startup time for the containers (which is time wasted even in the best of circumstances) and time spent on testing the configuration, and many containers had a long startup time.

But if the *goal* is to test something, pragmatism would suggest that configuration of containers and startup times is *all* wasted.

With Dependency Injection, you simple create a Java class that reflects the behavior you want, and supply it to the class you’re testing. Your test doesn’t even have to depend on Spring or anything else. As an example, let’s consider two forms of “Hello, World”^([^(16)](#fn16)) – the EJB version (as it would exist around 2005^([^(17)](#fn17))) and a version that represents the class structure that’s ready for dependency injection.

### J2EE Hello World, As of 2005

An EJB back in the first days of Spring needed at least three source files: an interface that described the contract for the EJB, a class that provided the actual executable code for the EJB, and a “home object” that was used to manage the EJB’s lifecycle (operations that took place when the EJB was created, or destroyed, among other lifecycle stages.) We’re not even going to go into the process of developing the actual deployment itself – this is *just* the Java class structure required at *runtime*. Deployment required **at least** one XML descriptor, with each container often requiring yet another container-specific XML file to connect resources inside the container’s JNDI tree. It was maddening, even in the *best* case…​ and the best cases were rather rare.^([^(18)](#fn18))

```
interface HelloWorldEJB extends EJBObject {
String sayHello(String name)
throws RemoteException;
}
Listing 1-2
The EJB interface
```

In our `HelloWorldEJB`, we’re simply saying that we have one method that the EJB provides: `sayHello()`. Since this is an EJB, we have to anticipate the possibility of errors occurring due to the nature of EJBs being remoteable. It’s worth pointing out that even the *interface* of our EJB is affected by, well, being an EJB; we have to include the `EJBObject` interface. We can’t even develop this much of our EJB without being aware of the fact that it’s an EJB. This is *Not Good*; it represents technical debt to J2EE and we haven’t even done anything yet.

```
public interface HelloWorldEJBHome  extends EJBHome {
HelloWorldEJB create()
throws RemoteException, CreateException;
}
Listing 1-3
The EJB home object
```

The `HelloWorldEJBHome` interface specifies how a concrete instance of `HelloWorldEJB` can be created. In our case, our interface demonstrates a stateless service (we don’t create it with a default target to greet, for example), so there’s nothing special for the creation process, yet we still need to create this interface.

```
class HelloWorldEJBImplementation implements SessionBean {
public String sayHello(String name) {
if(name==null) {
name="world";
}
return "hello, "+name+".";
}
}
Listing 1-4
The EJB implementation
```

At least, we have meaningful code! It’s a simple method; if the argument is `null`, default to saying “Hello, World,” but otherwise greet the name provided to the method. It’s worth noting here that it doesn’t actually implement `HelloWorldEJB` – evidence of an antipattern, indeed, because we can’t ensure that it fulfills the correct specification to serve as a valid concrete instance of the `HelloWorldEJB`.

```
...
try {
Context ctx=new InitialContext();
Object ref=ctx.lookup("java:comp/env/ejb/hello");
HelloWorldEJBHome home= (HelloWorldEJBHome)
PortableRemoteObject.narrow(
ref, HelloWorldEJBHome.class);
HelloWorldEJB greeter=home.create();
System.out.println(greeter.sayHello("Andrew"));
} catch(Throwable throwable) {
/*
bubble up to the next level of the application
for all exception conditions, since we can
handle none of them *here* - note that we're
handling CreateException, RemoteException,
and NamingException with one catch block
*/
throw new RuntimeException(
throwable.getMessage(), throwable);
}
Listing 1-5
The EJB client code
```

This is what the *caller* of our EJB would have to do as a bare minimum. This doesn’t *test* our EJB; it only *calls* it. If we wanted to test our EJB out, we’d have to construct something similar to this, **deploy** our EJB, and add code to pass it known inputs and compare to expected outputs.^([^(19)](#fn19))

I think it’s safe to say that this sounds like a burden…​ but this is what every J2EE developer had to endure.

### Spring’s Better Vision of Hello World

Now let’s take a look at what Rod Johnson encouraged coders to create. We’ll have our contract (the interface), an implementation of that interface, and show client code that would use our `HelloWorldBean` – and then walk a little bit through some of the many reasons this implementation is better.

```
package com.bsg6.hello;
interface HelloWorld
{
String sayHello(String name);
}
Listing 1-6
The HelloWorld interface
```

Note how clean this interface is: it does nothing more than our EJB’s interface does (and in fact `HelloWorldEJB` could extend this interface, adding only the `EJBObject` interface as decoration). This class has no technical debt *anywhere* – not to Spring, not to J2EE.

```
package com.bsg6.hello;
public class HelloWorldImplementation implements HelloWorld {
public String sayHello(String name)
{
if(name==null) {
name="world";
}
return "Hello, "+name+".";
}
}
Listing 1-7
The HelloWorld implementation
```

We see the same pattern with `HelloWorldImplementation` as we do with `HelloWorld`. It depends on the `HelloWorld` interface, of course, but that’s all. We have nothing in the class that’s not focused on actually fulfilling the contract of the `HelloWorld` interface. The `HelloWorldEJBImplementation` gets **close** to that idea, but has to implement `SessionBean` – and *is not supposed to* implement the interface that represents its contract. We’ve only displayed two example classes and we’ve got far less technical debt, and our classes conform to idiomatic Java – even the idioms of the day back in 2004 or so, when J2EE was king and dinosaurs ruled the Earth.

```
package com.bsg6.client;
public class Greeter
{
HelloWorld generator;
void setGenerator(HelloWorld generator)
{
this.generator=generator;
}
public void displayHello(String name)
{
System.out.println(generator.sayHello(name));
}
}
Listing 1-8
The HelloWorld client
```

Here, you see the `generator` reference points to a `HelloWorld` implementation. `Greeter` doesn’t instantiate a `HelloWorld` (although it could if it wanted); however, the idea here is that something *external* to `Greeter` provides a `HelloWorld`, and a `Greeter` just uses it. It doesn’t have to have any acquisition code, has no lookup code, it’s just an attribute on a regular Java class.

`HelloBean` is too simple to actually *need* other implementations…​ but suppose we needed to wrap some other functionality into an implementation for testing or other diagnosis?^([^(20)](#fn20)) We could create the functionality and just use the new implementation for testing; our `Greeter` class doesn’t have to change, and we don’t have to do anything special to switch to our new implementation besides using it instead.

*This* is dependency injection; we’re injecting the `HelloWorld` dependency when we need it.

Spring not only recognized the power of this pattern, but the Spring framework provided tools to use it; you would define an XML file that named a `helloworld` bean (of type `com.bsg6.hello.HelloWorldImplementation`), as well as a `Greeter` instance, and would inject the `helloworld` reference into the `Greeter` automatically, with no source-level dependencies on Spring whatsoever.

Spring added some technical debt, if you used certain aspects of the framework: you could have lifecycle hooks in your beans, for example, if you wanted them. (You could still configure lifecycle stages via XML, too, and avoid technical debt in the source files.) But Spring was, and is, largely pragmatic; you, as a developer, had the power to choose how much debt you wanted to incur, as opposed to J2EE’s mandated and often crushing technical debt.

## Spring Breaks Free and Fixes Java EE

Sun, the original creators and maintainers of Java, created the Java Community Process back in 1998, but opened up membership a few years later. Spring, being an influential aspect *of* the community, participated in the creation of Java EE 6, with J2EE having been renamed to something less confusing.^([^(21)](#fn21)) With Spring’s participation, Java EE 6 gained a new specification, “Context and Dependency Injection.” Standard annotations were added to support CDI and features related to it, and the world rejoiced publicly and privately.

The funny thing is, as Spring became more influential on Java EE, Java EE itself became *less* influential. Spring never stopped innovating; now, you can deploy microcontainers using Spring, leveraging Jakarta EE API specifications, without bothering with Jakarta EE containers as such at all. You can still use a Jakarta EE application server, of course, but the need to do so has lessened.

That represents a loss of control by the “old guard,” the system administrators who still think that 2005 represented the “good old days”^([^(22)](#fn22)) – but it means that programmers of today have more power and flexibility than they’ve ever had, and in Java it’s fair to say they owe a debt of gratitude to the Spring Framework for it.

## Spring 6 and Modern Java

Java’s added a lot of really nice functionality in the last few years. After Oracle spun Java EE off and into Eclipse (giving us “Jakarta EE” instead of “Java EE”), the JVM itself was largely democratized. It’s still mostly controlled by Oracle, but the wider community has had more input into Java than it’s had since the early 2000s, and Java’s been evolving rapidly as a result. We have new `record` types (intended to be immutable data structures) and modules, and we’re likely to see new multithreading models soon as well.

Spring’s not kept still with all of this change. Spring 6 acknowledges the module infrastructure and supports it directly, and much of the Spring ecosystem has also shifted to the Jakarta EE naming structure (`jakarta` instead of `javax`, as a package name.) We’re going to look at some of this as we go through the book.

Warning

The shift from `javax` to `jakarta` for Jakarta EE is not seamless. The Java ecosystem is quite large, after all, and there are a lot of interdependencies in projects; some projects may still use the old `javax` packages where one would prefer they use `jakarta` instead. The only solutions for that are time and elbow grease, unfortunately. We’ll try to be careful and point out any issues with things like this as we proceed, but be aware that in real life, few things are as perfect as we’d like them to be.

## Next Steps

In our next chapter, we’re going to take a look at “Hello, World” again – and go a lot deeper into the strengths of dependency injection, and Spring in particular.

Footnotes [1](#471370_2_En_1_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_1_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_1_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_1_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_1_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_1_Chapter.xhtml#Fn6_source)   [7](#471370_2_En_1_Chapter.xhtml#Fn7_source)   [8](#471370_2_En_1_Chapter.xhtml#Fn8_source)   [9](#471370_2_En_1_Chapter.xhtml#Fn9_source)   [10](#471370_2_En_1_Chapter.xhtml#Fn10_source)   [11](#471370_2_En_1_Chapter.xhtml#Fn11_source)   [12](#471370_2_En_1_Chapter.xhtml#Fn12_source)   [13](#471370_2_En_1_Chapter.xhtml#Fn13_source)   [14](#471370_2_En_1_Chapter.xhtml#Fn14_source)   [15](#471370_2_En_1_Chapter.xhtml#Fn15_source)   [16](#471370_2_En_1_Chapter.xhtml#Fn16_source)   [17](#471370_2_En_1_Chapter.xhtml#Fn17_source)   [18](#471370_2_En_1_Chapter.xhtml#Fn18_source)   [19](#471370_2_En_1_Chapter.xhtml#Fn19_source)   [20](#471370_2_En_1_Chapter.xhtml#Fn20_source)   [21](#471370_2_En_1_Chapter.xhtml#Fn21_source)   [22](#471370_2_En_1_Chapter.xhtml#Fn22_source)  

# 2. Hello, World!

It seems appropriate to start learning about Spring by building out our first example as a simple “Hello, World!” application. In this chapter, we’re going to take a look at the tools and libraries we’re going to rely on – in particular, Maven and TestNG^([^(23)](#fn23)) – and build a simple application to demonstrate how we validate that our application works as designed. Then – at last – we’ll leverage Spring in our application. This way, we’ll establish the knowledge we’ll need to make sense of the rest of the book.

## A Simple Application

Our goal in this chapter will be to take another look at the “Hello, World” application from Chapter [1](#471370_2_En_1_Chapter.xhtml), except this time we’re going to examine tooling and the mindset that drove the design.

First, why “Hello, World?” Traditionally, programming languages (and frameworks) use this application because it’s very **simple** – and that means we have room to focus on things like overall syntax, how to design, how to build, and how to run the app. “Hello, World” allows us to do nothing but explore the lifecycle of a “full program.”^([^(24)](#fn24))

Spring is going to be incredibly heavy for such a simple application, and we aren’t even going to use it for the first iteration, but that allows us to focus on tooling and process, including tests.^([^(25)](#fn25)) We’ll wire everything together with Spring as we continue – even though it will look a bit silly – because that will demonstrate how we can move configuration around as needed, a feature that’s necessary in real-world scenarios.

### Suffering-Oriented Programming

Throughout the entire book, we’re going to do simple things over and over (and over) again – you’ll probably be sick of “Hello, World” as we use it multiple times in this chapter. This is partly because of the familiarity of “Hello, World” – it has simple, knowable inputs and simple, knowable outputs and therefore allows us to focus on the bits that **surround** the basic process rather than the process of saying “Hello” itself.

A bit of history: “Hello, World” was introduced to the world in Kernighan and Ritchie’s *The C Programming Language*.^([^(26)](#fn26)) This is an iconic programming book, and it’s the most accessible of the classic programming books such as *The Art of Computer Programming* (Knuth) or *Design Patterns* (Gamma, Helm, Johnson, Vlissides). The other books are accessible, too – they’re classics for a reason – but *The C Programming Language* stands alone due to its directness, simple and clear presentation of its subject matter, and, well, the time of publication.

“Hello, World” is used as a “sanity check” – a very simple program to make sure the compiler and runtime environment are working, as well as illustrating a general “bare minimum” to get a program running that actually generates output.

Many programmers add functionality to it to illustrate additional features; the Free Software Foundation’s “Hello, World” (found at [`www.gnu.org/software/hello/`](http://www.gnu.org/software/hello/)) is *184* lines of code, illustrating command line parsing and licensing and other such issues of concern for the FSF – and the downloadable archive is nearly 800K!

Following this time-honored tradition – are there any other kinds of valid traditions? - we’ll be following suit in this book, generally using “Hello, World” to make sure our library is doing what it’s supposed to, before adding functionality that actually exercises the chapter’s subject in question.

We also follow the concept of “suffering-oriented programming,” a term coined by Nathan Marz on [`http://nathanmarz.com/blog/suffering-oriented-programming.html`](http://nathanmarz.com/blog/suffering-oriented-programming.html). The concept behind this is simple: “First make it possible. Then make it beautiful. Then make it fast.”

This mindset says that **working** code is superior to any other kind of code; you should first focus on making your application **run**. Working code is superior to non-working code, in every case, no matter how pretty the non-working code may be.

The next most important aspect of your code is to make it **understandable** – or “beautiful,” or perhaps “simple.” Once you’ve solved your problem somehow (you’ve made it work), you are likely to understand your problem more completely than when you first started writing code. Now that you’ve got working code, you can try to simplify your solution such that it’s more clear and maintainable. (Spring helps a lot with this, by the way.) Along the way, if you find your “more beautiful” version doesn’t actually work, well…​ you’ve still got your baseline code, the code that existed before you started beautifying, so you can refine your attempts to clean up your code until it, too, works.

Lastly, you make your code **fast**. By this point you’ve presumably got working code written with simple abstractions; now it’s time to focus on where the code actually runs inefficiently, such that it uses resources properly. This is where you’d go through the code with a profiler, looking for places where you allocate memory unnecessarily (a small concern with languages like Java, where short-term memory usage is, to use the technical term, “fast as blazes”) or where you iterate through data structures more slowly than you could have.

This book focuses heavily on the first two aspects of suffering-oriented programming, because Java’s actually fairly spectacular at making simple code run well, but it’s worth keeping all three concerns in mind.

## Building

With simplicity in mind, we’re going to use an open source build tool called Maven.^([^(27)](#fn27)) Maven is one of the two major build systems popular in Java, along with Gradle ([`https://gradle.org/`](https://gradle.org/)); Maven was chosen for this book despite Gradle having simpler configuration files because Maven’s configuration has the advantage of greater consistency.

Truthfully, Gradle’s a great build system in a lot of ways, and prior editions of this book used it. However, Gradle is also undergoing very active development, which means that it changes constantly; the techniques that worked for Gradle 5 are not appropriate for Gradle 8\. We could have locked down a consistent older version of Gradle for this book, but felt that advising readers to use outmoded techniques was a poor course of action.

If you’d like, you can certainly use Gradle for this book’s source code; the directory structure is wide and flat, and quite suitable for Gradle. The Gradle build structure would even be shorter, in most cases, than the Maven project files. We opted for consistency; nearly anyone who installs Maven, regardless of Maven version, would be able to use and replicate these project files and have a “correct build,” and with Gradle, that’s a lot more difficult to ensure.

Maven uses a conventional directory structure for projects. Basically, there’s a directory for source files called `src` – with typically two directories in it. `src/main` holds the application files – the ones that make up the actual program being built – and `src/test` contains the files that are for testing only. `src/main` and `src/test` themselves have different directories; Java files that are part of the application would go into `src/main/java`, while tests written in Java would be located in `src/test/java`. You might have static resources as part of the build; things meant to be included for delivery would go in `src/main/resources`, where resources meant only for testing would go into `src/test/resources`.

![](images/471370_2_En_2_Chapter/471370_2_En_2_Fig1_HTML.jpg)

A visual representation of a directory structure for a Java project. The top-level directory is labeled s r c, which contains two subdirectories main and test. Both main and test directories further contain two subdirectories java and resources.

Figure 2-1

src directory

You’ll have a lot of opportunities to see these structures over and over again as the book progresses.

First, let’s show how to install Maven for some popular operating systems.^([^(28)](#fn28)) After that, we’ll walk through `pom.xml`, the file that informs Maven about how you want your project built.

### Installing Maven

The best method to install Maven is subjective, but our recommendation is to use a package manager available on your platform.

For MacOS and Linux, you can use the SDKMAN! package manager^([^(29)](#fn29)) and install with:

```
sdk install maven
```

Note

If you don’t have Java installed on your operating system, SDKMAN! can help with that, too: simply run `sdk install java`. Just make sure that the version of Java is at least version 17 or higher. The version used for this book was `17.0.7-tem`.

And for those of you unlucky enough to be using Windows, scoop^([^(30)](#fn30)) is inspired by Homebrew and looks to be pretty decent. There’s also chocolatey.^([^(31)](#fn31))

```
scoop install maven
```

Make sure Maven is executable (the command is `mvn`) and you’re ready to get started.

Note

This book was built with Maven 3.9.3 (and Java 17.0.8, from Eclipse Adoptium). Most recent versions of Maven will work fine, as will any version of Java that’s at least version 17.

### Building the Project

Maven builds by convention, over configuration. Basically, every project is considered to have a template for construction:

*   A validation phase, which makes sure the project has the information it needs to be built

*   Compilation, where the “main source code” of the project is compiled

*   Testing, where the “test source code” is compiled and executed

*   Packaging, where the main source code is prepared for use elsewhere

*   Verification, which runs any integration tests for quality

*   Installation, where the results of the packaging phase are deployed for use locally

*   Deployment, where the results of the packaging phase are deployed for use remotely, by others

A Maven project is represented by an XML file (normally called `pom.xml`) that tells Maven how to tune each of these phases. In general, the compilation and testing phases are the ones that get modified the most, adding dependencies for use in the appropriate phase, although there are certainly options for the other phases as well, and Maven can also **add** phases for projects that need them.

In this book, we’ll mostly be concerned with compilation and testing. We’ll do so by representing the entire book as a single Maven project, but with each chapter having one or more *subprojects*.

Our top-level `pom.xml`, then, will have a centralized configuration for common elements that apply to each chapter, as well as containing references to each chapter as we go.

Since this is Chapter [2](#471370_2_En_2_Chapter.xhtml), we’ll start off with two `pom.xml` files, one for the book as a whole and one for this chapter. Every chapter will follow this same model, only adding a “chapter reference” to the top-level `pom.xml`. Speaking of, let’s take a look at it for this chapter:

```

4.0.0
com.apress
bsg6
1.0
pom

chapter02

UTF-8
17
17
7.8.0
6.1.1
3.1.1
2023.0.1
2.15.0
2.1.214
1.4.8

org.springframework.boot
spring-boot-dependencies
${springBootVersion}
pom
import

org.testng
testng
${testngVersion}

org.springframework
spring-core

org.springframework
spring-context

org.testng
testng
test

org.springframework
spring-test
test

org.apache.maven.plugins
maven-surefire-plugin
3.1.2

org.apache.maven.surefire
surefire-testng
3.1.2

Listing 2-1
/pom.xml, the main project file for the book
```

There are five sections to this `pom.xml`: a project definition section, first, which describes things for the whole book: a `groupId`, an `artifactId`, a project version, and that it’s a multi-module project (as well as a single module, `chapter02`, which will be described as our next file).

After that, there’s a `<properties>` section, which describes values that will be central for the entire project, things like the Java version (`17`), the Spring Framework version, and other centralized values.

Note

This book was written using the versions shown here. However, the world doesn’t stop turning just because some authors are writing a book, so while these versions are current *as this content is written*, more recent versions might be available.

Next, there’s a `<dependencyManagement>` section, which **describes** dependencies but doesn’t alter the classpath for any compilation phases; this allows us to indicate, for example, that if a project should need `TestNG` (a testing framework), it should use a specific version described here. The `spring-boot-dependencies` dependency is important here, because it is actually an *umbrella* dependency, describing *many* Spring dependencies as a single unit. This prevents us from having to chase specific Spring module dependencies, in case their versions aren’t synchronized; we include `spring-boot-dependencies`, and any time we refer to a Spring dependency, it’s loaded from this list of dependencies, no matter what the specific version is.

After `<dependencyManagement>`, there’s an actual `<dependencies>` section, which has dependencies that *every submodule will use*. In this book, that means Spring itself, as well as TestNG. We can specifically set `TestNG` and `spring-test` to be available only at `test` scope, which means they are only available during Maven’s `test` phases, which is what we want.

Lastly, we have a `<build>` section, where we provide configuration for the testing phase itself, where we specify that we want to use TestNG for the test framework, in case there are situations where TestNG isn’t detected by default. (Most projects won’t need this.)

It’s time to take a look at this chapter’s `pom.xml`, to see what most of our projects will look like.

```

4.0.0

com.apress
bsg6
1.0

chapter02
1.0

Listing 2-2
/chapter02/pom.xml
```

This `pom.xml` looks incredibly sparse compared to the `pom.xml` from Listing [2-1](#471370_2_En_2_Chapter.xhtml#PC3). That’s because it’s inheriting everything it needs from Listing [2-1](#471370_2_En_2_Chapter.xhtml#PC3); all this chapter’s build needs to do is refer to the “parent `pom.xml`” (with the `<parent>` tag), and specify its own name and version.

Note

We are going to use a very flat module structure in this book; each chapter will have one (or more) subdirectories in the “parent directory” (where Listing [2-1](#471370_2_En_2_Chapter.xhtml#PC3) is). We could have used nested directories for chapters that contain more than one module, but flatter directory structures are more easily named and described. Thus, in Chapter [5](#471370_2_En_5_Chapter.xhtml), we’ll have three directories named `chapter05-api`, `chapter05-anno`, and `chapter05-xml`, rather than having `chapter05` with `api`, `xml`, and `anno` directories in it.^([^(32)](#fn32))

In the next section, we’ll go into more detail about TestNG and how we’ll be using it throughout the many examples in the pages ahead.

## Testing

The best way to ensure that your program is working as intended is *not* `System.out.println` all over your codebase. It’s to write tests. Tests allow us to write assumptions about what our code does, and have those validated (or not). They help with refactoring and can sometimes be an initial basis for self documentation of the codebase.

As we saw in Listing [2-1](#471370_2_En_2_Chapter.xhtml#PC3), we added TestNG to our configuration for every submodule in our directory structure. So let’s assume before we write any implementation that we have a class called `HelloWorldGreeter` that implements an interface `Greeter` which defines two methods `greet()` and `setPrintStream()`.

Here is our very simple `Greeter` interface.

```
package com.bsg6.chapter02;
import java.io.PrintStream;
public interface Greeter {
void setPrintStream(PrintStream printStream);
void greet();
}
Listing 2-3
chapter02/src/main/java/com/bsg6/chapter02/Greeter.java
```

As mentioned above, TestNG makes use of annotations to instrument your test classes. For our test `GreeterTest,` we will use the method annotation `@Test` to let TestNG know to run `testHelloWorld` as a test – if any of the assertions fail, or if an exception is thrown, the test will be considered to have failed.

```
package com.bsg6.chapter02;
import org.testng.annotations.Test;
import java.io.ByteArrayOutputStream;
import java.io.PrintStream;
import java.nio.charset.StandardCharsets;
import static org.testng.Assert.assertEquals;
public class GreeterTest {
@Test
public void testHelloWorld() {
Greeter greeter = new HelloWorldGreeter();
final ByteArrayOutputStream baos = new ByteArrayOutputStream();
try (PrintStream ps = new PrintStream(baos,
true,
StandardCharsets.UTF_8)) {
greeter.setPrintStream(ps);
greeter.greet();
}
String data = baos.toString(StandardCharsets.UTF_8);
assertEquals(data, "Hello, World!");
}
}
Listing 2-4
chapter2/src/test/java/com/bsg5/chapter2/GreeterTest.java
```

You can see above that we also have a static import so in our assertions we can type `assertEquals(actual, expected)` vs. `Assert.assertEquals(actual, expected)`. Using static imports in test code is generally a good idea^([^(33)](#fn33)) as this type of code tends to duplicate calls many times in each method and we’re not losing any readability since it’s fairly constrained. The implementation of a `Greeter` is that an implementation will output something (the default implementation will send to `System.out`). A test that can confirm this is a bit difficult, so we inject our own implementation of a `PrintStream` and use that to assert the test case we expect. Take a look at the snippet of code below, and see how we’re creating a new `HelloWorldGreeter` object, a `ByteArrayOutputStream,` and the `PrintStream` which our `Greeter` will use to send data to (which by default is assigned to `System.out` – we want to override it so we can check the output).

Now that we have a test we can use to validate expected functionality, let’s write the implementation for `HelloWorldGreeter` so we can get it to compile and run. Below you’ll find a base implementation of the `Greeter` interface.

```
package com.bsg6.chapter02;
import java.io.PrintStream;
public class HelloWorldGreeter implements Greeter {
public void setPrintStream(PrintStream printStream) {
}
public void greet() {
}
}
Listing 2-5
HelloWorldGreeter without implementation
```

With the above in place, we can run `mvn test` and get the expected failure below.^([^(34)](#fn34)) The failure proves our assumption that the current implementation of `Greeter` does in fact not print “Hello, World!” and the test works as expected. (The actual failure record can be seen in `chapter02/target/surefire-reports/index.html`, if you’re interested, but in this case we know exactly why the test failed: our class doesn’t do anything yet.)

```
[INFO]
[INFO] Results:
[INFO]
[ERROR] Failures:
[ERROR]   GreeterTest.testHelloWorld:23 expected [Hello, World!] but found []
Listing 2-6
GreeterTest failure
```

Finally, let’s actually write the implementation for `HelloWorldGreeter` so that we know the test not only works as expected but will pass. We’re going to write a non-Spring implementation of `HelloWorldGreeter` so that in the next section when we add the relevant Spring configuration you’ll have a base of understanding to work from.

```
package com.bsg6.chapter02;
import com.bsg6.chapter02.Greeter;
import java.io.PrintStream;
public class HelloWorldGreeter implements Greeter {
private PrintStream printStream = System.out;
public void setPrintStream(PrintStream printStream) {
this.printStream = printStream;
}
public void greet() {
printStream.print("Hello, World!");
}
}
Listing 2-7
chapter02/src/main/java/com/bsg6/chapter02/HelloWorldGreeter.java
```

Now if we rerun the build, the test passes, because the `PrintStream` contains the content the test expects.

In this section, we wrote a test using TestNG against the `Greeter` interface and exercised the implementation we wrote called `HelloWorldGreeter`. Everything has used vanilla Java so far because of the nature of the example and was very simple to achieve. In the next section we’re going to refactor the above to show you how to wire the beans together using the power of Spring’s dependency injection framework.

## A Simple Application with Spring

What have we done so far? We’ve built an “application” (in our test) that creates a `Greeter` and, after supplying it with instances of a `PrintStream` we can use to test, we exercise it. We are manually building the classes to inject into our `Greeter`, and we’re manually instantiating the actual `Greeter` implementation as well.

Spring allows us to automate almost everything **but** the test itself – which we don’t really want to automate (although I suppose we could, given a scaffolding). We’re going to get Spring to do all of the object instantiation and injection for us; the power here is that if we should want to redirect to something different, all we need to do is change the actual objects being injected.

Historically one of the most pervasive arguments for not using Spring was the configuration, which was written in namespaced XML.

While there is definitely a programmatic way of implementing our Spring context,^([^(35)](#fn35)) for purposes of this example, we’re going to stick with the XML file. Using the conventions from our build system, we can put the config file in `src/main/resources` and load it from the classpath.

The header of our file below pulls in specifics that we’ll need from Spring which include the specs for beans.

Note that most IDEs can generate Spring contexts, including most or all of this, for you.

```

Listing 2-8
applicationContext.xml XML header
```

Let’s add our beans to the Spring config. In the section on testing, we checked the output of the `HelloWorldGreeter` by creating a String from a passed in `ByteArrayOutputStream`. When we wrote this out without using Spring, we were forced to create the `ByteArrayOutputStream`, `PrintStream`, and `HelloWorldGreeter` implementation like so.

```
Greeter greeter = new HelloWorldGreeter();
final ByteArrayOutputStream baos = new ByteArrayOutputStream();
try (PrintStream ps = new PrintStream(baos,
true,
StandardCharsets.UTF_8)) {
greeter.setPrintStream(ps);
greeter.greet();
}
String data = baos.toString(StandardCharsets.UTF_8);
assertEquals(data, "Hello, World!");
Listing 2-9
GreeterTest test to validate HelloWorldGreeter
```

While the above is certainly fine, we can do the entire thing with the Spring context. We will create the `ByteArrayOutputStream` and `PrintStream` objects using the Spring context along with our `HelloWorldGreeter` which will take the `PrintStream` as a bean reference.

Take a look at the following listing to see this in action.

```

Listing 2-10
chapter2/src/main/resources/applicationContext.xml
```

With the above we can greatly simplify our test because Spring ends up doing all of the work by reading our configuration. We’re obviously (hopefully!) great proponents of testing so the way we’ll ensure everything is working is to spruce up our test with Spring.

Lastly, we will look at our test class, with a focus on the new `testHelloWorld` method, and see how much it has simplified.

The first thing is does is create an `ApplicationContext` reference, by instantiating a `ClassPathXmlApplicationContext`. This is a concrete instance of an `ApplicationContext` that loads a configuration file from the classpath. As you may (or may not) suspect, this is one of many possible concrete instances of `ApplicationContext`; for most simple uses, this is one that works quite well.

Outside of the creation of the Spring context, the rest of the code is quite simple. With Spring injecting our `HelloWorldGreeter` into the test class, in our method we can simply call `greeter.greet()` and then convert the contents in the injected `ByteArrayOutputStream` into a `String` and assert that it equals what we expect. If all goes well, and it should, we’ll get a passing test now.

```
package com.bsg6.chapter02;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.testng.annotations.Test;
import java.io.ByteArrayOutputStream;
import java.nio.charset.StandardCharsets;
import static org.testng.Assert.assertEquals;
public class SpringGreeterTest {
@Test
public void testHelloWorld() {
ApplicationContext context =
new ClassPathXmlApplicationContext(
"/applicationContext.xml"
);
Greeter greeter = context.getBean("helloGreeter",
Greeter.class);
ByteArrayOutputStream baos = context
.getBean("baos", ByteArrayOutputStream.class);
greeter.greet();
String data = baos.toString(StandardCharsets.UTF_8);
assertEquals(data, "Hello, World!");
}
}
Listing 2-11
chapter02/src/test/java/com/bsg6/chapter02/SpringGreeterTest.java
```

When we run this test – by running `mvn test`, in our top-level directory – we should see something equivalent to the following output.

```
$ mvn test
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running TestSuite
SLF4J: No SLF4J providers were found.
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#noProviders for further details.
[INFO] Tests run: 2, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.566 s - in TestSuite
[INFO]
Listing 2-12
Truncated output of mvn test in root directory
```

There’s no output for a successful test – only a successful build. Congratulations, programmer!

One thing: it’s **very** important to note the class hierarchy of this test: it **must** extend `AbstractTestNGSpringContextTests` (or, if you’re using JUnit 4, `AbstractJUnit4SpringContextTests`; JUnit 5 has a different extension mechanism, where you annotate a test class with `@ExtendWith(SpringExtension.class)` instead of altering the class hierarchy. The new base class of the test is where the context is loaded and any processing occurs, before the tests are run.^([^(36)](#fn36))

If we ignore the extravagant lengths we went to do build a Hello, World app and think through the power that can come from loosely coupling our individual classes in a bigger application, the power of dependency injection really starts to shine. In this case we simply created and injected an `OutputStream` that gave us the ability to see what had been written into it; we could easily replace this with an `OutputStream` (or `Greeter`) that sent an email, or logged data, or performed a translation into different languages, or any other feature we can think of – and yet our client application doesn’t have to change, or even be aware of the differences.

Since the configuration of the structure is **external** to the classes themselves, we can radically change the function of the program – while retaining a fairly high confidence that the program works as designed, because our object model is trivial to test, and our configuration is easily debugged.

## Next Steps

In our next chapter, we’re going to shift gears a bit and dive into configuration and bean declaration with Spring.

We’re going to expand on our small “Hello, World” example and start exercising more aspects that the Spring framework provides.^([^(37)](#fn37))

Footnotes [1](#471370_2_En_2_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_2_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_2_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_2_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_2_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_2_Chapter.xhtml#Fn6_source)   [7](#471370_2_En_2_Chapter.xhtml#Fn7_source)   [8](#471370_2_En_2_Chapter.xhtml#Fn8_source)   [9](#471370_2_En_2_Chapter.xhtml#Fn9_source)   [10](#471370_2_En_2_Chapter.xhtml#Fn10_source)   [11](#471370_2_En_2_Chapter.xhtml#Fn11_source)   [12](#471370_2_En_2_Chapter.xhtml#Fn12_source)   [13](#471370_2_En_2_Chapter.xhtml#Fn13_source)   [14](#471370_2_En_2_Chapter.xhtml#Fn14_source)   [15](#471370_2_En_2_Chapter.xhtml#Fn15_source)  

# 3. Configuration and Declaration of Beans

In this chapter, we’re going to explore a decent subset of Spring configuration, and we’re going to shift attention away from “Hello, World” into a simple application that will allow us to explore features and configuration. We’ll introduce the sample application first, then walk through a few different ways to configure it. There’s a lot of code here, much of it redundant on the surface, but we’ll use some base classes to help reduce the tendency to repeat ourselves.^([^(38)](#fn38))

## The Container

Spring changed how Java developers thought about class structure and design in their applications,^([^(39)](#fn39)) and it’s entirely doable to design applications with dependency injection in mind – without using Spring – while reaping the benefits of the mindset. However, the central mechanism for using the dependency injection mindset, regardless of what framework you use, is the Container.

The Container is responsible for managing instances of what are referred to as “managed objects.” Our `HelloWorldGreeter` from Chapter [2](#471370_2_En_2_Chapter.xhtml), for example, is a manageable object (as most objects would be) but *is* managed when it’s created and injected via the `ApplicationContext` – which is a Container. The `ApplicationContext` is the primary interaction point for Spring-managed classes, so when we refer to the “context,” this is usually what we’re referring to.

Classes that are managed by a Spring container are referred to as “Spring beans,” in context.

The Container not only creates instances – the Spring beans – but also provides references to those instances in various ways (through constructors, or mutators and accessors^([^(40)](#fn40))). The process as a whole is referred to as “wiring.”

We’re going to step through an example application’s specification, and then we’re going to start showing how to wire the application together with Spring.

## The Sample Application

This next section – the entire heading, actually – has nothing to do with Spring at all. No dependencies on Spring, no Spring references, no rites of Spring, absolutely nothing outside of a reference here and there to remind us that we’re in a book about Spring. However, we’re introducing an application that gives us enough functionality to explore Spring in some depth; without the application and an understanding of it, we’re going to end up saying “Hello, World” in eighty different ways. With the project, we’ll have a practical application to keep in mind as we walk through Spring features in some of our future chapters.

Imagine you’re the fan of a band called “Threadbare Loaf.”^([^(41)](#fn41)) If you were trying to introduce a friend to Threadbare Loaf, they’d probably wonder what song (or songs) they should listen to, to get a sense of what Threadbare Loaf is about as an artist; you might suggest their first hit, “Someone Stole The Flour,” or perhaps the single from their second release, “What Happened to Our First Release?”

These two songs might be considered the “hooks” for the artist. Most artists have a song or set of songs that exemplify the band’s direction and focus; for the Rolling Stones, it might be “Satisfaction” or “Jumpin’ Jack Flash”; for Pink Floyd it might be “Money” or “Comfortably Numb”; for The Beatles, it might be “Hey Jude” or “Let It Be,” and so forth and so on.

It’s not that the artists’ *other* songs aren’t great, but that these are songs which a given person might think were perfect to entice someone else to enjoy the band as well.

What we’re going to do is create an API for an application to allow users to suggest “hooks” for artists, and for other users to see what users suggested most. It’s a simple application, and we’re not going to construct a full user experience; we’re mostly going to focus on the core API for the application to illustrate Spring concepts.

We’re also going to create many, many versions of the API, with each iteration being used to demonstrate different features of Spring. The API will likely improve as we continue, but the goal of the iterations will *usually* be demonstrative and not for the purpose of making the API more “mature” or full-featured.

For the purposes of this book, the application will be called the “band gateway,” suggesting that the songs managed by the API are the “gateway songs” to appreciate the artists in question.

We’re initially going to have two entities to consider as part of our data model: the `Artist` and the `Song`.

An `Artist` is uniquely referred to by name.^([^(42)](#fn42)) We should expect to support only one `Artist` with a given name, as long as we’re working *only* with music.

A `Song` is scoped to an `Artist` and does *not* have a unique name – it’s perfectly legitimate to have a song called “Come Together” recorded by both The Beatles and Aerosmith, for example, and users might consider these songs the ideal introductions to both bands.^([^(43)](#fn43)) We’re also aware that artists might have other types of media that serve as “hooks” for the band, such as videos or other artistic works; for the purposes of simplicity, we’re assuming that we are *only* managing audio recordings and not, say, videos or paintings that might be associated with a given `Artist`.

Our application, in its initial form, needs to support a few basic read operations and two write operations.

The operations related to *reading* data are as follows:

*   Retrieve songs for an artist, ordered by popularity (the most popular song is the better “hook”).

*   Retrieve song names for an artist (for use in autocompletion operations).

*   Retrieve a list of artist names (for use in autocompletion operations).

Then, we also need to allow people to **contribute** to our database:

*   Record that a song exists.

*   Vote for a song as a hook for a given `Artist.`

### The Code for the Band Gateway

We’re going to need at least five classes to begin to construct our API: our model consists of two classes (the `Artist` and `Song`), and we’re going to create an interface (called `Normalizer`) that represents a method by which we can transform (or “normalize”) names for our API, and lastly, an interface for our API (the `MusicService`) and a base class that contains an in-memory representation of our model (`BaseMusicService`).

![](images/471370_2_En_3_Chapter/471370_2_En_3_Fig1_HTML.jpg)

A flowchart runs as follows. I normalizer leads to C base music service which leads to I music service. I music service leads to E artist and E song which are interconnected. E artist lists I d integer and name string. E song lists I d integer, artist I d integer, title string, and votes integer.

Figure 3-1

API classes

We’re also eventually going to build a base class for our tests, for the same reason (it will contain the basic tests we will want to run no matter what the actual implementation of the `MusicService` is), and our *actual* tests will extend this base class.

Our model will be concrete, but the rest of the interfaces and abstract classes are chosen because we don’t want to have eight implementations of the same functionality.^([^(44)](#fn44))

#### The Build

We’ll want to create a module for this project. In Chapter [2](#471370_2_En_2_Chapter.xhtml), we created a top-level project and a `chapter02` module under it; in Chapter [3](#471370_2_En_3_Chapter.xhtml) (this one), we’ll follow the same idea and create a `chapter03` directory. You’ll want to open up your `pom.xml` from the top-level directory and make sure it has a `<module>chapter03</module>` line after the reference to `chapter02`.

Our `chapter03` directory should have a source tree in it, of course, so create `src/main/java`, `src/main/resources`, `src/test/java`, and `src/test/resources`. If you’re using a UNIX-like operating system like OSX or Linux, you can do this trivially with a typical shell like `bash` or `zsh` with the following commands, when in the project’s top-level directory.

```
mkdir -p chapter03/src/main/java
mkdir -p chapter03/src/test/java
mkdir -p chapter03/src/main/resources
mkdir -p chapter03/rc/test/resources
Listing 3-1
Building the chapter03 directory structure
```

Next we need a `pom.xml` to tell Maven how to compile and test this chapter’s code. It’s nearly identical to Chapter [2](#471370_2_En_2_Chapter.xhtml)’s `pom.xml`, differing *only* in the project name in `<artifactId>`.

```

4.0.0

com.apress
bsg6
1.0

chapter03
1.0

Listing 3-2
chapter03/src/pom.xml
```

#### The Model

First let’s take a look at our model classes, `Artist` and `Song`. These are simple Java objects, and as such there’s a lot of boilerplate with accessors and mutators, `equals()`, `hashCode()`, and `toString()`.

Our implementations are also fairly simple – we’re choosing mutable classes, with default constructors as well as simple parameterized constructors. We’re leaving our classes mutable (thus, an `Artist` might change names, according to our model.) There are reasons to prefer immutable classes (namely, performance, and avoiding having programmers who worship at the Church of Functional Programming say rude things about you), and there are also reasons to expect more complicated constructors that validate arguments and other such things, but we’re going to stay simple by choice and acknowledge that there are workable alternatives even though we’re not using them here.

Our first class is the `Song`, which is very simple: it contains the `Song name` (a `String`) and a number of `votes`, basically the number of people who suggest that this `Song` is a good hook for its artist. It does not have a reference to the `Artist` (because it will exist only in the context of an `Artist`, as we’ll see in the next code listing – an `Artist` has a reference to a `Set` of `Song` instances).

Most of the code in the listing is, of course, boilerplate; note that we do not consider `votes` as part of uniqueness, but only the `Song name`.

```
package com.bsg6.chapter03.model;
import java.util.Objects;
import java.util.StringJoiner;
public class Song implements Comparable {
private String name;
private int votes;
public Song() {
}
public Song(String name) {
setName(name);
}
public String getName() {
return name;
}
public void setName(String name) {
this.name = name;
}
public int getVotes() {
return votes;
}
public void setVotes(int votes) {
this.votes = votes;
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof Song)) return false;
Song song = (Song) o;
return Objects.equals(getName(), song.getName());
}
@Override
public int hashCode() {
return Objects.hash(getName());
}
@Override
public String toString() {
return new StringJoiner(", ", Song.class.getSimpleName() + "[", "]")
.add("name='" + name + "'")
.add("votes=" + votes)
.toString();
}
@Override
public int compareTo(Song o) {
int value = Integer.compare(o.getVotes(), getVotes());
if (value == 0) {
value = getName().compareTo(o.getName());
}
return value;
}
}
Listing 3-3
chapter03/src/main/java/com/bsg6/chapter03/model/Song.java
```

Our `Artist` class consists of a `name` – again, a simple `String` – and a `Map` of `Song` objects, indexed by name, called `songs`. Since we’re using a `Map`, the songs are considered to be unique (we can’t have two `Song` instances with the same name, by implication, although we need to make sure that our `Song` class enforces this.

```
package com.bsg6.chapter03.model;
import java.util.*;
public class Artist {
private String name;
private Map songs=new HashMap();
public Artist() {
}
public Artist(String name) {
setName(name);
}
public String getName() {
return name;
}
public void setName(String name) {
this.name = name;
}
public Map getSongs() {
return songs;
}
public void setSongs(Map songs) {
this.songs = songs;
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof Artist)) return false;
Artist artist = (Artist) o;
return Objects.equals(getName(), artist.getName());
}
@Override
public int hashCode() {
return Objects.hash(getName());
}
@Override
public String toString() {
return new StringJoiner(", ", Artist.class.getSimpleName() + "[", "]")
.add("name='" + name + "'")
.add("songs=" + songs)
.toString();
}
}
Listing 3-4
chapter03/src/main/java/com/bsg6/chapter03/model/Artist.java
```

#### The Normalizer Interface

This is a simple single-access method interface, by which we can transform input somehow. The default transformation is to trim whitespace from the edges of the input string, but implementations can obviously change the behavior.

This is only an *interface*. It has a default implementation of the `transform` method; we could have made it a base class, but we’ve chosen to make it an interface so we can use it to illustrate some concepts with Spring later in the chapter.

```
package com.bsg6.chapter03;
public interface Normalizer {
default String transform(String input) {
return input.trim();
}
}
Listing 3-5
chapter03/src/main/java/com/bsg6/chapter03/Normalizer.java
```

#### The Music Service

The Music Service API itself is represented in a single interface. It really would be better, in a real-world application, to split the API calls related to `Artist` into an `ArtistService`, and the other API calls into a `SongService`, and then have the `MusicService` delegate to concrete implementations of *those* interfaces, but our initial revision is going to focus on different aspects of the configuration rather than object design.^([^(45)](#fn45))

The interface itself is simple:

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.model.Song;
import java.util.List;
public interface MusicService {
List getSongsForArtist(String artist);
List getMatchingSongNamesForArtist(String artist, String prefix);
List getMatchingArtistNames(String prefix);
Song getSong(String artist, String name);
Song voteForSong(String artist, String name);
}
Listing 3-6
chapter03/src/main/java/com/bsg6/chapter03/MusicService.java
```

Astute readers might be thinking “Why not use a `Set` instead of a `List` for the song names or artists?” That’s a good question! The reason – if we look into the future ever-so-slightly – is that we’re actually returning ordered results. They’re unique, which makes a `Set` workable – but ordered – and Java `Set` implementations **can** be ordered (see `TreeSet`), but that structure is more complex than a simple `List` is. For an external consumer of these classes, `List` represents how access is expected, and `Set` does not, so we’re opting for `List` here even when we could make `Set` work.

We want to have another interface for testing purposes – a `Resettable`.^([^(46)](#fn46)) This allows us to mark a component as being able to be reset – something useful for us during testing.

There are multiple ways to accomplish resetting a class. This just happens to use an interface to expose `reset()` to other classes, so it meets some object-oriented design requirements while really contributing fairly little value. It’s just convenient for testing, especially when we haven’t explored the scope of references we get from Spring – which we’ll do more in Chapter [4](#471370_2_En_4_Chapter.xhtml) when we introduce the `prototype`.

```
package com.bsg6.chapter03;
public interface Resettable {
void reset();
}
Listing 3-7
chapter03/src/main/java/com/bsg6/chapter03/Resettable.java
```

There’s not a lot going on here – we just want implementations to be easily marked as being able to be reset, and we want to know the entry point for actually causing the reset. Since we’re not insane, we’re going to call the method `reset()`.

We’re also going to build a base class that implements each of those methods in both `MusicService` and `Resettable` to varying degrees. This class is going to be a little more verbose than it really should be, because it has to anticipate conditions that normal implementations shouldn’t have to. Let’s take a look at it, and then we’ll discuss what it’s doing and why.

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.model.Artist;
import com.bsg6.chapter03.model.Song;
import java.util.*;
import java.util.function.Function;
import java.util.stream.Collectors;
public abstract class AbstractMusicService implements MusicService, Resettable {
private Map bands = new HashMap();
protected String transformArtist(String input) {
return input;
}
protected String transformSong(String input) {
return input;
}
@Override
public void reset() {
bands.clear();
}
private Artist getArtist(String name) {
String normalizedName = transformArtist(name);
return bands.computeIfAbsent(normalizedName,
s -> new Artist(normalizedName));
}
@Override
public Song getSong(String artistName, String name) {
Artist artist = getArtist(artistName);
String normalizedTitle = transformSong(name);
return artist
.getSongs()
.computeIfAbsent(normalizedTitle, Song::new);
}
@Override
public List getSongsForArtist(String artist) {
List songs = new ArrayList(
getArtist(artist)
.getSongs()
.values()
);
songs.sort(Song::compareTo);
return songs;
}
@Override
public List getMatchingSongNamesForArtist(String artist,
String prefix) {
String normalizedPrefix = transformSong(prefix)
.toLowerCase();
/**
* This is a fun operation!
*
* We get the artist's songs, which are in a map.
* We then get only the keys of the map, which are all we care about.
* Next, we want to process those keys in a stream...
* We filter the names by looking for titles that start with
*    our prefix..
* Then sort them in alphabetical order
* And return them in a list.
*/
return getArtist(artist)
.getSongs()
.keySet()
.stream()
.map(this::transformSong)
.filter(name -> name
.toLowerCase()
.startsWith(normalizedPrefix))
.sorted(Comparator.comparing(Function.identity()))
.collect(Collectors.toList());
}
@Override
public List getMatchingArtistNames(String prefix) {
String normalizedPrefix = transformArtist(prefix)
.toLowerCase();
return bands
.keySet()
.stream()
.filter(name -> name
.toLowerCase()
.startsWith(normalizedPrefix))
.sorted(Comparator.comparing(Function.identity()))
.collect(Collectors.toList());
}
@Override
public Song voteForSong(String artistName, String name) {
Song song = getSong(artistName, name);
song.setVotes(song.getVotes() + 1);
return song;
}
}
Listing 3-8
chapter03/src/main/java/com/bsg6/chapter03/AbstractMusicService.java
```

There’s quite a lot going on in this class.

First, we have two methods (`transformSong()` and `transformArtist()`) that do nothing but return their input parameters. These are functional stubs, or hooks; classes that extend the `AbstractMusicService` can (and probably should) override their behavior, possibly by delegating to a `Normalizer` instance. These methods are meant to be called in order to make sure `Song` and `Artist` names are in some sort of consistent form before use.

Next we have our humble `reset()` method – which, true to its name, simply clears out the in-memory data structure.

After the normalization methods, we have a private method, `getArtist()`, which maps a `String` into a valid `Artist` reference. If the `Artist` does not exist, we create an instance for future use. We then see the same pattern replicated in `getSong()`, with the main difference between the methods being that `getSong()` is exposed as part of the external API. (It fulfills the specification requirement of being able to create a `Song` without voting for it as a hook for the artist.)

Next we have a series of methods to retrieve a sorted set of `Song` references for the `Artist` and return lists of matching names. In nearly every case we call our transformation methods to make sure the names are in consistent form (even though by default that doesn’t change the names at all). Even though some of the chains of methods look quite long (see `getMatchingSongNamesForArtist()` as an example, where *eight* methods are chained together in a row, with a few extra chains embedded), the actual process is expressed rather simply, and few of those methods incur any noticeable CPU cost.

Note

In `getMatchingSongNamesForArtist()`, the `sorted()` method for the `Stream` is probably the most expensive method call. Of course, if you really wanted to make sure, you’d crank up your friendly local profiler and check, and leave assumptions to authors and other such miscreants.

One thought that our eagle-eyed readers will probably already have noted: our model classes are mutable (i.e., can be changed), *and* we’re returning the actual instances that represent our model in these methods. In other words, a bad actor (or rookie programmer) could get a song from `voteForSong()`, then call `setVotes(400)` – and the actual “database” would change the votes for that `Song` to 400\. This is not wise; you don’t want to propagate changes unintentionally. Here, though, we’ll allow it because this is a memory-only demonstration and the code’s complex enough already without adding a copy step to our methods. Consider it an exercise for the reader.

Note

If our data model was actually being held in secondary storage – i.e., in a file, or in a database, or on punch cards, the persistence layer could have a step that had nothing do to with the references in memory, and therefore, the incorrect changes would propagate only to the scope of the instance. This problem is limited to the in-memory form of the data storage.

At long last, it’s time for us to start building some concrete implementations and tests, wiring them together with Spring.^([^(47)](#fn47))

## Configuration Through Annotation

Configuration through annotations in Spring is very simple. The main thing to remember is that we want to **tell Spring we’re using annotations**. You can do this in a number of ways; we’re going to use XML to configure the annotations, since it’s simple (and reflects what we’ve already learned, in Chapter [2](#471370_2_En_2_Chapter.xhtml)).

After that, we have to remember that Spring will work only with *managed* objects: that means we can annotate a class all we like, but if we don’t retrieve it from a Spring container (an `ApplicationContext`), the annotations will be irrelevant.

Annotations in Spring generally fall into two categories: component declaration and wiring. Component declaration means that a class is managed by a container, and wiring means that the class has resources that are injected (or “wired”) by the container.

### Declaring a Spring Bean with **@Component**

To enable scanning for components, we need to tell Spring to, well, scan for the components. Let’s create the first in a series of configuration files, called `config-01.xml`, in `src/test/resources`. This is a baseline Spring configuration for annotations; normally we’d have a *little* more information in this file, but this will serve for our first explorations.

This file has one significant line in it: `<context:component-scan base-package="com.bsg6.chapter03.mem01" />`. (Well, that’s not strictly true: the entire `<beans...`​`>` content is pretty important, too. Most people, including your authors, copy and paste that section, unless you use something like the Spring Tool Suite,^([^(48)](#fn48)) which can build the header for you.) What this line does is pretty simple: it scans that package and any others under it for beans marked with the valid annotations for components, like `@Component`, `@Service`, `@Repository`, and others. (The specific meanings will be discussed in later chapters.) Those beans will be registered in the `ApplicationContext` and can be referred to by any other Spring components managed by that `ApplicationContext`, whether scanned in with `<component-scan />` or not.^([^(49)](#fn49))

Now that we’ve talked about it, let’s take a look at our first iteration of the Spring configuration:

```

Listing 3-9
chapter03/src/test/resources/config-01.xml
```

We’ll need two more classes to make this worthwhile. Our first one will be a concrete realization of the `AbstractMusicService` interface, which will do nothing more than add a `@Component` annotation to the abstract class. (An abstract class in Java can’t be instantiated, so telling Spring that an abstract component is a component would be …​ unfortunate.)

```
package com.bsg6.chapter03.mem01;
import com.bsg6.chapter03.AbstractMusicService;
import org.springframework.stereotype.Component;
@Component
public class MusicService1 extends AbstractMusicService {
}
Listing 3-10
chapter03/src/main/java/com/bsg6/chapter03/mem01/MusicService1.java
```

We also want to build a test, however. Our first test is going to be very simple and test very little of the `MusicService` interface; we’re going to use it to *acquire* a `MusicService`, and we’re also going to take a quick look inside the `ApplicationContext` to get a sense of what our simple Spring configuration actually contains.

As we’ve seen in Chapter [2](#471370_2_En_2_Chapter.xhtml), Spring-aware tests using TestNG extend `AbstractTestNGSpringContextTests`. We add a class-level annotation, `@ContextConfiguration`, with a reference to our `/config-01.xml` file to specify that this test uses that particular configuration. Let’s take a look at the source file for the test, and then we’ll discuss what we can learn from it.

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.model.Song;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
import java.util.Arrays;
import java.util.HashSet;
import java.util.Set;
import static org.testng.Assert.assertEquals;
import static org.testng.Assert.assertNotNull;
import static org.testng.Assert.assertTrue;
@ContextConfiguration(locations = "/config-01.xml")
public class TestMusicService1 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@Autowired
MusicService service;
@Test
public void testConfiguration() {
assertNotNull(context);
Set definitions = new HashSet(
Arrays.asList(context.getBeanDefinitionNames())
);
/*
// uncomment if you'd like to see the entire set of defined beans
for (String d : definitions) {
System.out.println(d);
}
*/
assertTrue(definitions.contains("musicService1"));
}
@Test
public void testMusicService() {
Song song = service.getSong(
"Threadbare Loaf", "Someone Stole the Flour"
);
assertEquals(song.getVotes(), 0);
}
}
Listing 3-11
chapter03/src/test/java/com/bsg6/chapter03/TestMusicService1.java
```

Aside from the `@ContextConfiguration` annotation on the test class itself, we also have two fields marked with `@Autowired`. The first one is an `ApplicationContext` and the second is a `MusicService`. When Spring sees an `@Autowired` field, it will look for classes that it manages that fit the description of the field, and automatically set their references to the matching instances.

Warning

Autowiring happens **only** if Spring manages the class that requests autowiring! If we were to instantiate the classes manually, Spring would leave them alone and no wiring would occur.

The simplest matching is done on the basis of type; if we are wiring a field of type `ApplicationContext` and Spring is managing only one component that can be assigned to that field, then that component’s reference is used in the field.

If the configuration contains more than one component that can be assigned to the reference, then Spring will look for a component whose name matches the reference. Thus, if we had a field named `xyzyx`, of type `Fuzzball`, it would look for a *single* component of type `Fuzzball`, and if it had more than one `Fuzzball` in the configuration, it would look for the component named `xyzyx`, and fail to inject that value if such a component wasn’t found.

We’ll also see the use of `@Qualifier` for autowired fields in a subsequent test, through which we can tell Spring what specific component name to look for, such that it does not try to derive the component name from the reference. (In other words, we could tell it to look for a component called `myxalotl` even if the field was `xyzyx`. We promise: we’ll explain more later.)

There are two tests in this file: `testConfiguration()` and `testMusicService()`. The first test, `testConfiguration()`, simply checks our configuration to demonstrate that wiring has occurred properly. It first checks to make sure that the `ApplicationContext` has been provided to the test; if the `context` reference is `null`, we know something has failed rather dramatically.^([^(50)](#fn50)) After it makes sure that the `context` is available, it checks to make sure that a component with a specific name (`musicService1`) is available in the context. This is the name Spring will derive from a component called `MusicService1`, by default. We can override it with `@Component`, and we will do so in a later example; if no name is specified, Spring takes the class name (`MusicService1`) and decapitalizes the first letter (leaving it as `musicService1`).

If you would like to see all of the beans in the context as specified by our current configuration and package structure, you could include the code from the Java comment. For this test, for the record, this code outputs the following.

```
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
musicService1
org.springframework.context.annotation.internalAutowiredAnnotationProcesso
Listing 3-12
Console output from the commented code in TestMusicService1
```

The `org.springframework` classes noted here are components for internal Spring use; they’re not really things we care about much, as application designers. What we’re looking for in that list is the `musicService1` reference.

The second test simply calls `getSong()` for our mythical band, “Threadbare Loaf,” and makes sure that the song should have no votes (as we’ve not registered any yet).

### Wiring Components Together with **@Autowired**

In our first configuration, we had a single component (a `MusicService`), and our test simply made sure the component scanning was working and that our component was able to be injected into our test.

In our next configuration, we’re going to actually have two components, and wire them together. We’re going to add a good bit of code here, too, to have a repeatable set of tests that we can apply to multiple configurations; when we reach the end of the chapter, we can use this to test nearly all of our implementations in series.^([^(51)](#fn51))

We’re going to create another instance of `MusicService`, one that looks almost exactly like our `MusicService1`, except that it’s going to override `transformArtist()` and `transformSong()` such that they call an injected `Normalizer` instance. We’ll also need to create a concrete type to serve as a `Normalizer` – because we declared it as an interface, so it’s non-instantiable in and of itself. We also need another configuration file, and two more classes: one will be our base test class (incorporating tests we will want to be able to apply to *every* `MusicService`), and the other will be our actual executable test, which delegates to our base test class.

Our `MusicService2` class is very simple:

```
package com.bsg6.chapter03.mem02;
import com.bsg6.chapter03.AbstractMusicService;
import com.bsg6.chapter03.Normalizer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
@Component
public class MusicService2 extends AbstractMusicService {
@Autowired
Normalizer normalizer;
@Override
protected String transformArtist(String input) {
return normalizer.transform(input);
}
@Override
protected String transformSong(String input) {
return normalizer.transform(input);
}
}
Listing 3-13
chapter03/src/main/java/com/bsg6/chapter03/mem02/MusicService2.java
```

Our `Normalizer` is a simple concrete realization of our `Normalizer` interface; the primary values it conveys are in that it can be marked as a `@Component`, and it can be instantiated. Since we have a default method body in `Normalizer`, we don’t even have to create the method in our `SimpleNormalizer`, unless we want it to do more than simply trim whitespace. (At this point in our application, we’re not concerned about the actual functionality; we’re demonstrating component wiring).

```
package com.bsg6.chapter03.mem02;
import com.bsg6.chapter03.Normalizer;
import org.springframework.stereotype.Component;
@Component
public class SimpleNormalizer implements Normalizer {
/* inherits default transform() method from interface */
}
Listing 3-14
chapter03/src/main/java/com/bsg6/chapter03/mem02/SimpleNormalizer.java
```

Lastly, our configuration file, `config-02.xml`.

```

Listing 3-15
chapter03/src/test/resources/config-02.xml
```

Now we get to the “fun” part of the code. We’re going to build a test class that contains a series of methods usable by a test. They won’t be invoked directly by TestNG, but they’re designed to be called from test methods that *are* invoked by the test framework.

Why are we doing it this way? Primarily, it’s because a test class has a single application context.^([^(52)](#fn52)) What we’d like to do is enable ourselves to eventually load a set of configurations on demand and run each test on each individual configuration; this way, we repeat ourselves for the delegation call but little else.

This class is designed to be used through composition – that is, it’s designed to be included in an actual class with the tests. This may make you think that it’s a good candidate to be a Spring bean – and you’re right, it is. We’ll show this in practice in a later section of this chapter.

Let’s take a look at our `MusicServiceTests` class, and then we’ll step through some of the details of what it provides. After we do that, we’ll show a test that actually uses this class – and the good news is that the actual test class is far, far shorter than the `MusicServiceTests` class.

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.model.Song;
import java.util.List;
import java.util.function.Consumer;
import static org.testng.Assert.assertEquals;
public class MusicServiceTests {
private Object[][] model = new Object[][]{
{"Threadbare Loaf", "Someone Stole the Flour", 4},
{"Threadbare Loaf", "What Happened To Our First CD?", 17},
{"Therapy Zeppelin", "Medium", 4},
{"Clancy in Silt", "Igneous", 5}
};
void iterateOverModel(Consumer consumer) {
for (Object[] data : model) {
consumer.accept(data);
}
}
void populateService(MusicService service) {
iterateOverModel(data -> {
for (int i = 0; i 
assertEquals(
service.getSong((String) data[0],
(String) data[1]).getVotes(),
((Integer) data[2]).intValue()
));
}
void testSongsForArtist(MusicService service) {
reset(service);
populateService(service);
List songs = service.getSongsForArtist("Threadbare Loaf");
assertEquals(songs.size(), 2);
assertEquals(songs.get(0).getName(), "What Happened To Our First CD?");
assertEquals(songs.get(0).getVotes(), 17);
assertEquals(songs.get(1).getName(), "Someone Stole the Flour");
assertEquals(songs.get(1).getVotes(), 4);
}
void testMatchingArtistNames(MusicService service) {
reset(service);
populateService(service);
List names = service.getMatchingArtistNames("Th");
assertEquals(names.size(), 2);
assertEquals(names.get(0), "Therapy Zeppelin");
assertEquals(names.get(1), "Threadbare Loaf");
}
void testMatchingSongNamesForArtist(MusicService service) {
reset(service);
populateService(service);
List names = service.getMatchingSongNamesForArtist(
"Threadbare Loaf", "W"
);
assertEquals(names.size(), 1);
assertEquals(names.get(0), "What Happened To Our First CD?");
}
}
Listing 3-16
chapter03/src/main/test/com/bsg6/chapter03/MusicServiceTests.java
```

The first bit worth noticing in this class is the `Object[][] model`. This may surprise some readers, but this is our starting data model for the tests. Each row represents an artist’s song, and the number of times users have indicated that this song is the artist’s ideal introductory song. Thus, we have three artists represented, with four songs; two of the artists have “Th” at the beginning of the names, and one of the artists has two songs represented. This allows us to test name matching (for autocompletion) as well as allowing us to test the sorting of songs based on votes.

The first method in the class is `iterateOverModel()`. This method is designed to allow us to execute a simple method over every row in the `model`; it accepts a `Consumer<Object[]>`, which means a lambda that expects an `Object[]`, and calls the lambda for every row; nothing more, nothing less. It can be seen being used in the next method in this class, `populateService()`.

The next method – again, `populateService()` – creates a lambda that issues a single vote for a song and calls that lambda multiple times for every row in our `model`. The model has an `Integer` as the third “column,” and `populateService()` calls `voteForSong()` for each song as many times as that column indicates. In other words, if we have an artist of “Threadbare Loaf” and a song title of “What Happened To Our First CD?” with a third column of 17 – and this may surprise you, but we do have those things – `populateService` will call `voteForSong()` seventeen times for that particular song. This method is primarily meant as a utility method, not something to serve as a test itself.

We have another utility method after `populateService()`, called `reset()`. This makes sure that the `Service` is marked as `Resettable`, and if it’s not, throws an exception; for this chapter, all of our services **are** resettable, and if they’re not, we want to know. Assuming they are, then this method simply delegates to the service’s `reset()` method, which **should** empty out the service’s data model.

After that, we have four methods that are designed to be delegated to – `testSongVoting()`, `testSongsForArtist()`, `testMatchingArtistNames()`, and `testMatchingSongsForArtist()`. These all accept a `Service` reference and generally exercise the `Service`, using data that we know about the model. The `testSongVoting()` method tests the votes for every song in our model.

However, as we’ve stated, this class isn’t actually usable as a test. We want to write a test that delegates to an instance of `MusicServiceTests` to actually execute the methods that actually represent our tests.

```
package com.bsg6.chapter03;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
@ContextConfiguration(locations = "/config-02.xml")
public class TestMusicService2 extends AbstractTestNGSpringContextTests {
@Autowired
MusicService service;
MusicServiceTests tests = new MusicServiceTests();
@Test
public void testSongVoting() {
tests.testSongVoting(service);
}
@Test
public void testGetMatchingArtistNames() {
tests.testMatchingArtistNames(service);
}
@Test
public void testGetSongsForArtist() {
tests.testSongsForArtist(service);
}
@Test
public void testMatchingSongNamesForArtist() {
tests.testMatchingSongNamesForArtist(service);
}
}
Listing 3-17
chapter03/src/main/test/com/bsg6/chapter03/TestMusicService2.java
```

Finally, we have an actual executable test!^([^(53)](#fn53)) This test has the same scaffolding as `TestMusicService1`, except with a different configuration file. It uses an injected `MusicService` and instantiates a local copy of `MusicServiceTests`; it then has four test methods, each of which delegates to the method of the same name in `MusicServiceTests`. It’s very simple. It **also** manages to test *every line of code* in `MusicService2` and its superclass, `AbstractMusicService`, with the sole exceptions being the methods in `AbstractMusicService` that are overridden by `MusicService2`.

Note

It’s a good thing that every line of code is hit, even though all of these are test classes and the code doesn’t *really* matter, but don’t make the mistake of thinking that 100% code coverage is an “end goal.” We have 100% coverage, but we’ve not tested any edge cases, and no failure conditions. Again: 100% coverage is good, but it’s a lousy target; you should aim to fulfill all of the specification’s requirements, instead. It just so happens that our “specification” doesn’t contain any error conditions to speak of.

### Choosing Components with `@Qualifier` and Bean Names

In the previous section, we injected a normalizer into our `MusicService2`, and used it to normalize both the artist names and the song names. This assumes, though, that both sets of names are normalized in the same way. What if they’re not?

In that case, we need to use two different kinds of normalizers. Then we’re faced with a different kind of problem: how do we tell Spring which normalizer to use, and where?

It turns out we have a few different options. Since injection is based on types, we could simply create new marker interfaces, like `SongNameNormalizer`, that simply extends `Normalizer`. Then we would have a declaration like `@Autowired SongNameNormalizer` `songNameNormalizer` and be done; Spring would look at the components it manages, find one component that can be assigned to `songNameNormalizer`, and be done.

We can also use the component names, as opposed to the components’ assignable types. The `@Component` annotation has an optional value that serves as the component name for Spring. The default component name is the class’ name itself, with an initial lowercase letter (thus, `MusicService1` is given a component name of `musicService1`), but if we use `@Component("bluePin")` then the component is named, predictably, `bluePin` instead.

If the autowired reference uses a name that matches a Spring bean – that is, we have `MusicService bluePin` – then Spring will inject a bean with that name.

That doesn’t help us when one of two different circumstances arise in our code, though. If our variable reference doesn’t match a component name, *or* we have two of the same types to inject, Spring still won’t know how to choose which instance to inject.

We can help Spring decide which component to inject by using the `@Qualifier` annotation, which requires a value corresponding to the name of the Spring bean you want assigned.

Let’s see it in action! We’re going to use *two* `Normalizer` types: one will trim all whitespace from the beginning and the end of the input text (the default behavior, and we’re basically replicating the `SimpleNormalizer` from the last example), and the other will capitalize the start of every word in addition to trimming the whitespace. Then we’ll create a `MusicService` that uses both normalizers, and lastly, we’ll have another test.

These listings will all be short, thankfully.

Here’s our configuration file. Again, it’s *very* similar to our previous configurations, only changing the `base-package` of the `component-scan` tag.

```

Listing 3-18
chapter03/src/test/resources/config-03.xml
```

Our `Normalizer` instances come next. Note that we don’t *need* the `@Component` annotations to use names; the two classes would get names of `simpleNormalizer` and `capLeadingNormalizer` by default. Those are boring names, and thus we’re changing them to `foo` and `bar`, respectively.

Note

Of course, `foo` and `bar` are just as boring, as is the use of `baz` later in this very chapter. These are known as “metasyntactic variables,” a set of meaningless placeholder words used as names when we don’t want to communicate specific meaning. In this code, they’re actually being used in exactly the wrong sense, since these references *do* have specific meaning, but we’re using the meaningless words because they help us illustrate a concept.

First, the `SimpleNormalizer`, which is basically a copy of what we’ve seen before, but with a new `package` and a name in the `@Component` annotation.^([^(54)](#fn54))

```
package com.bsg6.chapter03.mem03;
import com.bsg6.chapter03.Normalizer;
import org.springframework.stereotype.Component;
@Component("foo")
public class SimpleNormalizer implements Normalizer {
}
Listing 3-19
chapter03/src/main/java/com/bsg6/chapter03/mem03/SimpleNormalizer.java
```

Next, we have our `CapLeadingNormalizer`. The `transform()` method trims whitespace from the edges of the input, and then splits the string into words separated by whitespace and creates a `Stream`. It then filters out tokens that are blank and then (with `map()`) creates a word with a leading capitalized character and the rest of the word lowercased. It then joins the results into a single `String` as output. It looks (and sounds) more complicated than it is.

```
package com.bsg6.chapter03.mem03;
import com.bsg6.chapter03.Normalizer;
import org.springframework.stereotype.Component;
import java.util.StringJoiner;
import java.util.stream.Stream;
@Component("bar")
public class CapLeadingNormalizer implements Normalizer {
@Override
public String transform(String input) {
StringJoiner joiner = new StringJoiner(" ");
Stream
.of(input.trim().split("\\s"))
.filter(s -> !s.isBlank())
.map(s ->
Character.toUpperCase(s.charAt(0)) +
s.substring(1).toLowerCase()
)
.forEach(joiner::add);
return joiner.toString();
}
}
Listing 3-20
chapter03/src/main/java/com/bsg6/chapter03/mem03/CapLeadingNormalizer.java
```

Of course, now we have to wonder: will this code work? The simplest response is “of course it will,” but if you’re from Missouri like one of my favorite authors, that isn’t likely to fly. We had better cater to our readers from Missouri and demonstrate that this class does what it’s supposed to do, with a test.^([^(55)](#fn55)) Our test can be really simple, simply feeding a set of inputs into our `Normalizer` and checking it against expected output.

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.mem03.CapLeadingNormalizer;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import static org.testng.Assert.assertEquals;
public class TestCapLeadingNormalizer {
Normalizer normalizer=new CapLeadingNormalizer();
@DataProvider
Object[][] data() {
return new Object[][] {
{ "this is a test", "This Is A Test"},
{ " This IS a test ", "This Is A Test"},
{ "this     is   a test", "This Is A Test"}
};
}
@Test(dataProvider = "data")
public void testNormalization(String input, String expected) {
assertEquals(normalizer.transform(input), expected);
}
}
Listing 3-21
chapter03/src/main/test/com/bsg6/chapter03/TestCapLeadingNormalizer.java
```

Now that we have a test that shows that we can trust our `CapLeadingNormalizer`, it’s time to show a `MusicService` that uses it. This is a slight variant on `MusicService2`, as you might expect, with the main differences being in the `package` and the use of `Normalizer` references with a `@Qualifier` annotation, matching the names in our `Normalizer` classes. We also add mutators and accessors (again, “setters” and “getters”) to expose the `Normalizer` references – we don’t really need this yet, but we will.^([^(56)](#fn56))

```
package com.bsg6.chapter03.mem03;
import com.bsg6.chapter03.AbstractMusicService;
import com.bsg6.chapter03.Normalizer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
@Component
public class MusicService3 extends AbstractMusicService {
@Autowired
@Qualifier("bar")
Normalizer artistNormalizer;
@Autowired
@Qualifier("foo")
Normalizer songNormalizer;
public Normalizer getArtistNormalizer() {
return artistNormalizer;
}
public void setArtistNormalizer(Normalizer artistNormalizer) {
this.artistNormalizer = artistNormalizer;
}
public Normalizer getSongNormalizer() {
return songNormalizer;
}
public void setSongNormalizer(Normalizer songNormalizer) {
this.songNormalizer = songNormalizer;
}
@Override
protected String transformArtist(String input) {
return artistNormalizer.transform(input);
}
@Override
protected String transformSong(String input) {
return songNormalizer.transform(input);
}
}
Listing 3-22
chapter03/src/main/java/com/bsg6/chapter03/mem03/MusicService3.java
```

And finally, we want to verify that *this* code works, too, so let’s round things out with another (short) test class, which mirrors our previous test classes but changes the configuration file.

```
package com.bsg6.chapter03;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
@ContextConfiguration(locations = "/config-03.xml")
public class TestMusicService3 extends AbstractTestNGSpringContextTests {
@Autowired
MusicService service;
MusicServiceTests tests = new MusicServiceTests();
@Test
public void testSongVoting() {
tests.testSongVoting(service);
}
@Test
public void testGetMatchingArtistNames() {
tests.testMatchingArtistNames(service);
}
@Test
public void testGetSongsForArtist() {
tests.testSongsForArtist(service);
}
@Test
public void testMatchingSongNamesForArtist() {
tests.testMatchingSongNamesForArtist(service);
}
}
Listing 3-23
chapter03/src/test/com/bsg6/chapter03/TestMusicService3.java
```

Careful readers might note that we’re still manually instantiating the `MusicServiceTests` object. In the next section, we’ll fix that – and introduce constructor injection. However, we’re going to start reusing some of the code from *this* section and introduce some more configuration elements so that you, the reader, don’t have to read the same code over and over (and over and over) again.

### Constructor Injection with Annotations

There’s one more primary mode of bean injection we haven’t addressed so far: constructor injection. If you’ve looked at our example code in an IDE like IDEA ([`https://jetbrains.com/idea`](https://jetbrains.com/idea)) or Eclipse ([`https://eclipse.org`](https://eclipse.org)), you might have noticed that the IDE was telling you something ominous about the `@Autowired` annotation, like “Field injection is not recommended.”

The alternative is to use *constructor injection*, to have a constructor with parameters marked with `@Autowired` and, if necessary, `@Qualifier`. The reason is pretty simple: fields are mutable by implication, and using a constructor argument means that we can mark a field as `final`; *this* means that we can not only *require* a field to be set, but we can also assume that when it’s set, it remains at the value that’s been set.

In practice, all we need to do to enable constructor injection is, well, to create a valid constructor with the arguments marked for injection. The rules for constructor injection mirror the rules for field injection; we just get more control over it.

One other thing we’d like to do, to save ourselves from endless repetition in later sections (and chapters), is introduce a new configuration tag: `<import>`.

Note

If you’re wishing we’d have introduced this tag earlier in the chapter…​ well, us too. However, we’re trying to introduce new pieces of information at a deliberately slow pace, to avoid overwhelming readers new to the concepts we’re discussing, so this was where we *finally* felt like we had the chance to introduce this one. We’ve found that people usually learn best when new concepts are introduced piecemeal, so we’ve chosen to go fairly slowly.

What `<import>` does should be pretty obvious: it imports a separate configuration file into another. It allows us to create modular configurations.

We’re going to create two “modules” – meaning configurations with specific purposes. One is going to be a set of `Normalizer` instances, from the `com.bsg6.chapter03.mem03` package; the other is going to be our `MusicServiceTests` class. When we import those two configurations into a *third* configuration, the beans in each configuration will have access to one another.

There’s one problem, though: the `mem03` package has a `MusicService` implementation, too! So we’re going to introduce an additional attribute for `<context:component-scan />`, called `resource-pattern`.

Let’s start building our modules. First, the simplest one, a pure-XML configuration for `MusicServiceTests`. We’re not going to change that class from the code we’ve already seen, so there’s no annotation; we’re going to use a simple XML `<bean />` tag, much as we saw in Chapter [2](#471370_2_En_2_Chapter.xhtml).

```

Listing 3-24
chapter03/src/test/resources/musicservicetest.xml
```

This configuration is quite simple: it declares a bean named `musicServiceTests`, of the type `com.bsg6.chapter03.MusicServiceTests`. Therefore, in any context in which *this* file is referenced, `MusicServiceTests` is a valid candidate for injection, as we’ll see.

```

Listing 3-25
chapter03/src/test/resources/normalizers.xml
```

Here, we see the `resource-pattern` annotation for `<context:component-scan />`. This tag is a filter for the classes being scanned. Here, we use `resource-pattern="*Normalizer.class"`, which means that the only classes included in the scan are classes that *end with* `Normalizer` – thus, our `SimpleNormalizer` and `CapLeadingNormalizer` classes.

Why are we using `resource-pattern` here? Well, what we’re really doing is saying to scan only **specific** classes in the `com.bsg6.chapter03.mem03` package. If we didn’t limit the scan to only the normalization classes, we’d be importing the `MusicService3` too, and we don’t want to include that class in a configuration that’s supposed to be limited to, well, normalizers only.

The expression used here is a Java regular expression and includes the *entire* resource name – by default it’s `**/*.class`, meaning “any file in the entire package tree beginning at the package specified in `base-package` that ends with `.class`.”

In the `normalizers.xml` configuration, however, it’s limited to “any file in the current package that ends with `Normalizer.class,`” with the package set to `com.bsg6.chapter03.mem03`, so it will only pick up our two `Normalizer` implementations.

Now, we get to see our “master configuration” in use, showing the use of the two `<import />` tags. This creates a “flattened configuration,” such that anything that loads `config-04.xml` is going to see every component loaded by not only `config-04.xml`, but anything loaded by `normalizers.xml` and `musicservicetest.xml` as well.

```

Listing 3-26
chapter03/src/test/resources/config-04.xml
```

In fact, let’s prove this, by writing a quick test that validates that the beans we expect to have in our configuration are available. As usual, we’ll use a `@DataProvider` to provide a flexible dataset for a parameterized test. We’ll do something a little different, in that we’ll put both `String` instances and `Class` references into the data provider – and we’ll check the type to figure out which `ApplicationContext getBean()` method to use. (There are probably cleaner ways to do this, but honestly, this is a throwaway test. We don’t need it to be perfect, we just need it to work for our purposes.^([^(57)](#fn57)))

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.mem03.CapLeadingNormalizer;
import com.bsg6.chapter03.mem03.SimpleNormalizer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import static org.testng.Assert.assertNotNull;
import static org.testng.Assert.fail;
@ContextConfiguration(locations = "/config-04.xml")
public class TestConfigurationImport extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@DataProvider
Object[][] resources() {
return new Object[][]{
{"musicServiceTests"},
{MusicServiceTests.class},
{"foo"},
{"bar"},
{SimpleNormalizer.class},
{CapLeadingNormalizer.class},
{"musicService4"}
};
}
@Test(dataProvider = "resources")
public void validateResourceExistence(Object resource) {
if (resource instanceof String) {
assertNotNull(context.getBean(resource.toString()));
} else {
if (resource instanceof Class) {
assertNotNull(context.getBean((Class) resource));
} else {
fail("Invalid resource type");
}
}
}
}
Listing 3-27
chapter03/src/test/java/com/bsg6/chapter03/TestConfigurationImport.java
```

At last, it’s time to see our `MusicService4`, a near-clone of `MusicService3` – as all of our `MusicService` implementations have been. The major change here is that we’re going to make our `Normalizer` references `final`, and we’re going to initialize them through constructor injection. We’ll then have one more test class (a near-clone of other tests, referring to `config-04.xml` instead of the other configurations) to make sure our configuration loads smoothly and correctly (and, for that matter, quickly).

```
package com.bsg6.chapter03.mem04;
import com.bsg6.chapter03.AbstractMusicService;
import com.bsg6.chapter03.Normalizer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;
@Component
public class MusicService4 extends AbstractMusicService {
private final Normalizer artistNormalizer;
private final Normalizer songNormalizer;
public MusicService4(@Autowired
@Qualifier("bar")
Normalizer artistNormalizer,
@Autowired
@Qualifier("foo")
Normalizer songNormalizer) {
this.artistNormalizer = artistNormalizer;
this.songNormalizer = songNormalizer;
}
@Override
protected String transformArtist(String input) {
return artistNormalizer.transform(input);
}
@Override
protected String transformSong(String input) {
return songNormalizer.transform(input);
}
}
Listing 3-28
chapter03/src/main/java/com/bsg6/chapter03/mem04/MusicService4.java
```

Here, we’ve still got the `@Autowired` and `@Qualifier` annotations in use; they’re just applied to arguments in the constructor instead of being applied to class attributes. The class attributes themselves are marked `private final` – because they’re not used outside of this class, and because we want to tell the virtual machine to not allow the references to change, once set.

In *this* class, having the references be final is of limited value, mostly because the entire `MusicService` is of limited value – so far our implementations have been designed purely to show Spring configuration features. With that said, in real components, having values marked `final` can help the JVM optimize code, and it also makes your intent for the values clear: these are not things to be managed or changed. They’re meant to be set and left alone.

Now, we have an almost desultory test: it looks exactly like `TestMusicService3`, except for the configuration file name. We’re going to parameterize the configurations later in this chapter, and actually test *every one of our configuration files* in one single test, but let’s take a look at XML configuration and programmatic configuration of Spring, first.

```
package com.bsg6.chapter03;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
@ContextConfiguration(locations = "/config-04.xml")
public class TestMusicService4 extends AbstractTestNGSpringContextTests {
@Autowired
MusicService service;
@Autowired
MusicServiceTests tests;
@Test
public void testSongVoting() {
tests.testSongVoting(service);
}
@Test
public void testGetMatchingArtistNames() {
tests.testMatchingArtistNames(service);
}
@Test
public void testGetSongsForArtist() {
tests.testSongsForArtist(service);
}
@Test
public void testMatchingSongNamesForArtist() {
tests.testMatchingSongNamesForArtist(service);
}
}
Listing 3-29
chapter03/src/test/com/bsg6/chapter03/TestMusicService4.java
```

Our next section will recreate what we’ve just walked through, except without relying on annotations. We’re going to walk through the process of using XML to configure Spring beans and wire them together.

## Configuration Through XML

XML is not *required* to define a configuration, even though we’ve used it so far to kick-start the process of scanning for components. However, it *is* historically the most common (it’s the oldest configuration mechanism), and it also has the benefit of centralizing configuration very easily. (We can see that because in every configuration in this chapter, we’ve used XML to do component scanning.)

What we’d like to do is demonstrate the basic XML wiring capabilities, by replicating our annotation-based examples, except with XML. The only exceptions will be the test classes themselves, which will continue to use annotations for injection; this is because the `AbstractTestNGSpringContextTests` class (and its JUnit equivalent) specifically implements a process by which annotation injection is applied. Therefore, there’d be no point to explicitly using XML to wire the test class’ dependencies; the annotations are already being scanned and processed.

### Declaring a Bean with **<bean />**

To declare a component in XML, one uses the `<bean />` tag, as shown in Listing [3-25](#471370_2_En_3_Chapter.xhtml#PC25).

```
Listing 3-30
chapter03/src/test/resources/musicservicetest.xml
```

This is a simple and straightforward usage of `<bean/>`, but there are a *lot* of attributes that can be used here. We’re going to cover the most common ones in this chapter, with others covered in Chapter [4](#471370_2_En_4_Chapter.xhtml). There are more attributes than are being covered in this table, but the ones that aren’t included here are rare.

Other attributes to look for, particularly in Chapter [4](#471370_2_En_4_Chapter.xhtml), include `init-method`, `destroy-method`, `factory-method`, and `factory-bean`.

Table 3-1

Attribute list

| Attribute | Meaning | Description |
| --- | --- | --- |
| `name` | The qualified name of the bean | If no name is provided, the bean name is derived from the class name. |
| `class` | The fully qualified class of the bean | There are cases where this is actually unnecessary, but they’re rare and not covered in a book for Spring beginners. |
| `scope` | The lifetime of the bean component | This determines if `getBean()` will return the same instance on every invocation (thus keeping the component as a singleton) or if Spring will create a new instance on every call to `getBean()`. The default is the singleton mode (with the attribute value being `singleton`, if you can imagine.) If a prototype is desired, the attribute value is `prototype`. |
| `lazy-init` | When in the context’s lifecycle, a bean should be created | If set to `true`, the bean will be instantiated when it’s requested, rather than on container startup. The default is `false` so the Spring beans are created immediately and eagerly. |
| `autowire` | Controls the autowiring behavior for this bean | Spring will not autowire references created via XML by default; this setting allows programmers to fine-tune autowiring behavior. It can be set to `no`, which means "no autowiring," `byName` and `byType` which means autowiring is based on the attribute name (so `foo` will match a component with the name `foo`) or by reference type, and `constructor`, which is like `byType` except for constructor arguments. |
| `depends-on` | A list of beans that must be initialized before this one | This is a comma-separated list of dependency references that must be fully initialized before this one is initialized. This is not normally required; Spring can determine if bean `foo` depends on `bar` or not, so this is only if the default dependency mechanism fails thanks to static references or something like that. In general, if you need this, you’ve done something wrong. |

So what we see in Listing [3-30](#471370_2_En_3_Chapter.xhtml#PC30) is a simple Spring bean, called `musicServiceTests`, of type `com.bsg6.chapter03.MusicServiceTests`. It will be created as soon as the configuration is loaded (since `lazy-init` is left to the default, which is `false`), and it will be a singleton (only one instance loaded per `ApplicationContext`), since we let the `scope` to its default value, which is `singleton`.

This is, of course, the simplest type of bean to create, a bean that has no attributes to manage. It’s entirely self-contained. What happens if we want to set other attributes – as we do with our `Normalizer` instances? Well, first, let’s create a `normalizers-na.xml` configuration. We’re going to use `normalizers-na.xml` because `normalizers.xml` uses annotations for configuration, and we want to be explicit^([^(58)](#fn58)); our beans will still be named `foo` and `bar` but we want to create them explicitly. Note that the beans are the `Normalizer` classes from the `mem03` package – and we’re going to reuse `MusicService3` as well, just without the annotations being processed.

```

Listing 3-31
chapter03/src/test/resources/normalizers-na.xml
```

### Wiring Components Together with **<property />**

Now that we’ve declared a set of configurations with, well, a set of Spring beans, we need to figure out how to use these beans in our `MusicService3`; if you paid any attention to Chapter [2](#471370_2_En_2_Chapter.xhtml), of course, you know: we’re going to use `<property />` to set the references.

The `property` tag has the following attribute values and meanings:

| **Attribute** | **Meaning** | **Description** |
| --- | --- | --- |
| `name` | This is the name of the property to set | This is required |
| `value` | This is the value of the argument, which must be easily coerced into the `type` | This cannot co-exist with `ref` |
| `ref` | This is a reference to another named Spring bean, by name | This cannot co-exist with `value` |

In this case, we have two `Normalizer` references available, provided we import `normalizers-na.xml` – and we will – so we’re going to use `ref` to set the values. Here’s our `config-05.xml`, which declares a `MusicService` (with a type of `com.bsg6.chapter03.mem03.MusicService3`) and then calls `setArtistNormalizer()` and `setSongNormalizer()` with the appropriate references, looking them up by name.

```

Listing 3-32
chapter03/src/test/resources/config-05.xml
```

Of course, it’s all well and good to have nice, convenient, modular configuration files…​ but they don’t *do* anything. Astute readers know what’s coming next: a test!

This test is going to be *very* similar to our other tests, with one difference: we’re going to load multiple configuration files in our `@ContextConfiguration` annotation. (Our tests don’t use the XML configuration, nor should they, really.) Other than the usage of an *array* of configuration files, this test is identical to `TestMusicService4`. Each configuration file is loaded into a single `ApplicationContext`, so this is a handy way for us to modularize for tests, where beans like our `MusicServiceTests` don’t need to be part of the “main configuration,” our `config-05.xml` file.

```
package com.bsg6.chapter03;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
@ContextConfiguration(locations = {"/config-05.xml","/musicservicetest.xml"})
public class TestMusicService5 extends AbstractTestNGSpringContextTests {
@Autowired
MusicService service;
@Autowired
MusicServiceTests tests;
@Test
public void testSongVoting() {
tests.testSongVoting(service);
}
@Test
public void testGetMatchingArtistNames() {
tests.testMatchingArtistNames(service);
}
@Test
public void testGetSongsForArtist() {
tests.testSongsForArtist(service);
}
@Test
public void testMatchingSongNamesForArtist() {
tests.testMatchingSongNamesForArtist(service);
}
}
Listing 3-33
chapter03/src/test/com/bsg6/chapter03/TestMusicService5.java
```

### Wiring Components Together with **<constructor-arg />**

Our next example will replicate what we *just* did, but just as we saw in the series of annotation-based examples, this one will use constructor injection. Just as we did with `TestMusicService5` (which reused `MusicService3`), we’re going to reuse `MusicService4` with another configuration and another test.

First, the configuration. This one will use `<constructor-arg />` instead of `<property />`. The `<constructor-arg />` node has the following attributes available.

Table 3-2

Attribute list

| Attribute | Meaning | Description |
| --- | --- | --- |
| `type` | This is the type of the argument, if it cannot be derived from the value | Must match the type hierarchy |
| `value` | This is the value of the argument, which must be easily coerced into the `type` | This cannot co-exist with `ref` |
| `ref` | This is a reference to another named Spring bean, by name | This cannot co-exist with `value` |
| `name` | This is the name of the argument, as in the constructor’s source code | This cannot co-exist with `index` |
| `index` | This is the index of the argument being set | This cannot co-exist with `name` |

So let’s take a look at our `config-06.xml`, which does the same thing as `config-05.xml`, except with the constructor-only version of the `MusicService`.

```

Listing 3-34
chapter03/src/test/resources/config-06.xml
```

And we’d be breaking a habit if we didn’t include *yet another* test, another copy of an earlier test, except this time referring to `config-06.xml` instead of, well, anything else.

```
package com.bsg6.chapter03;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
@ContextConfiguration(locations = {"/config-06.xml","/musicservicetest.xml"})
public class TestMusicService6 extends AbstractTestNGSpringContextTests {
@Autowired
MusicService service;
@Autowired
MusicServiceTests tests;
@Test
public void testSongVoting() {
tests.testSongVoting(service);
}
@Test
public void testGetMatchingArtistNames() {
tests.testMatchingArtistNames(service);
}
@Test
public void testGetSongsForArtist() {
tests.testSongsForArtist(service);
}
@Test
public void testMatchingSongNamesForArtist() {
tests.testMatchingSongNamesForArtist(service);
}
}
Listing 3-35
chapter03/src/test/com/bsg6/chapter03/TestMusicService6.java
```

We’re now going to take a look at a *third* way to configure an `ApplicationContext`: Java configuration.

## Configuration Through Java Config

With programmatic configuration, you have a few options: one is to define a Java class and mark it as a `@Configuration`, providing methods to return Spring beans internally. This is (mostly) *static* configuration.

Another way to configure Spring programmatically is to grab an `ApplicationContext` and manually register components in it. This is *dynamic* configuration. The latter is very flexible, but uncommon, so we’re going to focus on static configuration in this section.

With static configuration, you declare a class with methods that return components; mark the methods that return Spring beans with `@Bean`, and mark the *class* with `@Configuration`, and you’re done, although of course you have the capability to fine-tune the annotations. If the methods have arguments, then Spring tries to use a resolution mechanism similar to constructor-based injection.

### Declaring Components with **@Bean**

Let’s see a simple configuration in action. This configuration will mirror what we did with the `MusicService1` in Listing [3-9](#471370_2_En_3_Chapter.xhtml#PC9) – a simple component with no dependencies.

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.mem01.MusicService1;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class Configuration7 {
@Bean
MusicService musicService() {
return new MusicService1();
}
}
Listing 3-36
chapter03/src/test/com/bsg6/chapter03/Configuration7.java
```

Our eagle-eyed readers will notice that we have `Configuration7`, leaving out configurations one through six. This is deliberate. This is the seventh configuration in this chapter, so rather than restarting the sequence, we just continued where we left off. Good eye, though, readers.

This configuration would contribute one Spring bean to an `ApplicationContext`, whose name would be derived from the method – so create a reference to a `MusicService` called `musicService`, with an instance reference of type `MusicService1`. (We’ll show how you can name the references differently in a later example.)

Let’s create another configuration class, one that creates a `MusicServiceTests` reference, which we’ll use in a test we’ll see after we walk through the other configuration possibilities.

```
package com.bsg6.chapter03;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class TestConfiguration {
@Bean
MusicServiceTests musicServiceTests() {
return new MusicServiceTests();
}
}
Listing 3-37
chapter03/src/test/com/bsg6/chapter03/TestConfiguration.java
```

### Using a Programmatic Configuration for **ApplicationContext**

We’ve seen how to load configurations with `ClassPathXmlApplicationContext` (from Chapter [2](#471370_2_En_2_Chapter.xhtml)), but that class accepts XML file locations as arguments; here, we have no XML at all. In order to use this kind of configuration, we need to use `AnnotationConfigApplicationContext`, passing it a reference to the `Class<?>` that represents the configuration. We can pass in as many configuration classes as we need. In practice, a runnable application might look something like Listing [3-38](#471370_2_En_3_Chapter.xhtml#PC38), which loads our two configuration classes and dumps every managed Spring bean’s name to the console.

```
package com.bsg6.chapter03;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class MusicServiceRunner {
public static void main(String[] args) {
Class[] configurations = new Class[]
{Configuration7.class, TestConfiguration.class
};
ApplicationContext context =
new AnnotationConfigApplicationContext(configurations);
for(String name:context.getBeanDefinitionNames()) {
System.out.println(name);
}
}
}
Listing 3-38
chapter03/src/test/com/bsg6/chapter03/MusicServiceRunner.java
```

If it’s run without changes, the output looks like the following.

```
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
configuration7
testConfiguration
musicService
musicServiceTests
Listing 3-39
Output of MusicServiceRunner
```

You can see in this output how there’s a reference to `musicService` and `musicServiceTests`. (We’re going to walk through a few more configuration examples before using this – and the rest of our configurations, *all* of them, in a test.)

### Wiring Components Together with **@Autowired** with Static Configuration

References marked with `@Autowired` are still candidates for injection, of course. What’s more, the rules for injection hold even with programmatic configuration. In the second configuration (`config-02.xml`), we had a single `Normalizer` in the configuration, automatically wired into a `MusicService2`. Here’s what that same configuration looks like, using Java for configuration instead of XML.

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.mem02.MusicService2;
import com.bsg6.chapter03.mem02.SimpleNormalizer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class Configuration8 {
@Bean
Normalizer normalizer() {
return new SimpleNormalizer();
}
@Bean
MusicService musicService() {
return new MusicService2();
}
}
Listing 3-40
chapter03/src/test/com/bsg6/chapter03/Configuration8.java
```

### Using **@Qualifier** to Select Specific Components for Wiring

For `MusicService3`, we had multiple `Normalizer` references. Again, the resolution works out exactly the same; the injected reference is qualified by name if no other candidate exists, and in the programmatic configuration, the name of the bean defaults to the name of the method that returns a given class instance. (Thus, in Listing [3-40](#471370_2_En_3_Chapter.xhtml#PC40), the beans are named `musicService` and `normalizer.`)

In our next example, we have two methods that produce `Normalizer` references: `foo()` and `capNormalizer()`. If they’re both marked solely with `@Bean`, the Spring beans’ names will be, logically enough, `foo` and `capNormalizer`, respectively, but that would yield a missing reference for the autowired attribute in `MusicService3`, which is looking for a component named `bar`.

We can name references explicitly, by using the `name` attribute in the `@Bean` annotation. Our next configuration shows this in action.

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.mem03.CapLeadingNormalizer;
import com.bsg6.chapter03.mem03.SimpleNormalizer;
import com.bsg6.chapter03.mem03.MusicService3;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class Configuration9 {
@Bean
Normalizer foo() {
return new SimpleNormalizer();
}
@Bean(name="bar")
Normalizer capNormalizer() {
return new CapLeadingNormalizer();
}
@Bean
MusicService musicService() {
return new MusicService3();
}
}
Listing 3-41
chapter03/src/test/com/bsg6/chapter03/Configuration9.java
```

### Constructor Injection with Static Configuration

Our last example corresponds to our examples that leverage constructor injection. We have a few options here: we could construct the references manually (thus, `new MusicService4(new SimpleNormalizer(), new SimpleNormalizer())`), but this violates the principle of allowing Spring to manage the references.

We can actually have the bean construction method *itself* accept arguments (with names that resolve to bean components, or set explicitly via `@Qualifier`). Spring will then do argument matching based on type or name, allowing us to use those references in the construction of the new component.

Note

This is probably the most useful and flexible of the configuration options for Spring, and we’ll likely use it quite often.

```
package com.bsg6.chapter03;
import com.bsg6.chapter03.mem03.CapLeadingNormalizer;
import com.bsg6.chapter03.mem03.SimpleNormalizer;
import com.bsg6.chapter03.mem04.MusicService4;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class Configuration10 {
@Bean
Normalizer foo() {
return new SimpleNormalizer();
}
@Bean
Normalizer bar() {
return new CapLeadingNormalizer();
}
@Bean
MusicService musicService(Normalizer bar,
@Qualifier("foo")
Normalizer baz) {
return new MusicService4(bar, baz);
}
}
Listing 3-42
chapter03/src/test/com/bsg6/chapter03/Configuration10.java
```

The advantage provided here is not only that of *flexibility* but *management*. Only one instance of a `SimpleNormalizer`, for example, is created; it’s a singleton.

### Testing Every Configuration with a DataProvider

We haven’t seen most of these configurations in use, and the only use we’ve seen is that of a poor excuse for a test. Let’s write a test that actually exercises *every* configuration we’ve presented so far. This class has some fairly significant oddities to it, so we’ll present two methods it contains and explain what these methods do, then we’ll see the entire class, with the method in context.

```
private void runMethod(Object config, Consumer method) {
ApplicationContext context;
if (config instanceof String) {
context = new ClassPathXmlApplicationContext(config.toString());
} else {
if (config instanceof Class) {
context = new AnnotationConfigApplicationContext((Class) config);
} else {
throw new RuntimeException("Invalid configuration argument: " + config);
}
}
MusicService service = context.getBean(MusicService.class);
method.accept(service);
}
@Test(expectedExceptions = RuntimeException.class)
public void testRunMethod() {
runMethod(Boolean.TRUE, tests::testSongVoting);
}
Listing 3-43
chapter03/src/test/com/bsg6/chapter03/TestMusicService10.java
```

The `runMethod()` method is internal to our test, and what it does is pretty simple but might seem odd to some readers: it accepts a configuration object of some kind as well as a method reference that accepts an `ApplicationContext`. The `config` object is looked into, to see if it’s a `Class` or a `String`; if it’s a `String`, the `ApplicationContext` is loaded from XML, and if it’s a `Class` it’s loaded from a programmatic configuration. (If it’s not one of those, an exception is thrown. Our other method tests this out.)

Once the context is loaded, it grabs a `MusicService` from the context, and then invokes the method passed in as the second argument with the `MusicService`. A failure in the method being invoked – an exception or assertion failure – is bubbled up to the caller of `runMethod()`. Therefore, we can accept a configuration of some kind – either a `String` or a `Class` – and invoke a method with a properly constructed `ApplicationContext` at will.

We’ve seen this in our `MusicServiceTests`, by the way – we’re just going one level higher and compositing method invocation twice instead of once.

So let’s take a look at the *entire* test class, `TestMusicService10.java`. It’s going to use a data provider method (called `configurations()`) to create a list composed of references to *every* configuration this chapter contains, and in every method that refers to the data provider, it will call `runMethod()` with the provided configuration reference and a method reference from `MusicServiceTests`. Any failures will show up as test failures.

```
package com.bsg6.chapter03;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.function.Consumer;
@ContextConfiguration(classes = {TestConfiguration.class})
public class TestMusicService10 extends AbstractTestNGSpringContextTests {
@Autowired
MusicServiceTests tests;
@DataProvider
Object[][] configurations() {
return new Object[][]{
{"/config-01.xml"},
{"/config-02.xml"},
{"/config-03.xml"},
{"/config-04.xml"},
{"/config-05.xml"},
{"/config-06.xml"},
{Configuration7.class},
{Configuration8.class},
{Configuration9.class},
{Configuration10.class}
};
}
// tag::runMethod[]
private void runMethod(Object config, Consumer method) {
ApplicationContext context;
if (config instanceof String) {
context = new ClassPathXmlApplicationContext(config.toString());
} else {
if (config instanceof Class) {
context = new AnnotationConfigApplicationContext((Class) config);
} else {
throw new RuntimeException("Invalid configuration argument: " + config);
}
}
MusicService service = context.getBean(MusicService.class);
method.accept(service);
}
@Test(expectedExceptions = RuntimeException.class)
public void testRunMethod() {
runMethod(Boolean.TRUE, tests::testSongVoting);
}
// end::runMethod[]
@Test(dataProvider = "configurations")
public void testSongVoting(Object config) {
runMethod(config, tests::testSongVoting);
}
@Test(dataProvider = "configurations")
public void testGetMatchingArtistNames(Object config) {
runMethod(config, tests::testMatchingArtistNames);
}
@Test(dataProvider = "configurations")
public void testGetSongsForArtist(Object config) {
runMethod(config, tests::testSongsForArtist);
}
@Test(dataProvider = "configurations")
public void testMatchingSongNamesForArtist(Object config) {
runMethod(config, tests::testMatchingSongNamesForArtist);
}
}
Listing 3-44
chapter03/src/test/com/bsg6/chapter03/TestMusicService10.java
```

Some interesting observations from this test’s successful conclusion:

1.  On the author’s machine, this test runs in 1.6 seconds, with the most significant times being spent either on warmup (as the code is first seen by the JVM). The XML-based test configurations tended to run slightly slower than the programmatic configurations (as one might expect), but even the slowest runs in around 50 milliseconds, although readers’ results may vary slightly depending on a number of factors.

2.  More significantly, we’re demonstrating rather different components, all referred to by the same interface, all used identically from the calling code. Our test runs through ten different configurations, with four fairly differentiated implementation classes, and there’s absolutely no change in how classes are acquired from the Spring context. There’s no real performance difference; there’s some time spent initializing the contexts, but it’s not significant, because most contexts are retained for far longer than a single test.

## Next Steps

In Chapter [4](#471370_2_En_4_Chapter.xhtml), we’re going to explore some of the lifecycle options in Spring, where we have even more control over how beans are created (through constructors, as we’ve done in this chapter, or factories), as well as how we invoke methods when Spring beans are created or destroyed. We’re also going to see how to create new Spring beans such that they’re not singleton objects.

Footnotes [1](#471370_2_En_3_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_3_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_3_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_3_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_3_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_3_Chapter.xhtml#Fn6_source)   [7](#471370_2_En_3_Chapter.xhtml#Fn7_source)   [8](#471370_2_En_3_Chapter.xhtml#Fn8_source)   [9](#471370_2_En_3_Chapter.xhtml#Fn9_source)   [10](#471370_2_En_3_Chapter.xhtml#Fn10_source)   [11](#471370_2_En_3_Chapter.xhtml#Fn11_source)   [12](#471370_2_En_3_Chapter.xhtml#Fn12_source)   [13](#471370_2_En_3_Chapter.xhtml#Fn13_source)   [14](#471370_2_En_3_Chapter.xhtml#Fn14_source)   [15](#471370_2_En_3_Chapter.xhtml#Fn15_source)   [16](#471370_2_En_3_Chapter.xhtml#Fn16_source)   [17](#471370_2_En_3_Chapter.xhtml#Fn17_source)   [18](#471370_2_En_3_Chapter.xhtml#Fn18_source)   [19](#471370_2_En_3_Chapter.xhtml#Fn19_source)   [20](#471370_2_En_3_Chapter.xhtml#Fn20_source)   [21](#471370_2_En_3_Chapter.xhtml#Fn21_source)  

# 4. Lifecycle

In this chapter, we’ll expand on our sample application and learn about the lifecycle options in Spring. We’ll introduce how to invoke methods when Spring beans are created or destroyed and how to do so via multiple configurable options either using the Spring XML file, annotations, or the programmatic configurations, all of which were used in Chapter [3](#471370_2_En_3_Chapter.xhtml).

## Introduction of Lifecycle

Every object has a lifecycle in the Java Virtual Machine. When an object is created, it’s got a series of initialization stages (at the class’ level, then the instance’ level), then the class’ constructor is called. When (and if) the object becomes unreachable by program code, the object might be cleaned up by the garbage collector, which might itself call specially named methods so that the object can clear out any allocated resources, a process known as finalization.^([^(59)](#fn59))

Dependency injection frameworks add extra control to the lifecycle of objects, by adding callbacks for lifecycle events or offering hooks that the dependency context will call when certain things have been accomplished. Spring also provides scope within a given context, to control whether the resources are considered to be singletons or not.

Let’s look at scope, first.

### Scope

Spring provides two basic scopes for objects in the standard `ApplicationContext``: singleton` and `prototype`.

An object set to `singleton` scope – the default – is created and destroyed **once** in a given application context. If you retrieve an object multiple times, you will receive the same object reference each time.

An object set to `prototype` scope is constructed on retrieval, and every time you get an object of this type from the context you will receive a unique object; Spring acts like a builder^([^(60)](#fn60)) in this case. You might use this scope if you have an object whose state is mutated but, once changed, the object isn’t useful for any other process.

A builder is actually an excellent example of this kind of pattern. With many instances of the builder pattern, one creates the builder, then adds data to it. When the data is considered “complete,” you’d call the `build()` method – or some analog – that yields a completed object from the builder. The builder isn’t useful for building anything else once it’s delivered what it’s built, because it contains state that’s unique to the object being built; you’d then discard the builder, and if you needed another object, you’d create a new builder to create it. In Spring terms, builders would be ideal prototypes, if you needed them.

There **are** actually other scopes available through Spring’s web module, but we’ll discuss them in Chapter [6](#471370_2_En_6_Chapter.xhtml), where they’re more appropriate.

Let’s take a look at a trivial example showing the scopes in action. We’re going to create a project for this chapter (called `chapter04`, because we’re quite original in this book), and we’re going to write a quick test that demonstrates how we can define object scopes and also what the effect of those scopes might be.

First, we need to create our directory structure, starting in the overall project directory.

```
mkdir -p chapter04/src/main/java/com/bsg6/chapter04
mkdir -p chapter04/src/test/java/com/bsg6/chapter04
mkdir -p chapter04/src/test/resources
Listing 4-1
Creating the directory structure with POSIX
```

We also need a `pom.xml` which will add a single dependency for this chapter – the `jakarta.annotation-api`, which contains an annotation or two that we’ll use to demonstrate things that can happen after object construction, later in this chapter.

```

4.0.0

com.apress
bsg6
1.0

chapter04
1.0

jakarta.annotation
jakarta.annotation-api
2.1.1

Listing 4-2
chapter04/pom.xml
```

Now it’s time to get more interesting. Let’s create an abstract class to hold a single piece of data – called `HasData` – and we’ll extend it with multiple classes embedded with our test code. We can get away with this because we’re not actually creating classes for the purpose of anything other than demonstrating lifecycle, and these aren’t public classes.^([^(61)](#fn61))

```
package com.bsg6.chapter04;
import java.util.Objects;
abstract class  HasData {
String datum = "default";
public String getDatum() {
return datum;
}
public void setDatum(String datum) {
this.datum = datum;
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof HasData)) return false;
HasData hasData = (HasData) o;
return Objects.equals(getDatum(), hasData.getDatum());
}
@Override
public int hashCode() {
return Objects.hash(getDatum());
}
}
Listing 4-3
chapter04/src/main/java/com/bsg6/chapter04/HasData.java
```

The `equals()` method is slightly more complex than it might have been, because it has to accept subclasses of `HasData` (and, in fact, it has no choice; since `HasData` is abstract, you’ll never have an actual instance of `HasData`). It’s also worth noting that IDEs can (and did) generate these methods; every method was generated automatically by IDEA. (For what it’s worth, Eclipse would have generated equivalent code; it just so happens that your authors used IDEA here.)

Why would `equals()` need to accept subclasses? Well, it really depends on the use case. Here, we have no choice but to accept things that aren’t the same class as `HasData`, because `HasData` is abstract – there will never be a valid instance of this type, unless the definition changes. However, if we’re talking about **concrete** types, then you still might want to accept subclasses – because many frameworks like Hibernate ([`https://hibernate.org`](https://hibernate.org)) will generate proxies as subclasses for entities, such that they look exactly like the entity classes but have code to interact with the database as needed. `HasData` isn’t particularly likely to become an entity for Hibernate, but it’s good practice to do the right thing when you can.

To set an object to be a prototype, we simply add the `scope` attribute to the configuration. (We’re going to show XML configuration first, as we found it to be more explicit than the annotations when it comes to fine-grained control of events.) The two values are – surprisingly – `prototype` and `singleton`, although `singleton` is the default and therefore isn’t necessary at all.

We are defining two beans – `foo` and `bar` – both of the same type, because we can retrieve objects by name – with different scopes.

```

Listing 4-4
chapter04/src/test/resources/config-01.xml
```

In our test, we have a data provider that refers to the reference names themselves, as well as a flag to tell the test whether the types are to be considered distinct or not. Our test grabs a single instance of the specified type by name, then mutates it from its original state. It then gets another instance with the same name.

If the type is specified to be a singleton, the objects should be the same **instance** (i.e., `o1 == o2`), and setting the data in one should also be reflected in the other (in other words, there’s both instance equality and data equality).

If the type is specified to be a *prototype* instead, then the objects should **not** be the same instance, and setting the data in one should leave the other in its default state.

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.UUID;
import static org.testng.Assert.*;
class FirstObject extends HasData {
}
@ContextConfiguration(locations = "/config-01.xml")
public class TestLifecycle01 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@DataProvider
Object[][] getReferences() {
return new Object[][]{
{"foo", true},
{"bar", false}
};
}
@Test(dataProvider = "getReferences")
public void testReferenceTypes(String name, boolean singleton) {
HasData o1 = context.getBean(name, HasData.class);
String defaultValue = o1.getDatum();
o1.setDatum(UUID.randomUUID().toString());
HasData o2 = context.getBean(name, HasData.class);
if (singleton) {
assertSame(o1, o2);
assertEquals(o1, o2);
assertNotEquals(defaultValue, o2.getDatum());
} else {
assertNotSame(o1, o2);
assertNotEquals(o1, o2);
assertEquals(defaultValue, o2.getDatum());
}
}
}
Listing 4-5
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle01.java
```

### Calling Constructors

In Chapter [3](#471370_2_En_3_Chapter.xhtml) we saw how we can specify constructor arguments for Spring beans, either by naming arguments or using the argument index. (The most straightforward technique is to use named arguments, if many exist, although perhaps using `@Autowired` references is even simpler. But we’re sticking to XML in this section.) Chapter [3](#471370_2_En_3_Chapter.xhtml) walks through most of this fairly well, but we’ll offer a short example here just for completeness’ sake.

```

Listing 4-6
chapter04/src/test/resources/config-02.xml
```

Note the use of the `name` attribute in the `constructor-arg` directive – it maps to the actual parameter named `initialValue`.

And now a test that utilizes that configuration.

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
import static org.testng.Assert.assertEquals;
class SecondObject extends HasData {
SecondObject(String initialValue) {
setDatum(initialValue);
}
}
@ContextConfiguration(locations = "/config-02.xml")
public class TestLifecycle02 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@Test
public void validateConstruction() {
HasData o1 = context.getBean(HasData.class);
assertEquals(o1.getDatum(), "Initial Value");
}
@Test
public void validateSecondObjectScan() {
// we can look for the instance with anything
// unique in its hierarchy.
SecondObject o2 = context.getBean(SecondObject.class);
assertEquals(o2.getDatum(), "Initial Value");
}
}
Listing 4-7
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle02.java
```

Our test here is simpler, because all we need to do is make sure the object built by Spring has the expected data value.

Note, too, how the `context.getBean()` looks for matching objects: we do two tests and look up the bean using `HasData` and `SecondObject`, and validate that the data referred to by the instance is the same (as the configuration specifies) in both cases

### Calling Methods After Construction and Before Destruction

We can also call methods after object construction and, oddly, before the context drops all references it is managing. This is done in XML with the `init-method` and `destroy-method` attributes.

The `destroy-method` is not the same as a Java finalizer, although it can serve **some** of the same roles; if the `ApplicationContext` is able to be closed (through the well-named `close()` method, first seen in `ConfigurableApplicationContext` in the class hierarchy), the context will call these methods **before the context closes**. This is not the same as finalization; if the classes have finalizers or utilize Java 9’s `Cleaner` facility, those methods will be called before garbage collection occurs.

Note that `destroy-method` only applies to Spring beans that are **singletons**. Prototypes are created by Spring and then are considered to be unmanaged, once retrieved; Spring will construct them and pass them to your code, and then forget such instances exist. Therefore, the method indicated by `destroy-method` will not be called on prototyped instances; this is **probably** all right because `destroy-method` should be rarely used in any event, as one rarely closes Spring contexts in the first place.

In other words, `destroy-method` is present to allow you to finely control when something **might** happen to a Spring bean, but it’s unlikely to be useful in ordinary development. That won’t stop us from demonstrating it, though!

For both methods, there’s a contract the methods need to follow. They can be named anything valid (since we specify which methods are to be called), but the methods must not have a return value (they have to be declared of type `void`) and they cannot have method parameters.

Here’s a configuration that shows the methods being specified.

```

Listing 4-8
chapter04/src/test/resources/config-03.xml
```

What this means is that when Spring creates the `ThirdObject`, it will call `init()` after all construction has been finished, just as if the following code snippet were run.

```
ThirdObject foo=new ThirdObject();
foo.init();
Listing 4-9
A code snippet replicating init-method
```

Our test source will define `ThirdObject` and create a static reference, called `semaphore`. (It’s not **truly** a semaphore, but it…​ could be?) This reference is set **only** by the `init` method; therefore, we can verify that the proper initialization methods are being followed. We also define a `destroy` method that sets the `semaphore` reference to null; it’s not a true semaphore, but it’s sort of acting like one for our test.

Note

A semaphore, in programming terms, is an object used to control access to resources. You might use a semaphore to indicate that an object is being used by a particular thread, so other threads should wait until the object is “available” before accessing it, for example. Java has semaphores built into its standard library in various forms, but a sort of “manual semaphore” is being used here for example’s sake only.

The actual test itself uses a `ConfigurableApplicationContext` instead of an `ApplicationContext`, because we want to explicitly close the context after we’ve retrieved an object from it. The `ApplicationContext` interface does not define `close()`, whereas the `ConfigurableApplicationContext` implements `Closeable` (as well as `ApplicationContext` and `Lifecycle`, for that matter). The method visibility is the **only** reason we’re using `ConfigurableApplicationContext` – if it weren’t for `close()` not being defined on `ApplicationContext`, it would serve well for this test.

The test method grabs a bean from the application context and then runs two simple verifications: one is that the bean **instance** looks correct (i.e., it has data matching what we expect), and the other is that the `semaphore` is populated.

We then close the context and check the `semaphore` reference again – which, if `dispose()` has been called, will be set to `null`.

```
package com.bsg6.chapter04;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
class ThirdObject extends HasData {
static Object semaphore = null;
public void init() {
semaphore = new Object();
}
public void dispose() {
semaphore = null;
}
}
@ContextConfiguration(locations = "/config-03.xml")
public class TestLifecycle03 {
@Test
public void testInitDestroyMethods() {
ConfigurableApplicationContext context
=new ClassPathXmlApplicationContext("/config-03.xml");;
ThirdObject o1 = context.getBean(ThirdObject.class);
assertNotNull(ThirdObject.semaphore);
assertEquals(o1.getDatum(), "default");
context.close();
assertNull(ThirdObject.semaphore);
}
}
Listing 4-10
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle03.java
```

### Lifecycle Listeners

There are more options for listening to lifecycle events, too: the `InitializingBean` and `DisposableBean` interfaces. These interfaces define `afterPropertiesSet()` and `destroy(),` respectively, and these methods will be called at the proper times (i.e., after construction and all properties are set, and before the context releases the reference to the bean). Functionally, it’s exactly the same as with the `init-method` and `destroy-method` attributes in the XML configuration; the main difference is that these interfaces bind you directly to Spring’s interfaces, whereas theoretically the `init-method` and `destroy-method` references do not.^([^(62)](#fn62))

As with our other tests, here’s a configuration file describing a `FourthObject`. Note the lack of lifecycle methods being described.

```

Listing 4-11
chapter04/src/test/resources/config-04.xml
```

Now, let’s see our test. The actual test itself is functionally equivalent to the `TestLifecycle03` test, with the main difference being the class type being used, which is `FourthObject` instead of `ThirdObject`. The `FourthObject` class itself implements both `InitializingBean` and `DisposeableBean`, and therefore replaces `ThirdObject’s init` and `dispose` methods with `afterPropertiesSet` and `destroy`, respectively.

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
class FourthObject extends HasData
implements InitializingBean, DisposableBean {
static Object semaphore = null;
@Override
public void afterPropertiesSet() throws Exception {
semaphore = new Object();
}
@Override
public void destroy() throws Exception {
semaphore = null;
}
}
public class TestLifecycle04 {
@Test
public void testLifecycleMethods() {
ConfigurableApplicationContext context
= new ClassPathXmlApplicationContext("/config-04.xml");
FourthObject o1 = context.getBean(FourthObject.class);
assertNotNull(FourthObject.semaphore);
assertEquals(o1.getDatum(), "default");
context.close();
assertNull(FourthObject.semaphore);
}
}
Listing 4-12
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle04.java
```

## Lifecycle with JSR-250 Annotations

Annotations provide most – but not **quite** all – features that the XML configuration provides. The main difference is in construction; most other lifecycle features are either identical (with the `InitializingBean` and `DisposableBean` interfaces) or have direct analogs (with annotations that contain method names for `init-method`, for example). Let’s step through our features, one by one, starting with component scope.

### Annotated Beans with Scopes

The scopes can be demonstrated with annotations logically enough: simply add the `@Scope` annotation with the correct scope name. Our example will be slightly more contrived than our XML example, because we need two different types such that they’re annotated differently. We’re going to use only **one** configuration file for all of the annotated lifecycle tests, though, and here it is.

```

Listing 4-13
chapter04/src/test/resources/annotated.xml
```

Here’s a working test with two annotated component types, `FifthObject` and `SixthObject`. It’s very nearly the same as our `TestLifecycle01` class, with the main differences being the component classes themselves, and we also use the component classes to look up references instead of named references.

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.config.ConfigurableBeanFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.UUID;
import static org.testng.Assert.*;
@Component
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
class FifthObject extends HasData {
}
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
class SixthObject extends HasData {
}
@ContextConfiguration("/annotated.xml")
public class TestLifecycle05 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@DataProvider
Object[][] getReferences() {
return new Object[][]{
{FifthObject.class, true},
{SixthObject.class, false}
};
}
@Test(dataProvider = "getReferences")
public void testReferenceTypes(Class clazz, boolean singleton) {
HasData o1 = context.getBean(clazz);
String defaultValue = o1.getDatum();
o1.setDatum(UUID.randomUUID().toString());
HasData o2 = context.getBean(clazz);
if (singleton) {
assertSame(o1, o2);
assertEquals(o1, o2);
assertNotEquals(defaultValue, o2.getDatum());
} else {
assertNotSame(o1, o2);
assertNotEquals(o1, o2);
assertEquals(defaultValue, o2.getDatum());
}
}
}
Listing 4-14
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle05.java
```

### Constructors with Annotated Classes

Here’s where things diverge from the XML configuration: annotated classes will be invoked with as much information as is available, and the objects will be constructed in an order that satisfies dependencies as much as possible. This mirrors things we’ve seen in Chapter [3](#471370_2_En_3_Chapter.xhtml); if a constructor has a parameter marked with `@Autowired`, Spring will look for a class that matches the parameter’s type and qualifier, if any, and inject it into the constructor call, after building a graph of dependencies internally.

In other words, if we have an object `A` that depends on an object `B` that itself requires an object `C`, Spring will first build `C`, and use it to construct `B`, and then use `B` to construct `A`.

As such, there’s not really anything to show here that wouldn’t mirror what we’ve already seen in Chapter [3](#471370_2_En_3_Chapter.xhtml) (see 3.4 for a full discussion and example of constructor injection with annotations).

### Calling Methods After Construction and Before Destruction

Here’s where things get interesting. Instead of naming methods for `init-method` and `destroy-method`, we have two annotations available to us through the `javax.annotation-api` artifact, imported from our `pom.xml` file: `@PostConstruct` and `@PreDestroy`. Methods marked with these annotations will be called at the appropriate spots in the object’s and context’s lifecycle, as we can see in the following test.

```
package com.bsg6.chapter04;
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.stereotype.Component;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
@Component
class SeventhObject extends HasData {
static Object semaphore = null;
@PostConstruct
public void initialize() throws Exception {
semaphore = new Object();
}
@PreDestroy
public void dispose() throws Exception {
semaphore = null;
}
}
public class TestLifecycle06 {
@Test
public void testInitDestroyMethods() {
ConfigurableApplicationContext context
= new ClassPathXmlApplicationContext("/annotated-06.xml");
SeventhObject o1 = context.getBean(SeventhObject.class);
assertNotNull(SeventhObject.semaphore);
assertEquals(o1.getDatum(), "default");
context.close();
assertNull(EighthObject.semaphore);
}
}
Listing 4-15
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle06.java
```

Warning

Careful readers will notice the use of `annotated-06.xml` as the configuration file. This file is identical to `annotated.xml` – with the **only** difference being the name. This isn’t something required by the test in and of itself, but if you run every test in this chapter in a single build (as with `mvn package` or `mvn test`), the `context.close()` will interfere with other tests' completions. Using a unique filename for this and for the next test (which uses `annotated-07.xml`) avoids this problem.^([^(63)](#fn63))

```
cp chapter04/src/test/resources/annotated.xml \
chapter04/src/test/resources/annotated-06.xml
cp chapter04/src/test/resources/annotated.xml \
chapter04/src/test/resources/annotated-07.xml
Listing 4-16
Copying annotated.xml for other tests
```

We also still have the callback interfaces themselves to rely on, `InitializingBean` and `DisposableBean`. The class’ annotated status changes nothing about these callbacks, as shown in yet another test.

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.stereotype.Component;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
@Component
class EighthObject extends HasData
implements InitializingBean, DisposableBean {
static Object semaphore = null;
@Override
public void afterPropertiesSet() throws Exception {
semaphore = new Object();
}
@Override
public void destroy() throws Exception {
semaphore = null;
}
}
public class TestLifecycle07 {
@Test
public void testLifecycleMethods() {
ConfigurableApplicationContext context
= new ClassPathXmlApplicationContext("/annotated-07.xml");
EighthObject o1 = context.getBean(EighthObject.class);
assertNotNull(EighthObject.semaphore);
assertEquals(o1.getDatum(), "default");
context.close();
assertNull(EighthObject.semaphore);
}
}
Listing 4-17
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle07.java
```

## Lifecycle with Java Configuration

Annotated beans are all well and good, but annotating the beans makes their configuration *global* – if you set an annotated bean’s name, it’s set for every instance of that class in the current classpath; compare that to XML, where you can have multiple beans of the same class, with different names and different property values.

We’ve demonstrated how to set beans to different scopes with XML and annotations; there’s also the ability to use a configuration class as well, and scope applies here just like it does elsewhere. The advantage of a configuration class is that you can have multiple configuration classes present on the classpath at any time, which gives you the power and cleanliness of the annotated approach, with the flexibility of the XML approach.

Let’s take one more look at a test. This one will create another object type extending `HasData` – this time named `NinthObject` – but the configuration will be stored in a local class called `Config08`. In it, there are two methods declared, `foo()` and `bar()`, and these methods are both marked with `@Bean` – which means that `foo()` will describe how to create a bean named `foo`, and `bar()` will describe how to name a bean named `bar`. We could, of course, provide annotation directives to rename the beans instead of using the method names, but that’s something we’ve already seen in Chapter [3](#471370_2_En_3_Chapter.xhtml).

Just as with our annotation example, we can add `@Scope("prototype")` to our methods, which tells Spring the appropriate scope to use for beans with that name.

We’re also changing the `@ContextConfiguration` for the test class, to refer to `Config08.class` instead of a named configuration file. Past that, though, everything looks similar (and acts the same as) what we saw in `TestLifecycle05.java` – we have a data provider that tells the test whether the class should be a singleton or not, and validates the instance references and instance data appropriately.

Here’s the test source.

```
package com.bsg6.chapter04;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Scope;
import org.springframework.test.annotation.DirtiesContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.UUID;
import static org.testng.Assert.*;
class NinthObject extends HasData {
}
@Configuration
class Config08 {
@Bean
public NinthObject foo() {
return new NinthObject();
}
@Bean
@Scope("prototype")
public NinthObject bar() {
return new NinthObject();
}
}
@ContextConfiguration(classes=Config08.class)
@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_CLASS)
public class TestLifecycle08 extends AbstractTestNGSpringContextTests {
@Autowired
ApplicationContext context;
@DataProvider
Object[][] getReferences() {
return new Object[][]{
{"foo", true},
{"bar", false}
};
}
@Test(dataProvider = "getReferences")
public void testReferenceTypes(String reference, boolean singleton) {
HasData o1 = context.getBean(reference, HasData.class);
String defaultValue = o1.getDatum();
o1.setDatum(UUID.randomUUID().toString());
HasData o2 = context.getBean(reference, HasData.class);
if (singleton) {
assertSame(o1, o2);
assertEquals(o1, o2);
assertNotEquals(defaultValue, o2.getDatum());
} else {
assertNotSame(o1, o2);
assertNotEquals(o1, o2);
assertEquals(defaultValue, o2.getDatum());
}
}
}
Listing 4-18
chapter04/src/test/java/com/bsg6/chapter04/TestLifeCycle08.java
```

## Additional Scopes

Spring, being a maze of twisty passages, all alike,^([^(64)](#fn64)) naturally adds more to scopes (the `prototype` and `singleton` scopes) than this chapter discusses – mostly because the additional scopes exist in context of Spring Web, covered in Chapter [6](#471370_2_En_6_Chapter.xhtml). The rules for scoping don’t change, but the names and reachability do; a component can be marked such that it exists as a singleton in the context of a single HTTP request, for example, or in the context of a user session. These scopes will be explored in more detail in the appropriate chapter (which is, as mentioned, Chapter [6](#471370_2_En_6_Chapter.xhtml)). Chapter [5](#471370_2_En_5_Chapter.xhtml) introduces the servlet API and a simple way of accessing a Spring context from HTTP endpoints, but that method is too simple to provide the full capability of Spring.

## Next Steps

In Chapter [5](#471370_2_En_5_Chapter.xhtml), we’re going to switch gears and talk about integrating Spring with Jakarta EE. Spring has been a driving force in the enterprise environment space since its early days, and we’ll introduce some of the terminology and APIs, as well as a fairly archaic and simple approach to integrating Spring, in preparation for something far more useful in Chapter [6](#471370_2_En_6_Chapter.xhtml).

Footnotes [1](#471370_2_En_4_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_4_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_4_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_4_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_4_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_4_Chapter.xhtml#Fn6_source)  

# 5. Spring and Jakarta EE

Spring can certainly be used in a standalone environment, but the most common environment for Spring has historically been in an enterprise environment, powering web applications and backend services in a managed server. This chapter will demonstrate some aspects of integration in a Jakarta EE container (formerly known as Java EE, or J2EE, or maybe even just “Tomcat” depending on your level of exposure, currency, and experience).

Note

Tomcat is a servlet container that represents a small part of what Jakarta EE can be – it’s actually an implementation of a specific profile of Jakarta EE. With that said, a lot of developers were (and are) unaware of the joys of Sun’s naming conventions, so they assumed Tomcat was the same as the Enterprise Edition of Java, as opposed to a useful implementation of some of its technologies.

This chapter also introduces submodules to our projects, as well as intermodule dependencies. We’re going to create a `chapter05-api` module (where we will store some classes that will be the same through the other modules), a `chapter05-anno` module (which will use annotation-based configuration), and a `chapter05-xml` module (which will use, of all things, XML for configuration). We’re not going to use a Java configuration because we wouldn’t learn anything new through the process. (We’ll be shifting to using Java-based configurations heavily once we hit Chapter [6](#471370_2_En_6_Chapter.xhtml), though.)

## Introduction to Jakarta EE

Jakarta EE is a set of specifications covering most, if not all, enterprise architectural patterns for applications written on the Java platform. For example, if you have a request/response interaction model for your application, there’s a specification that covers it – the Servlet specification. If you have a message-oriented architecture, there’s the Java Message Service. If you need a remote invocation architecture, Jakarta EE has a few specifications for that, too, starting with the Enterprise Java Bean specification. There’s even a Context and Dependency Injection (CDI) specification that looks an awful lot like Spring does.

CDI looks a lot like Spring because the Spring framework authors contributed to the specification. This is part of what makes the Java ecosystem as strong as it is, and it also contributes to how maddening the Java specifications can be. The specification groups giveth, and they taketh away. Overall, it works out to everyone’s benefit, but sometimes yields decisions that are rather frustrating.

There are, of course, reference implementations for the specifications. Glassfish ([`https://glassfish.org/`](https://glassfish.org/)) is the current implementation for the Servlet and the Java Message Service specifications, for example; Weld ([`http://weld.cdi-spec.org/`](http://weld.cdi-spec.org/)) is the reference implementation for CDI, even though it’s very arguable that Spring inspired the specification in the first place.

Note

These are “live specifications,” and information about them changes rapidly. Glassfish 7 is listed as a designated compatible implementation for Servlet 6.0, but this information can be outdated rather quickly.

Jakarta EE, incidentally, is a fairly recent name for what used to be called Java Enterprise Edition (“JavaEE”) and, before being known as “Java EE,” was “Java 2, Enterprise Edition,” or “J2EE.” In 2018, Java EE was released by Oracle to the Eclipse Foundation to be managed by the open source community, which renamed it “Jakarta EE” because of copyright issues around the name “Java.” It’s yet more branding confusion around Java – which has suffered from branding, naming, and version confusion from Day Paisley (or, as humans refer to it, “day one”), but hopefully the management of Jakarta EE by an open source community will help stabilize names and versions for the future.^([^(65)](#fn65))

What this chapter will do is show you how some basics around the most commonly used enterprise specification, the Servlet Specification, Jetty as a Servlet container. Be warned: Jakarta EE isn’t simple. Jakarta EE implementations run as nested applications inside of other applications, with consequences to classpaths and resource availability, and even *writing* about it can be confusing because there are so many different and successful approaches to solving each given problem.

This chapter is actually going to serve mostly to introduce concepts that future chapters will rely on, and illustrate a fairly archaic way of integrating Spring into Jakarta EE – mostly serving as an easy on-ramp to more complex and complete solutions.

### The Servlet API

As previously stated, the Servlet API is designed for services that follow a request/response lifecycle: a request comes in, and a response goes out. Ultimately, requests map to a single class that implements a known interface, `javax.servlet.Servlet`, but Servlets can chain (or `forward`) calls to other Servlets. The API also defines filters that can execute before or after Servlet invocation as well as listeners that can watch for events emitted by the container (like application startup or shutdown, for example).

Servlet containers establish network listeners on specific ports; how this is done is very dependent on the Servlet container in question. They typically use HTTP – the HyperText Transfer Protocol, one version of which can be found at [`https://tools.ietf.org/html/rfc2616`](https://tools.ietf.org/html/rfc2616) – but they don’t *have* to.

A Servlet has a `service()` method along with a Servlet context^([^(66)](#fn66)) and some lifecycle methods (including `init()` and `destroy()`). The `service()` method receives `ServletRequest` and `ServletResponse` references, both of which are interfaces themselves. The `ServletRequest` interface references information about the request (the protocol, attributes of the request, parameters, etc.), and the `ServletResponse` provides mechanisms by which a Servlet can build a response matching the request.

Filters can be defined in a manner similar to Servlets; there’s a general `javax.servlet.Filter` interface with a single primary entrypoint (called `doFilter()`, of all things, although `Filter` has other methods associated with filter lifecycles); this method receives the request and response objects created by the Servlet container, as well as a `FilterChain` reference. The filter can do almost anything it wants with the request and response, although usually filters will either set up data for delegated services, or decorate responses.

Again, most Servlets work with HTTP. As a protocol, HTTP maps Uniform Resource Locators – URLs – to data. HTTP also specifies verbs in relation to these URLs, such as `GET`, `POST`, `DELETE`, and `HEAD`. (There are others; check the specification^([^(67)](#fn67)) for the full list.)

Each HTTP verb has implicit semantic meanings.

Roy T. Fielding wrote a dissertation back in 2000 called “Architectural Styles and the Design of Network-based Software Architectures.” You can find it at [`www.ics.uci.edu/~fielding/pubs/dissertation/top.htm;`](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm%3B) it describes an architectural approach called “REST,” for “Representational State Transfer.” REST is a *big deal* in HTTP-based applications; it took some of the implications of the HTTP protocol and formalized them. If you **really** want to know how to use the HTTP verbs and how URLs work on the modern web, check out REST – which we’ll be using to create our application services in Chapters [6](#471370_2_En_6_Chapter.xhtml) and [7](#471370_2_En_7_Chapter.xhtml).

*   `GET` is a request for a resource whose location is known, and URLs used with `GET` are traditionally able to be bookmarked. This is by far the most common type of HTTP request.

*   `POST` is a request to store data into a known location (although how this location is determined depends on the implementation).

*   `PUT` is another type of storage request, although where `POST` means “store,” `PUT` implies “store or update if the object already exists.”

*   `HEAD` means to return data **about** the resource without returning the resource itself; this can be used to determine if a resource has been updated, for example.

*   `DELETE` requests that a resource referred to by the URL is removed somehow.

One usually speaks of the verbs in context of the URL itself; thus, one might say to issue a `GET http://localhost/foo/bar` – or, in the context of a specific host already, you might say to simply `GET /foo/bar`.

In `HttpServlet`, the `service()` method is overridden to dispatch the request type to methods specifically named for each verb, and with parameter types that cater to HTTP^([^(68)](#fn68)); thus, `GET` in HTTP is handled by the `doGet()` method, `POST` in HTTP is handled by the `doPost()` method, and so forth and so on.

It’s useful to understand how Servlets work and what they are actually doing, in order to make future endpoints easier to conceptualize. We’re eventually going to leverage resource handlers provided by Spring itself, and those handlers will dispatch requests into methods of our choosing, with arguments parsed by the framework and not manually as we’ll be doing in this chapter.

### Modern Web Application Design Principles

It used to be that web applications were built to be monolithic, or “complete”: they would include all static resources necessary for server-side rendering of content, for example. This hasn’t been the preferred practice for quite some time now, with the use of rich clients being very much the norm. A web application will refer to Javascript libraries like JQuery or Vue.js that request resources in JSON or XML,^([^(69)](#fn69)) and render that content appropriately.

There’s nothing preventing coders from using the older, monolithic approach, and there’s nothing inherently wrong with it, although it’s difficult to say whether there are any actual advantages to rendering a full page instead of sending data client-side to be rendered on-demand. The server-side rendering means that it’s nicer on lower-powered clients, as long as they have bandwidth to spare; the client-side rendering is better on bandwidth but uses more CPU power on the browser’s machine. (In practice, users have idle CPU cycles to use for client-side rendering, and a busy server is a more finite resource, so this approach makes a lot of cost-effective sense, although some applications’ needs may vary.)

For the purposes of this book, we’re going to use the modern approach, because it allows us to focus on the server-side technologies in use, rather than how rich clients work. We’ll use simple command line tools to issue HTTP requests where necessary, instead of using Javascript embedded in a web page.

## Module Structure

We’re going to create three modules in this chapter: `chapter05-api`, `chapter05-anno`, and `chapter05-xml`. In most builds, these would be grouped under a `chapter05` module whose sole purpose would be to contain the other modules, but that only adds another nested directory and a `pom.xml` file, so there’s no tangible benefit.

Previous editions of this book used Gradle, which prefers wider, flatter module structures. Maven handles more deeply nested modules better than Gradle, but in this case, there’s no point and nesting further only makes the filesystem path references longer.

### The Common Module

Our first module will be a simple API module. It will contain two Servlets, matching services that we’ll want in our music gateway application.^([^(70)](#fn70))

To create our directory structure, we need to create `chapter05-api/src/main/java/com/bsg6/chapter05`:

```
mkdir -p chapter05-api/src/main/java/com/bsg6/chapter05
Listing 5-1
Creating the directory structure with a UNIX shell
```

We’ll also want a `pom.xml`:

```

4.0.0

com.apress
bsg6
1.0

chapter05-api
1.0

${project.parent.groupId}
chapter03
${project.parent.version}

com.fasterxml.jackson.core
jackson-databind

jakarta.servlet
jakarta.servlet-api
6.0.0
provided

Listing 5-2
chapter05-api/pom.xml
```

This is all fairly straightforward, although we’re adding a resource for the Servlet API, set to `provided`, which means that it’s available for the compiler (we can compile using classes from the Servlet API, which is rather important when compiling Servlets), but it’s not a transitive dependency. It *shouldn’t* be a transitive dependency, remember; Servlets run in a container like Jetty or Tomcat, which will have its own copy of the Servlet API, so our application should actually make sure that it has no dependency that would duplicate what the container would provide.

We also have a transitive dependency on the `chapter03` module. The transitive dependency means that a dependency on `chapter05-api` carries with it another dependency on `chapter03` – as well as dependencies on anything else that `chapter05-api` or `chapter03` depend on.^([^(71)](#fn71)) We are going to use one of the `MusicService` implementations from Chapter [3](#471370_2_En_3_Chapter.xhtml) in some of our examples here, because we don’t want to have to rebuild a working example when we have fully working interfaces and implementations already written.

This way, we can have an intermodule dependency without having to copy our module outputs into a known repository.

We also have a dependency on `jackson-databind`; Jackson ([`https://github.com/FasterXML/jackson`](https://github.com/FasterXML/jackson)) is a popular library for converting Java objects to various formats like XML, JSON, CSV, and others. We’re explicitly including it here because our base servlet will use it; in practice, Spring has a transitive dependency on it already, so we wouldn’t have to include it, but this library doesn’t have a dependency that includes Jackson; therefore, we *do* need to include it in order to use it. In practice, this approach is fairly primitive, but it’s useful for learning how things work in practice.

Now it’s time to see our Servlets. Both of them will have the exact same structure:

1.  Grab a Spring application context from the Servlet context.

2.  Get a `MusicService` from the Spring application context.

3.  Create an `ObjectMapper` to prepare to generate JSON output.

4.  Get Servlet parameters from the `HttpServletRequest.`

5.  Validate parameters.

6.  Generate output from the `MusicService`, using Jackson’s `ObjectMapper.writeValueAsString()` method to convert to JSON.

The first Servlet is the `VoteForSongServlet`. We won’t get a chance to see this in action until we finish either the `anno` or `xml` modules, but note the `@WebServlet(urlPatterns="/vote")`, which tells us part of the URL this Servlet will be attached to. (The other parts of the URL are the protocol, host, the port the server is listening on, and the application name itself – so when we run the `anno` project, by default, this Servlet will be available at `http://localhost:8080/anno/vote`.)

```
package com.bsg6.chapter05;
import com.bsg6.chapter03.MusicService;
import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.context.ApplicationContext;
import java.io.IOException;
@WebServlet(urlPatterns = "/vote")
public class VoteForSongServlet extends HttpServlet {
@Override
public void doGet(HttpServletRequest req, HttpServletResponse resp)
throws IOException {
ApplicationContext context = (ApplicationContext) req
.getServletContext()
.getAttribute("context");
MusicService service = context.getBean(MusicService.class);
ObjectMapper mapper = new ObjectMapper();
String artist = req.getParameter("artist");
String song = req.getParameter("song");
if (artist == null || song == null) {
log("Missing data in request: requires artist and song parameters");
resp.setStatus(500);
} else {
log("Voting for artist " + artist + ", song " + song);
service.voteForSong(artist, song);
resp.setStatus(200);
resp.getWriter().println(
mapper.writeValueAsString(service.getSong(artist, song))
);
}
}
}
Listing 5-3
chapter05-api/src/main/java/com/bsg6/chapter05/VoteForSongServlet.java
```

Warning

This approach, using `@WebServlet`, is very much a matter of our writing directly to the Servlet API. In Spring, there are better ways to accomplish this, and we’ll see them very shortly as we progress. In practice, using `@WebServlet` like this is very rare for application developers.

Note how we get the `ApplicationContext`. A `ServletRequest` has a `ServletContext` associated with it by the container; we’re going to store a reference to an `ApplicationContext` as an attribute into the `ServletContext`. The two Servlets we’re showing here are going to grab the Spring context from the Servlet context.

Our next Servlet – the `GetSongsForArtistServlet` – follows the exact same pattern.

```
package com.bsg6.chapter05;
import com.bsg6.chapter03.MusicService;
import com.bsg6.chapter03.model.Song;
import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.context.ApplicationContext;
import java.io.IOException;
import java.util.List;
@WebServlet(urlPatterns = "/songs")
public class GetSongsForArtistServlet extends HttpServlet {
@Override
public void doGet(HttpServletRequest req, HttpServletResponse resp)
throws IOException {
ApplicationContext context = (ApplicationContext) req
.getServletContext()
.getAttribute("context");
MusicService service = context.getBean(MusicService.class);
ObjectMapper mapper = new ObjectMapper();
String artist = req.getParameter("artist");
if (artist == null ) {
log("Missing data in request: requires artist parameter");
resp.setStatus(500);
} else {
List data=service.getSongsForArtist(artist);
resp.setStatus(200);
resp.getWriter().println(
mapper.writeValueAsString(data)
);
}
}
}
Listing 5-4
chapter05-api/src/main/java/com/bsg6/chapter05/GetSongsForArtistServlet.java
```

Fascinating stuff – and archaic – but it’s necessary in order to save ourselves from copying source files back and forth. It’s time we looked at an actual web application, first with a non-Spring-related “Hello, World” Servlet, and then we’ll see how we can create a Spring context for use by our `common` module’s Servlets.

Now let’s see how we can execute our servlets.

### The Annotation-Based Web Application

Let’s see how we can do two things: one, build a working web application, and two, use a Spring context that programmatically scans packages for Spring-annotates classes, for use by our Servlets. This application is going to be called `anno`, not because we’re fond of Latin words for periods of time, but because it’s much shorter to type than “annotation” or other variants that might be more descriptive.

Let’s write our “Hello, World” Servlet first, because it allows us to get all of our pieces in place to integrate Spring for our second and third Servlets.

Maven has a package type that is designed to create artifacts designed for web containers, called “web archives,” or – more colloquially – “wars.”^([^(72)](#fn72)) It **also** has a series of convenient plugin to run a web application inside a container. which we’ll see in a few paragraphs.

First, we need to create the `chapter05-anno` directory itself.

```
mkdir -p chapter05-anno/src/main/java/com/bsg6/chapter05
mkdir -p chapter05-anno/src/main/resources
Listing 5-5
Creating the directory structure with POSIX
```

The source layout is nearly identical to our other projects’ source layouts.

Our `pom.xml` is where things start to get interesting. Note the addition of the extra things in the `plugins` section, and note also the additional dependencies:

*   The Servlet API itself, as in the `chapter05-api` module.

*   See also `spring-web` for web-application-specific bits of Spring.

*   A templating library, Mustache. ^([^(73)](#fn73))

*   A dependency on the code from Chapter [3](#471370_2_En_3_Chapter.xhtml) which we’ll use to do the actual work in this chapter.

*   Lastly, the `jetty-maven-plugin` plugin, which gives Maven a convenient way to run Jetty as a Servlet container with our application deployed in it.

```

4.0.0

com.apress
bsg6
1.0

chapter05-anno
1.0
war

${project.parent.groupId}
chapter05-api
${project.parent.version}

jakarta.servlet
jakarta.servlet-api
6.0.0
provided

org.springframework
spring-web

com.samskivert
jmustache
1.15

org.eclipse.jetty
jetty-maven-plugin
11.0.15

Listing 5-6
chapter05-anno/pom.xml
```

#### Our First Standalone Working Servlet

Now it’s time for us to create our first *working* Servlet^([^(74)](#fn74)) – an endpoint that will accept a request from a web browser and generate a response for it. It’s not very long, but it shows us how to accept a `GET` request (again, the most common request type) from a browser, and how to render output via Mustache (a skill that’s useful to have, but not one that’s horrendously useful for this chapter).

```
package com.bsg6.chapter05;
import com.samskivert.mustache.Mustache;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Map;
import java.util.Objects;
@WebServlet(urlPatterns = "/hello1")
public class FirstHelloServlet extends HttpServlet {
@Override
protected void doGet(HttpServletRequest request,
HttpServletResponse response)
throws IOException {
try (var in = Objects.requireNonNull(this
.getClass()
.getResourceAsStream("/hello.html"))) {
try (var reader = new InputStreamReader(in)) {
var output = Mustache.compiler().compile(reader)
.execute(Map.of("name", "world"));
response.getWriter().println(output);
}
}
}
}
Listing 5-7
chapter5-anno/src/main/java/com/bsg6/chapter05/FirstHelloServlet.java
```

In this Servlet – identified with an annotation, and available at a relative URL path of `/hello1` – we have a lot of boilerplate to handle imports. We also have a `doGet()` method – which handles `GET` requests over HTTP – which does nothing more than render a Mustache template (held in the classpath at `/src/main/resources/hello.html`, shown in our next listing) with a variable `name` that has a static value of `"world."`

As promised, let’s take a look at our Mustache template.

```

Hello, {{ name }}

Hello, {{ name }}!

Listing 5-8
chapter05-anno/src/main/resources/hello.html
```

Without going too far into too much detail, this template is simply an HTML5 document, with placeholders for a value called `name`. The renderer (Mustache, of course) will replace `{{ name }}` with the value of `name` passed into the `render()` method, if any.

All this is well and good, but it’s a little bit abstract: how do we *run* it? How can we get our Servlet to actually achingly and finally greet the world, which surely has been waiting with bated breath?^([^(75)](#fn75))

We do it with Maven and the afore-mentioned `jetty-maven-plugin` plugin, of course, by executing the following command in the top-level project directory, the one that contains `chapter03` and `chapter05`:

```
$ mvn -pl chapter05-anno jetty:run
Listing 5-9
Running the chapter05-anno project
```

After some churning to download the appropriate resources and compile our Servlet, Maven informs us that – among other things – Jetty is running on port 8080\. Our application, such that it is, is available at `http://localhost:8080/`.

```
[INFO] Started ServerConnector@5238896f{HTTP/1.1, (http/1.1)}{0.0.0.0:8080}
[INFO] Started Server@236ae13d{STARTING}[11.0.15,sto=0] @3218ms
[INFO] Automatic redeployment disabled, see 'mvn jetty:help' for more redeployment options
Listing 5-10
Relevant logs from the chapter05-anno project
```

If you recall the `@WebServlet` annotation we used on `FirstHelloServlet`, you’ll remember that we have a relative URL path of `/hello1`. This is added to the web application’s root URL – which is, as said, `http://localhost:8080/` – giving our Servlet an endpoint of `http://localhost:8080/hello1`. If we open up a web browser and go to that location, we will be showered with glory and praise as our browser window triumphantly displays `Hello, world` and all of our dreams come true.

![](images/471370_2_En_5_Chapter/471370_2_En_5_Fig1_HTML.jpg)

A screenshot of a web browser window displays a webpage hosted on localhost colon 8080 slash hello 1\. The main area of the page depicts the text Hello, World exclamation mark.

Figure 5-1

Hello, world!

We can also test with a command line application, `curl`,^([^(76)](#fn76)) which we’ll be doing for our other Servlets.

```
$ curl http://localhost:8080/hello1

Hello, world

Hello, world!

$
Listing 5-11
The content from the application
```

It’s exciting stuff, but as with other “Hello, World” mechanisms, it mostly makes sure all of the plumbing is in place so that we can start working on more interesting bits.

#### Adding a Spring Context for Our Servlets

There are a few ways to use Spring in Servlets: we could use a Spring module like Spring Web (which has a Servlet that serves as a dispatcher to service objects written with Spring in mind,^([^(77)](#fn77)) and which we’re actually including as a dependency here), or Spring Boot (which actually has its own Servlet engine embedded), or we could have a web application instantiate a Spring context and access it as a resource from within traditional Servlets. Most Spring experts would probably lean toward Spring Boot, with Boot’s trivial development and deployment model, but Spring Boot is a subject for Chapter [7](#471370_2_En_7_Chapter.xhtml), not Chapter [5](#471370_2_En_5_Chapter.xhtml), and Spring Web is a subject for later chapters (Chapter [6](#471370_2_En_6_Chapter.xhtml)) as well…​ which means we get to get our feet wet with more basic approaches.

When we look at Spring Boot and Spring Web (again, Chapters [6](#471370_2_En_6_Chapter.xhtml) and [7](#471370_2_En_7_Chapter.xhtml), respectively) we’ll find simpler ways to accomplish what we’ll see here – but this chapter will give us insight into what’s happening behind the scenes in the later chapters, too.

So now that we’ve talked about this being a “basic approach,” and so forth – what **is** the approach?

We’re going to add a `ServletContextListener` to our two web applications, and in this `ServletContextListener` we’ll instantiate a Spring `WebApplicationContext` and store it in the application scope for the entire web application. When we need resources from the Spring context, we’ll grab then from the `ServletContext` – somewhat like the traditional JNDI model in Jakarta EE, as we saw in our Servlets in the `common` module. (When we look at Spring Web and Spring Boot, we’ll see easy ways to autowire dependencies.)

Let’s look at the `ServletContextListener`, which we’re going to name `AnnotationContextListener`.

```
package com.bsg6.chapter05;
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;
import org.springframework.context.ApplicationContext;
import org.springframework.web.context.
support.AnnotationConfigWebApplicationContext;
@WebListener
public class AnnotationContextListener implements ServletContextListener {
@Override
public void contextInitialized(ServletContextEvent event) {
ApplicationContext context = buildAnnotationContext();
event.getServletContext().setAttribute("context", context);
}
private ApplicationContext buildAnnotationContext() {
AnnotationConfigWebApplicationContext context =
new AnnotationConfigWebApplicationContext();
context.scan("com.bsg6.chapter03.mem03");
context.refresh();
return context;
}
}
Listing 5-12
chapter05-anno/src/main/java/com/bsg6/chapter05/AnnotationContextListener.java
```

For the most part, this class is very simple. It uses `@WebListener` on the entire class – which tells the Servlet container to use an instance of this class **where appropriate** – and, given that it implements `ServletContextListener`, it will receive context-related events. There are only two: `contextInitialized` (called when the application is started) and `contextDestroyed` (called when the application is destroyed, if possible).

The `ServletContextListener` interface has default implementations for both `contextInitialized()` and `contextDestroyed()`, so we don’t have to implement anything unless we actually have operations to perform. We don’t have anything to do if the Servlet context stops, so we’re not going to bother with an implementation of `contextDestroyed()`. However, we want to create our Spring context in the `contextInitialized` event.

The `AnnotationConfigWebApplicationContext`, as its name implies, provides us the ability to scan for available components, as with the `<context:component-scan />` tag we’ve seen in earlier chapters. With this class, we can programmatically tell the Spring context which packages to scan for available annotations, which is exactly what the `buildAnnotationContext()` method does.^([^(78)](#fn78)) Once we have the `AnnotationConfigWebApplicationContext`, we need to tell it where to look to find candidate classes – and in this case, we’re reusing one of the Chapter [3](#471370_2_En_3_Chapter.xhtml) memory-based implementations, so we provide it a single package name, `com.bsg6.chapter03.mem03`. This method actually accepts an array of package names, and it actually takes a variable number of arguments; it just so happens that we only need to scan one package for our purposes here, but we could have provided a comma-delimited list of as many packages as we needed scanned.

Note that this isn’t quite the same as a Java configuration. If we were using a Java configuration instead of building our configuration, we could have marked with it `@ComponentScan` – which would have done the same thing as we’re doing here with `@AnnotationConfigWebApplicationContext` and `<context:component-scan />`. We’ll see it used in later chapters, when we actually do switch over to using Java-based configuration.

It’s usually wise to scan *exactly* what you need, rather than adding entire trees of packages. Scanning is fairly slow; you won’t do it often (only on application startup), but it’s still fairly heavy on the Java runtime. If you don’t have a lot of classes you’re actually interested in, consider programmatic configuration with a class annotated with `@Configuration` rather than scanning packages. We’re scanning here mostly because it’s fewer lines of code than the Java configuration, which requires a class with its own boilerplate.

However, simply telling the context where it **should** scan doesn’t make it actually perform the scan. That’s the role of `context.refresh()`. After we’ve done **that**, we get the `ServletContext` from the `ServletContextEvent`, and store the Spring context into the Servlet context as a named attribute^([^(79)](#fn79)); we name it “context” for simplicity. Once we’ve done that, any executable code in the entire web application can grab the Spring context from the Servlet context, by name.

When we use the Spring context in this manner (with an explicit load of the Spring context into the Servlet context), we don’t get autowiring of Spring resources into our Servlets. We get them from the Spring context instead. (The beans that are retrieved **from** the Spring context, however, will have autowiring in place – and the particular `MusicService` we use requires and demonstrates this.) Since we have full control over instance lifecycle (see Chapter [4](#471370_2_En_4_Chapter.xhtml)), we have fine-grained control over what gets created, and when.

All that’s incredibly useful^([^(80)](#fn80)); we can now grab our `MusicService` from Spring, as shown in the Servlets from the `common` module, but how do we demonstrate it?

By doing the same thing we did with our “Hello, World” Servlet, of course.

Our `chapter05-anno` application includes the `chapter05-api` module, as we’ve shown (and mentioned multiple times, in case readers weren’t paying attention). When we include the `chapter05-api` module, those Servlets are automatically set to respond to the URL patterns specified by the `@WebServlet` annotation – which means `/vote` and `/songs`. That means when we run our `anno` application with `jetty:run`, those Servlets are already active – although they aren’t going to work correctly unless we have our `ServletContextListener` in place.

We can test this by using a command line application such as `curl`. After starting the application, issue a curl command.

```
$ curl "http://localhost:8080/vote?artist=Therapy+Zeppelin&song=Medium"
{"name":"Medium","votes":1}
$ curl "http://localhost:8080/vote?artist=Therapy+Zeppelin&song=Medium"
{"name":"Medium","votes":2}
$ curl "http://localhost:8080/songs?artist=Therapy+Zeppelin"
[{"name":"Medium","votes":2}]
Listing 5-13
Testing the application manually
```

Wise readers might wonder why we’re not testing this automatically (as part of the build process), as we’ve done elsewhere in the book. The answer is pretty simple: it’s hard to do! Cranking up a compliant Servlet container in a test is doable, but the best way to do it is with something like Arquillian ([`https://arquillian.org/`](https://arquillian.org/)) – which is unfortunately not trivial to explain. There’s a ton of plumbing that goes into recreating the proper infrastructure, and it’s largely outside the scope of this book. Plus, this form of building applications is, as already said multiple times, a bit archaic – our next chapter will use Spring Web, which not only makes our endpoints far easier to write, but it also makes them inherently *testable* without all of the effort we’d have to go through for *this* chapter’s rather basic technique.

We promise, we’re not trying to short-change you, readers! We’re actually just not wasting effort showing you a testing process you’re not really going to need, but you *will* want to have a basic understanding of what the Servlet API is and how it works, in order to construct decent endpoints for your own applications. Check out the next chapter. It touches on all of this, and does it properly, and then in later chapters we do it *even better*.

### The XML-Based Spring Context Application

Here, everything is **almost** identical to our annotation-based application – which was, of course, the whole point of creating the `chapter05-api` module. We’re not going to bother with the “Hello, World” Servlet, so this web application will contain our `ServletContextListener` and an XML configuration file (along with the Servlets we wrote in our `common` module). With our instructions for `pom.xml`, we have four files.

First, the directory structure, starting from the `chapter05-xml` directory itself.

```
mkdir -p chapter05-xml/src/main/java/com/bsg6/chapter05
mkdir -p chapter05-xml/src/main/webapp/WEB-INF
Listing 5-14
Creating the directory structure with POSIX
```

Our `pom.xml` is effectively identical to the `pom.xml` from the `chapter05-anno` module, with the only real difference being that we no longer need Mustache (as the “Hello, World” Servlet was the only class where Mustache was used).

```

4.0.0

com.apress
bsg6
1.0

chapter05-xml
1.0
war

${project.parent.groupId}
chapter05-api
${project.parent.version}

jakarta.servlet
jakarta.servlet-api
6.0.0
provided

org.springframework
spring-web

org.eclipse.jetty
jetty-maven-plugin
11.0.15

Listing 5-15
chapter05-xml/pom.xml
```

The `ServletContextListener` creates an `XmlWebApplicationContext` instead of an `AnnotationConfigWebApplicationContext`, which means we’ll need to have an XML file in the application, but apart from that, everything should be very familiar:

```
package com.bsg6.chapter05;
import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;
import org.springframework.context.ApplicationContext;
import org.springframework.web.context.support.XmlWebApplicationContext;
@WebListener
public class XMLContextListener implements ServletContextListener {
@Override
public void contextInitialized(ServletContextEvent event) {
ApplicationContext context =
buildXmlContext(event.getServletContext());
event.getServletContext().setAttribute("context", context);
}
private ApplicationContext buildXmlContext(ServletContext sc) {
XmlWebApplicationContext context = new XmlWebApplicationContext();
context.setServletContext(sc);
context.refresh();
return context;
}
@Override
public void contextDestroyed(ServletContextEvent sce) {
}
}
Listing 5-16
chapter05-xml/src/main/java/com/bsg6/chapter05/XMLContextListener.java
```

Note that when we build an `XmlWebApplicationContext,` we also set a reference to the current `ServletContext`. This is primarily used in different environments than the one we’re creating; it’s useful when Spring dispatches requests to resources itself, which we’re not using here. When we get into Chapter [6](#471370_2_En_6_Chapter.xhtml), this sort of thing will happen behind the scenes and we won’t have to worry about it, but here it’s just making sure the Spring Context is set up as it expects.

And, of course, we need the configuration file itself.

```

Listing 5-17
chapter05-xml/src/main/webapp/WEB-INF/applicationContext.xml
```

This is a near-exact copy of one of the XML configurations from Chapter [3](#471370_2_En_3_Chapter.xhtml), just renamed to match the default location searched by `XmlWebApplicationContext`. (We can provide a resource to the context to read its configuration, but by default it searches the web application’s local resources – in this case, `/WEB-INF/aplicationContext.xml`, as shown.)

If we run the `xml` module with Maven, via `mvn -pl chapter05-xml jetty:run`, we can exercise the application in the exact same technique we used with the `chapter05-anno` version.

```
$ curl "http://localhost:8080/vote?artist=Therapy+Zeppelin&song=Medium"
{"name":"Medium","votes":1}
$ curl "http://localhost:8080/vote?artist=Therapy+Zeppelin&song=Medium"
{"name":"Medium","votes":2}
$ curl "http://localhost:8080/songs?artist=Therapy+Zeppelin"
[{"name":"Medium","votes":2}]
Listing 5-18
Testing the application manually
```

Our excitement should know no boundaries! But seriously, we’ve actually done a lot so far in this chapter. It just so happens that Spring provides easier ways to accomplish the same things, which we’ll see in the next chapter.

## Next Steps

In this chapter, we saw what goes on behind the curtains in a web application, with raw Servlets, template rendering with Mustache, and resource requisition from manually populated Spring contexts. We also got to see how modules in Maven can refer to each other. Chapter [6](#471370_2_En_6_Chapter.xhtml) will show us how to build a more fully featured band gateway application, by removing all of the manual invocations and conversions (and Servlets) from our application, leading us to a more streamlined and modern web application development process, including automated testing.

Footnotes [1](#471370_2_En_5_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_5_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_5_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_5_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_5_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_5_Chapter.xhtml#Fn6_source)   [7](#471370_2_En_5_Chapter.xhtml#Fn7_source)   [8](#471370_2_En_5_Chapter.xhtml#Fn8_source)   [9](#471370_2_En_5_Chapter.xhtml#Fn9_source)   [10](#471370_2_En_5_Chapter.xhtml#Fn10_source)   [11](#471370_2_En_5_Chapter.xhtml#Fn11_source)   [12](#471370_2_En_5_Chapter.xhtml#Fn12_source)   [13](#471370_2_En_5_Chapter.xhtml#Fn13_source)   [14](#471370_2_En_5_Chapter.xhtml#Fn14_source)   [15](#471370_2_En_5_Chapter.xhtml#Fn15_source)   [16](#471370_2_En_5_Chapter.xhtml#Fn16_source)  

# 6. Spring Web

In previous chapters, we’ve looked mostly at dependency injection – easily the “secret sauce” that made Spring so popular and useful – as well as looking into the beginnings of what Spring can do for web development. In this chapter we will dive into the Spring MVC library within the framework and continue to build on our band gateway application using this frontend library. The application will become more full-featured and remove all manual invocations and conversions with automated testing.

## Introduction to Spring MVC

Spring Web is a framework that provides a Model-View-Controller (MVC) architecture to develop applications with Spring for the Web. This focuses developer attention on what their application needs to do to provide access over HTTP, as opposed to *how* access is provided. For example, in our previous chapter, we built out several Servlets running within the Spring framework, but aside from the beans and other management aspects you get baked into Spring, we were still using regular Servlets. Nothing stops you from doing this; however as we’ll hopefully be able to show, using this module will save you loads of time by using a built-in Spring dispatcher to *controller methods* instead of having to write your own endpoints.

## MVC

The web module of Spring organizes components using a paradigm known as MVC – as already mentioned, an acronym that stands for “Model-View-Controller.” The architecture can be used to develop applications that are more flexible and loosely coupled than what we saw in the previous chapter. (We also get to stop managing the configuration so carefully.) We can easily separate the business logic, and input and frontend logic into separate components and use Spring to wire them together.

In MVC, a **model** holds the data for our application which allows us to separate our business logic from how we deal with the data. In the Java world, this is a POJO or Plain Old Java Object, which we’re all pretty used to, at this point.

The **view** is responsible for taking model data and showing it in some way to the front end. In this chapter we will show renderers for both HTML and JSON. With HTML we’ll be focused on rendering templates using a more human-readable format, and with JSON will be showing the result of an API call.

The **controller** is used to interface with the user. In a web-based interaction, this consists of user requests, form submissions, and what view to show from a business logic perspective. It is also responsible for how the model gets passed around.

## Hello, World with MVC

In this chapter we’re going to return to a simple module structure, with a dependency on Chapter [3](#471370_2_En_3_Chapter.xhtml).

First, we need to create our directory structure, starting in the overall project directory.

```
mkdir -p chapter06/src/main/java/com/bsg6/chapter06
mkdir -p chapter06/src/main/webapp/WEB-INF/templates
mkdir -p chapter06/src/test/java/com/bsg6/chapter06
Listing 6-1
Creating the directory structure with POSIX
```

As with previous chapters, we will need our Maven configuration file, `pom.xml`. There’s nothing too special about it other than the addition in `dependencies` of our `spring-web` and `spring-webmvc`. The rest has been dutifully copied from previous incarnations in `chapter05` and earlier which gave us the ability to run a Jetty instance with a simple Maven target.

```

4.0.0

com.apress
bsg6
1.0

chapter06
1.0
war

ch.qos.logback
logback-classic

${project.parent.groupId}
chapter03
${project.parent.version}

jakarta.servlet
jakarta.servlet-api
6.0.0
provided

org.springframework
spring-web

org.springframework
spring-test

org.springframework
spring-webmvc

com.samskivert
jmustache
1.15

com.fasterxml.jackson.core
jackson-databind

org.hamcrest
hamcrest
2.2
test

org.eclipse.jetty
jetty-maven-plugin
11.0.15

Listing 6-2
chapter06/pom.xml
```

In previous chapters we’ve gone into some depth on the XML configuration, so in this chapter and subsequent ones we’ll forego that and focus on annotations for all our instrumentation needs.

Why?

The Java-based configuration is much easier and more standard across the Java ecosystem, and we tend to like removing verbosity if it doesn’t add anything of value. The XML is **useful**, but what we’ve seen in the field (and what we’ve experienced, personally) is that *most* programmers are more comfortable *programming*, and the configuration being part of code means an easier selection of configuration via the Java classpath.^([^(81)](#fn81))

Let’s take a look at what a simple configuration and MVC endpoint looks like. In the following listing, we’ll take a simple controller which will output a common tutorial phrase to the end user once the endpoint is hit.

```
package com.bsg6.chapter06;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
@Controller
public class GreetingController {
@GetMapping(
path = "/greeting",
produces = {MediaType.TEXT_PLAIN_VALUE}
)
public ResponseEntity greeting() {
return new ResponseEntity(
"Hello, World!",
HttpStatus.OK
);
}
}
Listing 6-3
chapter06/src/main/java/com/bsg6/chapter06/GreetingController.java
```

There is a fair bit happening in the snippet that we haven’t talked about, so let’s unpack it. We’ve introduced a `GET` request mapping using the annotation `@GetMapping`. This annotation is a more specific form of the base `@RequestMapping`. We’ll talk more about HTTP methods when we touch on REST, but suffice to say that there is an annotation for each HTTP method you’d like to map to your controllers.

A `String` is a simple type and requires no conversion, and in more complex cases, Spring MVC will use Jackson (included as a transitive dependency as part of Spring itself) to convert an entity to whatever type your method is expected to return.

As we can see from our `@GetMapping,` it *produces* a type of `text/plain`. Our method returns a `ResponseEntity` of type `String`, which makes sense given that we’re returning a simple plain text item. The simple return is one of `Hello, World!` with a status of `OK` or `HTTP 200`. Spring MVC will do a check on start to ensure you don’t have any conflicting mappings.

It’s always a good idea to ensure that your controller actually works and does the thing you expect, so let’s create a test to validate our assumptions.

```
package com.bsg6.chapter06;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.MediaType;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;
import org.testng.annotations.Test;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
@Test
@WebAppConfiguration
@ContextConfiguration(classes = GatewayAppWebConfig.class)
public class TestGreetingController
extends AbstractTestNGSpringContextTests {
@Autowired
private WebApplicationContext wac;
private MockMvc mockMvc;
@Test
public void greetingTest() throws Exception {
this.mockMvc = MockMvcBuilders
.webAppContextSetup(this.wac)
.build();
this.mockMvc.perform(get("/greeting")
.accept(MediaType.ALL))
.andExpect(status().isOk());
}
}
Listing 6-4
chapter06/src/test/java/com/bsg6/chapter06/TestGreetingController.java
```

The preceding test is a fairly simple test ensuring that our controller is handling things that we expect it to. We are using the `MockMvc` class which fakes the HTTP call for the web layer so that we can keep this test quick, and we’re purposefully not starting up a web container for the test as that will be covered more in Chapter [7](#471370_2_En_7_Chapter.xhtml) with the `TestRestTemplate`. With this test we can ensure the contract is being fulfilled and our code is doing what it’s supposed to, which is exactly what you’d want to see in a test.

You’ll notice that it uses a `GatewayAppWebConfig` class. We’re going to flesh this out further, but here’s enough to get things running for now.

```
package com.bsg6.chapter06;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = {"com.bsg6.chapter06", "com.bsg6.chapter03.mem03"})
public class GatewayAppWebConfig implements WebMvcConfigurer {
}
Listing 6-5
chapter06/src/main/java/com/bsg6/chapter06/GatewayAppWebConfig.java
```

Note

We’re going to add some configuration to this class later in the chapter. It’s being included here just to enable `TestGreetingController` to run.

If you’d like to run the web server and test it manually (always a fine idea), you can run the command line to start tomcat with `mvn -pl chapter06 jetty:run`, hit the endpoint `http://localhost:8080/chapter06/greeting,` and be greeted with a simple text string `Hello, World!`.

So far we’ve introduced a simple GET request.

In the examples that follow, we will continue showing off portions of Spring MVC and follow REST as closely as we can in each. Given that this is the first time we’re talking about REST in the book, it’s probably appropriate for us to discuss some of the architectural concepts inherent in using this paradigm which we’ll do in the next section.

### REST Concepts

One of the guiding API design strategies of the last few decades has been REST, or Representational State Transfer.^([^(82)](#fn82)) It was developed by Roy T. Fielding in the late 1990s in parallel with the development of HTTP 1.1 and based on existing designs of the HTTP 1.0\. When comparing the SOAP protocol^([^(83)](#fn83)) to the architectural principles specified by REST, we see a larger focus on reducing overall complexity and discoverability by using the concepts baked into the HTTP protocol.

The following two things should be followed as a base implementation of a RESTful API. There are many others, but these are the most prevalent:

*   A base URI – for example, [`http://api.bandgateway.com/songs/`](http://api.bandgateway.com/songs/), which specifies the collection (“songs,” in this case), which you’ll be operating under using the actions specified using HTTP methods.

*   Use the HTTP methods appropriately, the most often used are `GET`, `POST`, `PUT,` and `DELETE`. Others like `PATCH`, `OPTIONS`, and `HEAD` are used less often but still can be important. In REST, they are used as actions operating on your collections or entities.

We’re not suggesting that the other tenets of the REST architectural style aren’t useful – they are. But they’re also out of scope for **this** book.

The above tenets should get you a good portion of the way toward a less complicated API. Let’s talk a bit about what we mean about using HTTP methods appropriately.

#### Using HTTP Methods Appropriately

They say there’s no wrong way to eat a Reese’s,^([^(84)](#fn84)) and the same can be said of using HTTP methods. There is, however, a **prescribed** way of using the HTTP methods to follow the REST architectural style.

REST splits things into member resources and collection resources. It bills itself as a paradigm because it doesn’t fit every possible API out there but does a very good job at representing most patterns you can think of. Below we’ll expand on some of this, but if your API feels like it needs something a bit different, that’s fine; it’s not a perfect abstraction, so focus on getting the job done rather than building out the perfect API. For the purposes of our simple API, we’ll talk about a single level of both resources **and** collections, but just know that nesting is possible and simple enough to achieve with Spring MVC. If you’re dealing with a collection of resources, you’d be looking at a URL like [`http://api.bandgateway.com/songs/`](http://api.bandgateway.com/songs/). For a member resource we will specify an identifier of some kind so your URL structure will look more like [`http://api.bandgateway.com/songs/42`](http://api.bandgateway.com/songs/42).

Warning: jargon ahead!

The list of HTTP verbs often refers to a concept of “idempotency.” A call is “idempotent” when you can make multiple calls with the exact same data and produce the **same** result. Operations that are read-only – like `GET` – are idempotent by nature; for other verbs, it can get a little more complicated. If a call changes the application state but does so consistently, it’s considered to be idempotent, like what would happen if you turned a light switch to “on” multiple times – it’s just going to stay on. In reality, of course, it’s a little more complicated.

Abstractions are nice, but not perfect. Back to our text!

`GET` is used to retrieve a resource. A request of this nature should normally have no side effects, meaning that the request is “safe” because the state of the resource is never changed.

![](images/471370_2_En_6_Chapter/471370_2_En_6_Figa_HTML.jpg)

A diagram illustrates client-server communication via requests and responses. The client sends 2 get requests for songs slash 1 and 1 for songs slash 3\. The server responds with 200 status codes and data for songs slash 1, and 404 status code for songs slash 3.

`POST` is used to create new resources and will use the collection resource URL structure. The primary differences with `PUT` are that it is not idempotent as multiple calls to `POST` will create new resources and it uses the collection resource URL structure. (It’s not idempotent because each call will create new object state, and the new state will have its own identifiers, and so forth.)

![](images/471370_2_En_6_Chapter/471370_2_En_6_Figb_HTML.jpg)

A diagram illustrates client-server communication via requests and responses. The client sends 2 post requests for songs with title something. The server responds with i d 1, title something for the first request, and i d 2, title something for the second request.

`PUT` is used primarily to update an existing resource in full. If the resource does not exist, the origin server must use the HTTP 201 (Created) response code and if it is an update a HTTP status code 200 if returning the entity, or an HTTP status code 204 if the choice is made to not return the entity. `PUT` requests should be idempotent, which means that they should produce the same result if executed once or multiple times with the same data, which ensures repeated/retried calls to a `PUT` will not cause unintended effects (thus, idempotency). In addition, a `PUT` request is operated on an individual resource rather than the collection since it’s intended to be an update in most cases.

![](images/471370_2_En_6_Chapter/471370_2_En_6_Figc_HTML.jpg)

A diagram illustrates client-server communication via requests and responses. The client sends 3 put requests for songs slash 1 with respective titles. The server responds with 201, i d 1, title something for the first request, and 200, i d 1, title something for the last 2 requests.

`DELETE` is used to remove a resource identified by the Request URI. As with `PUT`, a `DELETE` operation is idempotent which means repeatedly calling the `DELETE` endpoint on that resource will not change the outcome but a second time will return a 404 assuming the delete was successful.

![](images/471370_2_En_6_Chapter/471370_2_En_6_Figd_HTML.jpg)

A diagram illustrates client-server communication via requests and responses. The client sends 2 delete requests for songs slash 1\. The server responds with 200 status code for the first request and 404 status code for the second request.

`OPTIONS` is generally used for CORS requests.^([^(85)](#fn85)) A deeper look at CORS is beyond the scope of this book; it is a security-based mechanism implemented via HTTP headers that tells a browser to allow an application running at one origin to have permission to access selected resources from a different origin. In the real world, a common vector of web-based attack is injecting malicious JavaScript code which may hit other domains you have desired access to. Thus, a best practice is to limit the scope of external code that is allowed to hit your API to an approved list of domains. We’re not going to include a sequence diagram for `OPTIONS`, even a small one, since it’s out of scope for this chapter.

The above is (hopefully) a suitable-yet-sparse primer on useful methods with HTTP and REST, so let’s put them into action next with our first REST endpoint.

## Developing Our First Endpoint with MVC

Now that we’ve explained a bit more about the concepts involved with REST, let’s start building out some endpoints. The easiest to explain is a simple `GET` request. The following snippet will handle any requests going to a URI like [`http://api.bandgateway.com/songs?artist=threadbare%20loaf`](http://api.bandgateway.com/songs?artist=threadbare%20loaf). When Spring returns the query parameter, it will be URL decoded so `threadbare%20loaf` will turn into `threadbare loaf` with an ASCII space in between. With a URI there are specific allowed characters to allow easier transport. These characters are defined by RFC 3986 Section 2 ([`https://tools.ietf.org/html/rfc3986`](https://tools.ietf.org/html/rfc3986)) and generally include US-ASCII alphanumeric characters along with several reserved characters which have meaning in a URI string. If a character being represented doesn’t fall within the specification, they are percent-encoded with the US-ASCII code representing that character.

```
package com.bsg6.chapter06;
import com.bsg6.chapter03.MusicService;
import com.bsg6.chapter03.model.Song;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestParam;
import java.net.URLDecoder;
import java.nio.charset.StandardCharsets;
import java.util.List;
@Controller
public class GetSongsController {
MusicService service;
GetSongsController(MusicService service) {
this.service = service;
}
@GetMapping("/artists/{artist}/songs/{name}")
public ResponseEntity getSong(
return new ResponseEntity(data, HttpStatus.OK);
}
}
Listing 6-6
chapter06/src/main/java/com/bsg6/chapter06/GetSongsController.java
```

Note

We’re going to be adding a method to this class shortly.

In Listing [6-6](#471370_2_En_6_Chapter.xhtml#PC6), we have a class `GetSongsController`, and in order to tell Spring MVC that we intend this to be a class that will respond to HTTP requests of some fashion, we annotate it with `@Controller`. This annotation is basically a more specific form of `@Bean` or `@Component` that we’ve seen in earlier chapters and we’ll use a lot more throughout this chapter and the book. We want to be able to pull in our `MusicService,` so we use the annotation `@Autowired` on the declaration which we learned about back in Chapter [5](#471370_2_En_5_Chapter.xhtml). As we move through the chapters, we’ll continue to use services from previous chapters so we can expand on concepts building on what we already know.

As in the `GreetingController` earlier in the chapter, we’ve annotated our `getSongsByArtist` method with a `@GetMapping` annotation which means any GET request to `/songs` will hit this method.

Our `getSongsByArtist` method is set up to return a list of `Song` objects as a JSON array. In order to do this, we need to accept a query parameter `artist`. Since we’re down the road of annotations already, we preface our method parameter with `@RequestParam` which maps it to a web parameter.

A `@RequestParam` maps to query parameters, form data, and parts in multipart requests. While not required, any of the `@*Mapping` annotations will accept a `params` attribute so you can be more explicit about which params map to your method. For the `@RequestParam,` it can have the following parameters, all of which are also optional.

| Parameter | Description |
| --- | --- |
| `name` | This is the name of the query parameter in the URI |
| `required` | Default is true, safe to ignore if that fits your requirements; otherwise specify the opposite |
| `defaultValue` | Used in the event a value is not specified so you can operate on the default |

We’re going to return a JSON array of `Song` types, so we’re going to implement a bit of magic and return a `ResponseEntity` object which is commonly used in Spring MVC as the return value for a `@Controller` method. When we construct the `ResponseEntity`, we include the list of songs – which will get represented in JSON as an array, as the list type in JSON – and the HTTP response code.

The other method handling requests in `GetSongsController` is listed below. It shows use of another method of accepting user input. We don’t currently have any unique identifier to use as lookup, so we’re going to depend on two key pieces of information that make up a song, the artist and song name. If you’ll remember in Chapter [1](#471370_2_En_1_Chapter.xhtml), a song name is not enough to make up a unique identifier given that there have been examples of duplicate song names over the years.

A `@PathVariable` maps to a pattern in the mapping definition annotation and can have the following optional parameters.

| Parameter | Description |
| --- | --- |
| `name` | This is the name of the path parameter in the URI |
| `required` | Default is true, safe to ignore if that fits your requirements; otherwise specify the opposite |

Listing [6-7](#471370_2_En_6_Chapter.xhtml#PC7) shows what our `getSong` method looks like given the above information. We won’t bore you with re-explaining any detail on the annotations that we’ve previously explained above in the `@RequestParam` explanations.

```
@PathVariable("artist") final String artist,
@PathVariable("name") final String name
) {
var artistDecoded = URLDecoder.decode(artist, StandardCharsets.UTF_8);
var nameDecoded = URLDecoder.decode(name, StandardCharsets.UTF_8);
var song = service.getSong(artistDecoded, nameDecoded);
return new ResponseEntity(song, HttpStatus.OK);
}
@GetMapping("/songs")
public ResponseEntity> getSongsByArtist(
Listing 6-7
chapter06/src/main/java/com/bsg6/chapter06/GetSongsController.java
```

Our GET request here accepts two `@PathVariable` parameters and passes those into our method. The URI will look something like the following [`http://api.bandgateway.com/artists/threadbare+loaf/songs/someone+stole+the+flour`](http://api.bandgateway.com/artists/threadbare+loaf/songs/someone+stole+the+flour). In the case of path parameters unlike our query parameter above, this will NOT be automatically URL decoded by Spring. In our controller method, since we’re looking for “threadbare loaf,” and not “threadbare+loaf,” we will use the `URLDecoder` class available in the Java standard library to get a decoded version of the artist and name.

Spring also has its own encoder and decoder. Which one should you use?

It really doesn’t matter. Both libraries will do the same thing, and they’re both available to you. In the next chapter we’ll use the Spring encoder and decoder, and we won’t notice a difference.

Now that we have these two endpoints, we need to test them just like we did for our `GreetingController`. We’ll do the same song and dance with our tests here, and show you how to mock and ensure that your endpoints are processing the inputs and delivering the outputs in the way you expect.

```
package com.bsg6.chapter06;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.MediaType;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;
import org.testng.annotations.Test;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
@Test
@WebAppConfiguration
@ContextConfiguration(classes = GatewayAppWebConfig.class)
public class TestGetSongsController
extends AbstractTestNGSpringContextTests {
@Autowired
private WebApplicationContext wac;
private MockMvc mockMvc;
@Test
public void getSongControllerTest() throws Exception {
this.mockMvc = MockMvcBuilders
.webAppContextSetup(this.wac)
.build();
MvcResult result = this.mockMvc.perform(get("/songs")
.param("artist", "van halen")
.param("name", "jump"))
.andReturn();
this.mockMvc.perform(get("/songs")
.param("artist", "van halen")
.param("name", "jump")
.accept(MediaType.ALL))
.andExpect(status().isOk());
}
@Test
public void getSongsTestWithoutParameters()
throws Exception {
this.mockMvc = MockMvcBuilders
.webAppContextSetup(this.wac)
.build();
this.mockMvc.perform(get("/songs")
.accept(MediaType.ALL))
.andExpect(status().is4xxClientError());
}
@Test
public void getSongsByArtistTest() throws Exception {
this.mockMvc = MockMvcBuilders
.webAppContextSetup(this.wac)
.build();
this.mockMvc.perform(get("/songs").param("artist", "van halen")
.accept(MediaType.ALL))
.andExpect(status().isOk());
}
}
Listing 6-8
chapter06/src/test/java/com/bsg6/chapter06/TestGetSongsController.java
```

We chose using a mock here to make test writing simple and fast (skipping the deployment step of having to start a server instance). In Chapter [7](#471370_2_En_7_Chapter.xhtml), we will expand on this and use more tooling in the testing space.

The more astute reader will look at the above, or have entered it all into their codebase, and wonder how to map the template with our controller. Let’s set up our initializer and config which will show how this is done.

## Configuration

We’re getting very used to annotation-based configuration, and so we’ll expand by showing some configuration classes and how they’re used.

First up is the `GatewayAppInitializer` which requires us to override two of its methods, `getRootConfigClasses()` and `getServletConfigClasses()`. We’ll override a third, `getServletMappings()` which identifies what the root mappings will be for Spring’s `DispatcherServlet` to listen to. The one we care about the most is `getServletConfigClasses()`, which returns our `GatewayAppWebConfig` class.

```
package com.bsg6.chapter06;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;
public class GatewayAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
@Override
protected Class[] getRootConfigClasses() {
return new Class[0];
}
@Override
protected Class[] getServletConfigClasses() {
return new Class[]{GatewayAppWebConfig.class};
}
@Override
protected String[] getServletMappings() {
return new String[]{"/"};
}
}
Listing 6-9
chapter06/src/main/java/com/bsg6/chapter06/GatewayAppInitializer.java
```

Our `GatewayAppWebConfig` will start with two annotations which should make sense intrinsically, `@Configuration` and `@EnableWebMvc` as in, this is a configuration class, and we want to enable Spring MVC for the things that we’re configuring here.

The next annotation – `@ComponentScan` – we’ve seen mentioned in Chapter [5](#471370_2_En_5_Chapter.xhtml). It pulls in all classes under the specified package structure – in this case, our service and model classes from Chapter [3](#471370_2_En_3_Chapter.xhtml) `com.bsg6.chapter03.mem03` and the classes for Chapter [6](#471370_2_En_6_Chapter.xhtml) under `com.bsg6.chapter06`.

```
package com.bsg6.chapter06;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ViewResolverRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = {"com.bsg6.chapter06", "com.bsg6.chapter03.mem03"})
public class GatewayAppWebConfig implements WebMvcConfigurer {
@Override
public void configureViewResolvers(ViewResolverRegistry registry) {
registry.viewResolver(mustacheViewResolver());
}
@Bean
public ViewResolver mustacheViewResolver() {
var viewResolver = new MustacheViewResolver();
viewResolver.setPrefix("/WEB-INF/templates/");
viewResolver.setSuffix(".html");
return viewResolver;
}
}
Listing 6-10
chapter06/src/main/java/com/bsg6/chapter06/GatewayAppWebConfig.java
```

In Chapter [5](#471370_2_En_5_Chapter.xhtml) we introduced using Mustache for our template rendering, and we’ll continue doing that with Spring MVC. The reason to use this library is it provides a very simple and easily understood templating engine. It’s a fine option, and if you’re looking for something an alternative you can look into Freemarker ([`https://freemarker.apache.org/`](https://freemarker.apache.org/)), or dispense with any of this and just write RESTful components which return JSON.

In order to use Mustache, we need to register a new `ViewResolver` and set up where our templates are and what suffix to use when we scan.

Note

We’re explicitly creating a `ViewResolver` here. Spring already has a Mustache `ViewResolver`, as part of Spring Boot, and we’ll use that one in future chapters, but we’re not ready to use Spring Boot yet for this chapter. This `ViewResolver` is enough for now. If you look at the one provided by Spring itself, this one’s a pretty close copy.

To create a `ViewResolver`, we have to first create an implementation of a `View`.

```
package com.bsg6.chapter06;
import com.samskivert.mustache.Mustache.Compiler;
import com.samskivert.mustache.Template;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.core.io.Resource;
import org.springframework.web.servlet.view.AbstractTemplateView;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.Reader;
import java.util.Locale;
import java.util.Map;
public class MustacheView extends AbstractTemplateView {
private Compiler compiler;
private String charset;
public void setCompiler(Compiler compiler) {
this.compiler = compiler;
}
public void setCharset(String charset) {
this.charset = charset;
}
@Override
public boolean checkResource(Locale locale) throws Exception {
var resource = getApplicationContext()
.getResource(getUrl());
return (resource != null && resource.exists());
}
@Override
protected void renderMergedTemplateModel(
Map model,
HttpServletRequest request,
HttpServletResponse response)
throws Exception {
var template = createTemplate(getApplicationContext()
.getResource(getUrl()));
if (template != null) {
template.execute(model, response.getWriter());
}
}
private Template createTemplate(Resource resource)
throws IOException {
try (Reader reader = getReader(resource)) {
return this.compiler.compile(reader);
}
}
private Reader getReader(Resource resource) throws IOException {
if (this.charset != null) {
return new InputStreamReader(
resource.getInputStream(),
this.charset);
}
return new InputStreamReader(
resource.getInputStream()
);
}
}
Listing 6-11
chapter06/src/main/java/com/bsg6/chapter06/MustacheView.java
```

For the most part, this is a relatively resource-intensive, pessimistic implementation of a template rendering mechanism; it does no caching (so it recompiles the templates aggressively) and is very simple for what it is; as already described, Spring Boot already has something like this, so ordinarily we wouldn’t bother.

Now that we have a `MustacheView`, we need a way to *resolve* Spring view names to things that are rendered *by* that `View`, so we have a `MustacheViewResolver`.

```
package com.bsg6.chapter06;
import com.samskivert.mustache.Mustache;
import com.samskivert.mustache.Mustache.Compiler;
import org.springframework.web.servlet.view.AbstractTemplateViewResolver;
import org.springframework.web.servlet.view.AbstractUrlBasedView;
public class MustacheViewResolver extends AbstractTemplateViewResolver {
private final Compiler compiler;
private String charset;
public MustacheViewResolver() {
this.compiler = Mustache.compiler();
setViewClass(requiredViewClass());
}
public MustacheViewResolver(Compiler compiler) {
this.compiler = compiler;
setViewClass(requiredViewClass());
}
@Override
protected Class requiredViewClass() {
return MustacheView.class;
}
public void setCharset(String charset) {
this.charset = charset;
}
@Override
protected AbstractUrlBasedView buildView(
String viewName
) throws Exception {
var view = (MustacheView) super.buildView(viewName);
view.setCompiler(this.compiler);
view.setCharset(this.charset);
return view;
}
@Override
protected AbstractUrlBasedView instantiateView() {
if((getViewClass() == MustacheView.class)) {
return new MustacheView();
} else {
return super.instantiateView();
}
}
}
Listing 6-12
chapter06/src/main/java/com/bsg6/chapter06/MustacheViewResolver.java
```

Now that we have a way to render templates, let’s move through a simple example where we can put our new configuration into practice.

## Templates and Models

In the above we’ve covered pretty nicely the `@Controller` aspect of the Model View Controller paradigm. Let’s take a little time to talk about the View, and the Model.

There are three classes provided by Spring we can use to move data into our view from our controller classes: `Model`, `ModelMap,` and `ModelAndView`. Let’s take a look at our usage of the `Model` class in the next example.

```
package com.bsg6.chapter06;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
@Controller
public class GreetingWithModelController {
@GetMapping(name = "greeting", path = "/greeting/{name}")
public String greeting(@PathVariable(name="name") String name, Model model) {
model.addAttribute("name", name);
return "greeting";
}
}
Listing 6-13
chapter06/src/main/java/com/bsg6/chapter06/GreetingWithModelController.java
```

In this listing, we see a controller with a `@PathVariable` similar to what we’ve seen earlier accepting a required `name` field. Our method returns a `String` here rather than a `ResponseEntity` because we’re going to pass back the name of the template we want to serve this request. Our method accepts a `Model` class into which we’re throwing the value passed in to the path `name` into. One of the nice things about a `Model` is that it will also allow you to merge a `Map` of `String` values should you have the need.

You will remember in the configuration section that we set up a `ViewResolver` for handling Mustache templates. This is being put into action now with our `greeting.html` template. You can see from our method that we pass back a `"greeting"` String and merging that with the configuration we set up with our `ViewResolver` we can infer that the template path will be `templates/greeting.html`.

Let’s take a look at our template.

```

Hello, {{ name }}

Hello, {{ name }}

Listing 6-14
chapter06/src/main/webapp/WEB-INF/templates/greeting.html
```

Our template does a variable replacement on a `Model` attribute called `name` and effectively does a `model.getAttribute("name")` to fill in for the `{{ name }}` snippet in the code. A `Model` is essentially an interface that gets passed into your method, and allows you to add attributes to it.

The second method of passing model data to our view is a `ModelMap`. This method allows you to chain calls and supports auto-generated attribute names from the values. Let’s see a simple example of this.

```
public String greeting(ModelMap map) {
map.addAttribute("helloWorld");
map.addAttribute("threadbareLoaf");
return "greeting";
}
```

In the preceding code, we’re using the auto-generate functionality so that our model will have two attribute names, one called “helloWorld” and the other “threadbareLoaf.” It’s silly, but then life can be pretty silly, can’t it? (If it can’t, how do we explain some of the text in this book?)

The final method of model data passing is the `ModelAndView`. It is a convenience class for returning both the model and the view in a single call. The underlying holder of model data is a `ModelMap`, and the view can either be a `String` view like we’ve seen previously which needs to be resolved by a `ViewResolver` class, or a view object can be specified directly.

```
public ModelAndView greeting() {
Map model = new HashMap();
model.put("helloWorld", "helloWorld");
model.put("threadbareLoaf", "threadbareLoaf");
return new ModelAndView("greeting", model);
}
```

In this simple example, we have duplicated what we did in the `ModelMap` example, but we’re using the `ModelAndView` class now. It may not seem like much of a savings, but it allows us to encapsulate both model and view in a single class, so it affords us quite a lot of power. In the next section we’re going to tackle errors and how to configure and show them on the front end.

## Error Handling

Taking care of error cases when they come up is vital to the building of your web application. With that in mind let’s take a look at some ways in which you can let users of our application know when they have hit an error. Our first order of business is to build a custom exception which will simply extend `RuntimeException` and expose one of the methods. Let’s take a look at that now.

```
package com.bsg6.chapter06;
public class ArtistNotFoundException extends RuntimeException {
public ArtistNotFoundException(String message) {
super(message);
}
}
Listing 6-15
chapter06/src/main/java/com/bsg6/chapter06/ArtistNotFoundException.java
```

The preceding code is pretty standard stuff, and you can definitely customize it to your heart’s content with error codes or other data should you feel it necessary. The way this comes into action, though, given that we’re firmly on the side of using annotations everywhere, is with the `@ExceptionHandler` annotation. In the following snippet, we’re showing a handler for our custom exception `ArtistNotFoundException,` and this is going to tell Spring that the view we’re looking for when something like that is thrown, should be handled here.

Let’s take a look at some code to show how this is done.

```
@Controller
public class GetArtistsExceptionController {
MusicService service;
GetArtistsExceptionController(MusicService service) {
throw new ArtistNotFoundException("Artist with name " + artist + " not found");
}
@ExceptionHandler(ArtistNotFoundException.class)
public ModelAndView handleCustomException(ArtistNotFoundException ex) {
var model = new ModelAndView("error");
model.addObject("message", ex.getMessage());
model.addObject("statusCode", 404);
return model;
}
}
Listing 6-16
chapter06/src/main/java/com/bsg6/chapter06/GetArtistsExceptionController.java
```

This will handle a very specific exception that can be thrown in a controller method. It uses the `ModelAndView` object that we learned about in the previous section, and pre-fills in the 404 status code since this is a “Not Found” exception. What happens when something else happens, though, and it’s an exception we haven’t accounted for? We can define a catch-all exception like the following code snippet.

```
@Controller
public class GetArtistsExceptionController {
MusicService service;
GetArtistsExceptionController(MusicService service) {
}
@ExceptionHandler(Exception.class)
public ModelAndView handleAllExceptions(Exception ex) {
var model = new ModelAndView("error");
model.addObject("message", ex.getMessage());
model.addObject("statusCode", 500);
return model;
}
Listing 6-17
chapter06/src/main/java/com/bsg6/chapter06/GetArtistsExceptionController.java
```

This will default to returning a 500 since it may have been thrown somewhere outside of our code, and this is probably a legitimate server error that we can tell the user about.

Our next method is going to be a little silly, since we’re using service methods from Chapter [3](#471370_2_En_3_Chapter.xhtml), and they don’t throw exceptions or return nulls, we’ve just got a controller method that will respond with any `GET /artists/{artist}` with the 404.

```
@Controller
public class GetArtistsExceptionController {
MusicService service;
GetArtistsExceptionController(MusicService service) {
this.service = service;
}
@GetMapping("/artists/{artist}")
@ResponseBody
public ResponseEntity getSong(
@PathVariable("artist") final String artist
) {
throw new ArtistNotFoundException("Artist with name " + artist + " not found");
}
}
Listing 6-18
chapter06/src/main/java/com/bsg6/chapter06/GetArtistsExceptionController.java
```

Our `ModelAndView` definitions in the two exception handler methods of our controller reference a view named “error.” Let’s take a look at our error template.

```

Error {{ statusCode }}

An error has occurred with status: {{ statusCode }} and message: {{ message }}

Listing 6-19
chapter06/src/main/webapp/WEB-INF/templates/error.html
```

The preceding template is simple; it encapsulates a simple error page and pulls data from our controller in the event something goes wrong.

Warning

We’re leaving out some useful information in handling the exception, largely because we’re forwarding to a view, rendered on the server. There’s a “Problem Details for HTTP APIs” RFC (RFC 7807^([^(86)](#fn86))) that specifies information about the error that goes into greater, and more useful, detail than we’re showing here. We will show problem details in use in Chapter [7](#471370_2_En_7_Chapter.xhtml).

## Next Steps

In this chapter, we saw how to build a more fully featured band gateway application, by removing all of the manual invocations and conversions (and Servlets) from our application, leading us to a more streamlined and modern web application development process, including automated testing. The next chapter will integrate Spring Boot into our project, which gives us access to many more fully developed aspects of our deployment, saving us quite a bit of configuration and manual implementations of common classes like our `ViewResolver`.

Footnotes [1](#471370_2_En_6_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_6_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_6_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_6_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_6_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_6_Chapter.xhtml#Fn6_source)  

# 7. Spring Boot

So far, we’ve looked at dependency injection and various configuration approaches, and we’ve explored deploying some web services into Apache Tomcat. Along the way we’ve used a small set of Spring modules, picking and choosing as needed. It’s time for us to switch gears and look at Spring Boot, which is a project structure generally aimed at **microcontainers**; Spring Boot gives us an easier way to get larger feature sets out of Spring and offers an integrated set of services aimed at deploying running applications without having to rely on traditional Jakarta EE services like Apache Tomcat.

**Microcontainers** have multiple definitions, but in context, it’s a self-contained application, representable as a single artifact, deployed in a virtual machine. That sounds a little odd in the context of Java, which runs in a virtual machine itself; wouldn’t every application qualify as a “microcontainer,” then?

The answer is “…​ maybe.” However, the more common use of a “microcontainer” is an application with a single purpose, like “run the band gateway application,” running inside its own virtual machine (therefore, a virtual machine running inside another virtual machine) such as a Docker container.

## What Is Spring Boot?

Spring Boot ([`https://spring.io/projects/spring-boot`](https://spring.io/projects/spring-boot)) can be defined in many ways, just like Spring itself can be defined in many ways.

At its heart, it’s a project that combines many of the most commonly used bits of Spring into a single cohesive ecosystem such that dependencies are easier to manage, as well as an executable environment to make deployment without a container quite doable.

We haven’t really run into enough correlated dependencies to give ourselves a headache with trying to chase down **problems** in our dependencies, although as we’ve progressed we’ve added more and more modules to our chapter projects. So far we’ve been laser-focused on each chapter’s topics such that we’ve been able to keep dependency lists to a fair minimum. Even so, there are a few issues we’ve encountered – although we haven’t needed to address them, because they’re so minor, and our tools actually fix them for us.

Imagine we have a dependency on a library – for the sake of argument, we’ll call it `spring-foobar. This project name is entirely made up.` Let’s imagine that library has its own transitive dependencies, on `guava`, `slf4j-api`, and `spring-webmvc`. The question is: *which* `spring-webmvc`? If we have **our own** dependency on `spring-webmvc`, it might be that there’s a conflict between the `spring-foobar` dependency – say, `spring-webmvc` 3.1^([^(87)](#fn87)) and *our* dependency, which might be `spring-webmvc` 6.1.0.

Our build scripts will “win,” in general, no matter what build system we use, and the build systems like Maven and Gradle have the ability to specifically exclude transitive dependencies at need should they get confused in any case. However, there’s another possibility: we can use bills of materials in our build that incorporate a host of related dependencies, instead of picking and choosing specific versions.

The result is that we end up with a more cohesive, consistent build.

With the Spring Boot parent project, we coalesce many of the Spring dependencies into one reference list – the bill of materials – and thus eliminate these potential problems in one fell swoop. This has implications for our library declarations and testing, since almost everything is made available by magic.^([^(88)](#fn88))

But there’s more.

Spring Boot is designed to give you the ability to make your projects executable *without* a container like Apache Tomcat. You can still write your controllers and Spring beans the exact same way, but instead of starting a Tomcat instance and deploying your web application into it (a process we are more or less pretending to do when we use `mvn jetty:run`), Spring Boot can be executed as a regular executable Jar file. It embeds a web container itself (Tomcat, by default) and manages it; it also manages logging, database connections (and database setup), configurations, metrics, and other things.

It can be horribly convenient.

## Setting Up a Project

Actually setting up a Spring Boot project is rather easy. In Maven, it involves a single plugin and a single initial dependency (with the option of configuring an executable jar) – after which everything more or less works through convention, **including** a basic configuration. Let’s walk through yet another “Hello, World” application just to show the parts we need – and note that we’ll be adding pieces as we go through the chapter.

First, of course, we need to create the source structure and `pom.xml` itself. The source structure mirrors every other chapter we’ve seen so far, with a `main` and `test` directory; our `main` contains a `java` directory and a `resources` directory. (We’ll be using `resources` later in the chapter to add static content just to illustrate how we can create a “user interface” – a term being applied very loosely in this book’s case.)

```
mkdir -p chapter07/src/main/java/com/bsg6/chapter07
mkdir -p chapter07/src/main/resources
mkdir -p chapter07/src/test/java/com/bsg6/chapter07
Listing 7-1
Creating the directory structure with POSIX
```

Our `pom.xml` is fairly bland – a good thing in a build script. Things to note are as follows:

1.  We’re including a build plugin, `spring-boot-dependencies`, which provides version information for subsequent dependencies.

2.  The `spring-boot-starter-web` dependency, whose version comes from the `boot` plugin – so we don’t have to (or want to, normally) specify anything (it’s pulled from the plugin).

This structure is very, very common with Spring Boot applications – you could probably cut and paste this as a starter for Spring Boot projects if you wanted. (For the record, we’re going to alter this script later in the chapter to add some features our tests will need.)

For the record, this isn’t special information or tribal knowledge of any kind – Spring Boot’s own documentation shows you nearly the exact same thing, as will nearly any other Spring Boot reference.

```

4.0.0

com.apress
bsg6
1.0

chapter07
1.0
war

org.springframework.boot
spring-boot-starter-web

org.springframework.boot
spring-boot-starter-test
test

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

Listing 7-2
chapter07/pom.xml
```

## Checking the Foundation

Now that we have our project structure set up, we can create our entrypoint, which we’re going to call `Chapter7Application`. It will have no specific code to execute in and of itself – it will load a configuration (via the `@SpringBootApplication` annotation, which has special meanings as we’ll see in a few more paragraphs) – and crank up a web container as well as creating a lot of beans for us, for metrics and other services. What our application will do is simple: declare beans and controllers and make sure they’re visible to the application class (by virtue of being in the same package tree, so having a package that starts with `com.bsg6.chapter07`, including any packages **under** `com.bsg6.chapter07`.). Those services will be automatically made available to whatever needs them, as we’ll see.

### Building the Application

```
package com.bsg6.chapter07;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class Chapter7Application {
public static void main(String[] args) {
SpringApplication.run(Chapter7Application.class, args);
}
}
Listing 7-3
chapter07/src/main/java/com/bsg6/chapter07/Chapter7Application.java
```

### Building Our Transport Object

Of course, this isn’t useful without an actual controller to serve as an HTTP endpoint somewhere, so let’s also create a simple greeting service, to make sure the parts all work together. First, we’ll create a `Greeting` – an object containing content – that we can serve from a REST endpoint.

Note the use of methods like `Objects.equals()` and `Objects.hash()` – these are utility methods introduced way back in Java 1.7 to help generate hash codes and determine equality.

If you skipped the ideas behind REST in Chapter [6](#471370_2_En_6_Chapter.xhtml), this might be a good time to go back and review. If you don’t want to do that, it’s okay – basically, we’re hosting a resource behind an HTTP `GET` request.

```
package com.bsg6.chapter07;
import java.util.Objects;
public class Greeting {
String message;
public Greeting(String message) {
this.message = message;
}
public Greeting() {
}
public String getMessage() {
return message;
}
public void setMessage(String message) {
this.message = message;
}
@Override
public boolean equals(Object o) {
if (this == o) {
return true;
}
if (!(o instanceof Greeting)) {
return false;
}
Greeting greeting = (Greeting) o;
return Objects.equals(getMessage(), greeting.getMessage());
}
@Override
public int hashCode() {
return Objects.hash(getMessage());
}
}
Listing 7-4
chapter07/src/main/java/com/bsg6/chapter07/Greeting.java
```

### Actually Saying “Hello”

Now that we have an object to pass around, let’s create our `GreetingController`, in concept incredibly similar to the `GreetingController` from Chapter [6](#471370_2_En_6_Chapter.xhtml).^([^(89)](#fn89)) We’ll add a special case or two: for one thing, we’ll have a generalized greeting in case no name is provided, and for another, we’ll have our controller not recognize the name of the Invisible Man (“Jack Griffin,” in the 1933 horror film.)

```
package com.bsg6.chapter07;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class GreetingController {
@RequestMapping(value = {"/greeting/{name}", "/greeting/"})
Greeting greeting(@PathVariable(required = false) String name) {
String object = name != null ? name : "world";
/* Jack Griffin is the name of the "Invisible Man." */
if (object.equalsIgnoreCase("jack griffin")) {
return new Greeting("I don't know who you are.");
} else {
return new Greeting("Hello, " + object + "!");
}
}
}
Listing 7-5
chapter07/src/main/java/com/bsg6/chapter07/GreetingController.java
```

A few things to point out:

*   `@RequestMapping` means that this endpoint will handle the responses for multiple HTTP methods, so we can issue a `POST` to this endpoint as well as a `GET` and it should respond the same. If we wanted to restrict the HTTP method, we could use `@GetMapping` or `@PostMapping`, for example.

*   We have multiple locations specified – one with `{name}`, and one without. That’s to handle the situation where no name is provided, so we can respond to `/greeting/World` and `/greeting` with the same endpoint. This is not particularly wise, as `POST` accepts different kinds of data and we’re pretending all HTTP requests are treated the same.

*   We map the parameter type with a `@PathVariable` annotation, where we specify that it’s not required (because being required is the default). If we didn’t tell Spring that it was optional, we’d get an exception when the value was not provided.

### Testing with Spring Boot

Of course, there’s no point in having a controller or service if we don’t know that it works, so let’s create a test for it that runs a few sample inputs with their expected outputs. There are actually two approaches we can take here; one approach to testing is that we can test the controller directly, by issuing method calls directly against the endpoint method. However, that avoids a lot of the functionality of the endpoint itself. Let’s take a quick look at that code, just to see what we’re talking about.

```
@Test(dataProvider = "greetingData")
public void testDirectGreeting(String name, String greeting) {
assertEquals(
greetingController.greeting(name).getMessage(),
greeting);
}
Listing 7-6
chapter07/src/test/java/com/bsg6/chapter07/TestGreetingController.java
```

This is fine code, I suppose, and it actually verifies that the Java code for the controller is doing what it’s supposed to. However, what we’d like to do is issue a call over HTTP, to make sure our parameter conversion and URL mappings are working properly, and that we’re getting our objects back in the right form. We can do this, provided we allow Spring Boot to wire in an object to issue REST calls in a test, along with building the actual endpoint. To issue the call, we use a `TestRestTemplate`, which will allow us to get a `ResponseEntity<Greeting>` back from our endpoint. The `ResponseEntity` allows us to check the actual results of the REST call – HTTP codes, and the like – as well as returning an object we can examine as in our prior test. It means that we have more code to actually validate the results of the call, but that’s okay; we’re actually interested in such things. (If we weren’t interested in the HTTP status, we could use `TestRestTemplate.getForObject()` instead, which would return the `Greeting` itself and ignore the `ResponseEntity` wrapper.) Here’s the complete, functioning `TestGreetingController` class, to show what’s going on.

```
package com.bsg6.chapter07;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.test.web.server.LocalServerPort;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import static org.testng.Assert.assertEquals;
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class TestGreetingController extends AbstractTestNGSpringContextTests {
@Autowired
private GreetingController greetingController;
@LocalServerPort
private int port;
@Autowired
private TestRestTemplate restTemplate;
@DataProvider
Object[][] greetingData() {
return new Object[][]{
new Object[]{null, "Hello, world!"},
new Object[]{"World", "Hello, World!"},
new Object[]{"Andrew", "Hello, Andrew!"},
new Object[]{"Jack Griffin", "I don't know who you are."}
};
}
@Test(dataProvider = "greetingData")
public void testRestGreeting(String name, String greeting) {
String url = "http://localhost:" + port + "/greeting/" +
(name != null ? name : "");
ResponseEntity result =
restTemplate.getForEntity(url, Greeting.class);
assertEquals(result.getStatusCode(), HttpStatus.OK);
assertEquals(result.getBody().getMessage(), greeting);
}
@Test(dataProvider = "greetingData")
public void testDirectGreeting(String name, String greeting) {
assertEquals(
greetingController.greeting(name).getMessage(),
greeting);
}
}
Listing 7-7
chapter07/src/test/java/com/bsg6/chapter07/TestGreetingController.java
```

We can actually run a test **now** and have the four tests run; now we know our controller is working properly.

### Configuration in Spring Boot

To finish up, then, do we need to create any additional configuration of some kind?

As it turns out, we don’t. (Not yet, at least.) The `@SpringBootApplication` annotation actually implies autoconfiguration (so wiring happens automatically, and a lot of infrastructure is generated for us), component scanning in the same package (and packages “under” the same package) as the class that has the annotation (so all classes in `com.bsg6.chapter07` are scanned to check whether they’re Spring beans or not), and implicitly includes a configuration reference as well – so unless we’re doing something out of the ordinary, Spring Boot will automatically see every bean that is referenced somewhere in the current package, including classes that contain a Java configuration. We’re actually done with our “Hello, World” web service itself.

We’ve mentioned package trees a few times so far in this chapter, where we say that scanning happens in `com.bsg6.chapter07` and packages “under” that package, if we had any. It’s worth remembering, though, that packages are **named** hierarchically, and it’s convenient to think of them as a hierarchy, but they’re not actually hierarchical; you can’t `import` a package tree, but only a specific package.

You’d have to import each package specifically.

So when we write that scanning happens in a “package tree,” note that it’s actually walking through packages with a common prefix; it’s not actually a tree.

If we tell Maven to package the war in our `pom.xml` with `mvn -pl chapter07 package`, Maven will build an executable archive that we can run directly. It’s not a `.jar` file as many executable packages are for Java, but it works anyway.

```
mvn -pl chapter07
java -jar chapter07/target/chapter07-1.0.war
Listing 7-8
Building and running our «Hello, World» container
```

After a fairly large amount of logging information on startup (which should take only a few seconds), we can open `http://localhost:8080/greeting/Joe` in a browser, and be warmly (perhaps even “affectionately”) greeted by our application.

![](images/471370_2_En_7_Chapter/471370_2_En_7_Fig1_HTML.jpg)

A screenshot of a web browser window displaying a webpage hosted on localhost colon 8080 slash greeting slash Joe. The main area of the page depicts the text left brace double quotes message double quotes colon double quotes hello, Joe exclamation mark double quotes right brace.

Figure 7-1

Running the application

### Static Content with Spring Boot

It’s also worth noting that we can have Boot serve static content, as long as we locate it in `src/main/resources/static` in our build tree. (It’s actually more than that: static content can be served from `/static`, `/public`, `/resources`, or even `/META-INF/resources` by default – and yes, you can alter these as well, although it’s not advised. Having **four** “standard locations” is amusing in and of itself, without adding more confusion.)

Let’s add a `hello-boot.html` page that will allow us to actually **make** a REST call from an HTML page, just to demonstrate an end-to-end process.

This is not a recommendation for any kind of rich client programming practices. This is pretty much just about as simple a “rich client” as can be made, and readers are advised to read one of Apress’ many fine books on HTML user interfaces rather than take this primitive example as advice of proper design and practice.

What our HTML page will have is fairly simple: an HTML form that submits data via a short Javascript function, the Javascript itself, and a placeholder to render results. The Javascript will use JQuery ([`https://jquery.com/`](https://jquery.com/)) to issue a REST call against our endpoint, much like our test did, and it will alter the placeholder to render the data from our `GreetingController` verbatim.

```

Hello, World

function submitForm() {
$.get('http://localhost:8080/greeting/'+
$('#helloform input[name=name]').val(),
function(data) {
$("#greeting").text(data.message);
},
'json');
};

p#greeting {
font-family: "Andale Mono",monospace;
}

Hello, what is your name?

Listing 7-9
chapter07/src/main/resources/static/hello-boot.html
```

If we run our application (after rebuilding it, and by running `java -jar chapter07/target/chapter07-1.0.war` again), we can interact with our HTML page by opening `http://localhost:8080/hello-boot.html` and entering various names. For this example, we’ll identify as the Invisible Man.

![](images/471370_2_En_7_Chapter/471370_2_En_7_Fig2_HTML.jpg)

A screenshot of a web browser window displaying a webpage hosted on localhost colon 8080 slash hello-boot dot h t m l. The main area of the page depicts the text Hello, what is your name question mark, an entry field with Jack Griffin entered, and a submit button. I don’t know who you are.

Figure 7-2

Interacting with the page

### Summary of the “Hello, World” Boot Mechanism

We’ve seen how to build our project structure and create a runnable class for Spring Boot, as well as how to create an executable jar file; we’ve also seen how to create a REST endpoint (using concepts we discussed in Chapter [6](#471370_2_En_6_Chapter.xhtml)) with a working test, as well as how to embed static resources into our application. It’s time for us to go back to our music recommendation application, and show how more of this would tie together in a (slightly) more real-world application.

## Spring Boot and Database Connections

It’s time for us to create a better version of our music gateway services. In previous chapters, we’ve been reusing a memory-only version of the services from Chapter [3](#471370_2_En_3_Chapter.xhtml), which was convenient because those services had no other dependencies; they didn’t use a database or anything that might require configuration. In the interest of demonstrating a more fully featured configuration, let’s create a `MusicService` backed by an embedded database (H2, in this case).

We’re still showing you ways to do things that aren’t exactly “the most efficient.” That’s because this chapter is focused on Spring Boot and some of **its** features, not JDBC or other such technologies; we’re purposefully not leveraging some of the things we will cover in subsequent chapters. Some of those things, like Spring Boot itself, will make accomplishing some of these programming tasks much easier and with less code.

First, of course, we need to make sure H2 is available to our project. We also will need to make sure that `spring-boot-starter-jdbc` is in our list of dependencies, in order to – spoiler alert – get Spring Boot to add connection pooling for our database.^([^(90)](#fn90))

Why H2 and not HSQL or Derby? For one thing, H2 is probably the most popular of them; both it and HSQLDB are actively maintained forks of HSQL, but H2 is maintained by HSQL’s original author. Derby is actually surprisingly capable, being nearly a Java version of IBM’s DB2 and maintained by Apache, but it’s also heavier on system resources than H2 or HSQLDB.

```

4.0.0

com.apress
bsg6
1.0

chapter07
1.0
war

com.h2database
h2
runtime

org.springframework.boot
spring-boot-starter-web

org.springframework.boot
spring-boot-starter-jdbc

org.springframework.boot
spring-boot-starter-test
test

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

repackage

Listing 7-10
chapter07/pom.xml with H2 included
```

Note the use of the Spring Boot maven plugin, in the `build` node. This is what allows us to package the Spring Boot app in executable form, ready to deploy and run, along with some other (relatively minor) things.

What’s neat here is that for three embedded databases for Java – H2, HSQLDB, and Derby – Spring Boot can **autoconfigure** our databases for us. All we have to do is include the dependencies, and Boot does the rest. That doesn’t mean we won’t **want** to configure the databases, but for early development and testing, it’s rather convenient, as are many of Spring Boot’s features. In the interest of best practices, however, we’re going to manually configure the database connection.

### Initializing Data with Spring Boot

Spring Boot also runs SQL for us on application startup. It starts by executing the SQL commands contained in files named `schema.sql` and `data.sql` (in that order) from the classpath (so we can locate these files in our source tree at `src/main/resources`, for example). It also will run database-specific scripts based on a platform property – which we’ll show – so that we can have generalized schema set up and then fine-tune the configuration for whichever database we might choose, if we happen to use features that aren’t based on the SQL standard. (In other words, if our schema requires features for a specific database, using that database’s custom SQL, we can locate the custom SQL in a file named specifically for that database.)

Note that our startup scripts are designed for our testing requirements, not for live deployment. We forcibly reset the data to match what our tests require, which is something a live application would not want to do.

We’ve now mentioned autoconfiguration of the database connection pool and a “platform” property. These come from a simple property file in the classpath, called `application.properties`. In our case, we want to set up a simple database connection, with a JDBC URL, a username, a password, and a database driver name. We don’t actually **need** all of these – or any of them, really – but it’s good practice to set them for when you’ll want to change them to something more robust than an embedded database.^([^(91)](#fn91))

Our `application.properties` file for now.

```
spring.datasource.url=jdbc:h2:./chapter07;DB_CLOSE_ON_EXIT=FALSE
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driver-class-name=org.h2.Driver
spring.sql.init.platform=h2
Listing 7-11
chapter07/src/main/resources/application.properties
```

With this, we’re creating a database in the user’s current directory when the application is run, with a database username of `sa` and an empty password (which happens to match the default H2 user profile); we are also explicitly setting the driver class name to `org.h2.Driver`. Lastly, we’re setting the platform to be `h2` as well, so Spring Boot will first attempt to run `schema.sql`, and then `schema-h2.sql`, after which it will run `data.sql` and `data-h2.sql`.

Why would one have database-specific scripts? Well, SQL is generally going to be the same across every database platform, but not quite. Some databases will have nonstandard datatypes or ways to define primary keys, or table relationships; sadly, for as common and as powerful as SQL is, it’s terribly common to require database-specific scripting to accomplish certain tasks – and defining the schema is one of the areas that this is most true.

If you recall our data model in Chapter [3](#471370_2_En_3_Chapter.xhtml), you’ll note that we have two entities to manage: `Artist` and `Song`. This sounds like a suggestion that we have two tables that correspond to those entity names, and we could do that, but we’re not going to.

The reason is rather simple: this chapter is already covering a lot of ground, and covering both `Artist` and `Song` services would take too long and add relatively little (once you understand what’s going on with services related to `Artist`, the services related to `Song` wouldn’t make much difference – but it’d take a lot more room). We’re going to revise how we access data in the next chapter, so it makes more sense for us to focus on the full feature set of services in Chapter [8](#471370_2_En_8_Chapter.xhtml) and not here in Chapter [7](#471370_2_En_7_Chapter.xhtml). We’re going to include the data descriptions here – as SQL comments – just to show you where it would go, but then we’re going to pretend that `Song` doesn’t exist until we hit Chapter [8](#471370_2_En_8_Chapter.xhtml).

```
DROP INDEX IF EXISTS artist_name;
DROP TABLE IF EXISTS artists;
CREATE TABLE IF NOT EXISTS ARTISTS
(
id   BIGINT NOT NULL GENERATED BY DEFAULT AS IDENTITY,
name VARCHAR(64) NOT NULL
);
CREATE UNIQUE INDEX IF NOT EXISTS artist_name
ON ARTISTS(name);
Listing 7-12
chapter07/src/main/resources/schema.sql
```

We also want data in our database when our application starts. In testing, we might clear out the data (and often) so that we know in what condition the database is for a given test. Here’s our `data.sql` file. (If we happen to use a database for which this SQL does not work, we’d be better off creating a platform-specific file **for that database**.)

```
INSERT INTO ARTISTS (NAME)
VALUES ('Threadbare Loaf');
INSERT INTO ARTISTS (NAME)
VALUES ('Therapy Zeppelin');
INSERT INTO ARTISTS (NAME)
VALUES ('Clancy In Silt');
Listing 7-13
chapter07/src/main/resources/data.sql
```

### Building an ArtistService

Now we need to start populating our services – which means, as usual, creating a set of files. We need a way to represent artists – so we’ll create a class, `com.bsg5.chapter07.Artist`, logically enough. We’ll also want a service – a `Repository`, actually, something that actually does the work of interacting with a database – and we’ll call that one `com.bsg6.chapter07.ArtistRepository`. Lastly, we’ll need something to connect the service to the web layer – a controller – so, naturally, we’ll create a `com.bsg6.chapter07.ArtistController`.

Note

If you remember Chapter [3](#471370_2_En_3_Chapter.xhtml) well, you’ll recall that we used a `MusicService` class there that handled all the things we needed to do with our data model. Here, we’re splitting things out, as we said we were going to do back in Chapter [3](#471370_2_En_3_Chapter.xhtml). The reason: expediency. We chose to build the in-memory service in Chapter [3](#471370_2_En_3_Chapter.xhtml) as we did because it was far easier to have a single point of control for our data model, contained in a set of data structures. Here, we have an actual "system of record" – the database – and it makes sense for us to split the interactions out into smaller chunks, because we don’t have to worry about artists messing around with songs, in terms of our data management.

The `Artist` class is a simple POJO, with everything that implies: a no-argument constructor (a default constructor), private fields, a constructor that will initialize the private fields, accessors and mutators,^([^(92)](#fn92)) an implementation of `equals()` and `hashCode()`, and a simple implementation of `toString()` as well. Almost every bit of it was generated by an IDE once the fields were put into the class; there’s nothing special or unique about this implementation at all. (The exceptions: `compareTo()`, which ignores case for the artist names, and `equals()` was modified to do the same thing.) It’s a long class, compared to what it does – it’s simply a container for artist references, after all – but that’s the beauty (or “beauty”) of Java.

Note

Naturally, we have options here. We chose a POJO for `Artist` for simplicity’s sake; we could have used a Java `record` class, for instance.

```
package com.bsg6.chapter07;
import java.util.Objects;
import java.util.StringJoiner;
public class Artist implements Comparable {
private int id;
private String name;
public Artist() {
}
public Artist(int id, String name) {
this.id = id;
this.name = name;
}
public int getId() {
return id;
}
public void setId(int id) {
this.id = id;
}
public String getName() {
return name;
}
public void setName(String name) {
this.name = name;
}
@Override
public String toString() {
return new StringJoiner(", ",
Artist.class.getSimpleName() + "[", "]")
.add("id=" + id)
.add("name='" + name + "'")
.toString();
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof Artist)) return false;
Artist artist = (Artist) o;
return getId() == artist.getId() &&
Objects.equals(
getName().toLowerCase(),
artist.getName().toLowerCase()
);
}
@Override
public int hashCode() {
return Objects.hash(getId(), getName());
}
@Override
public int compareTo(Artist o) {
return o.getName().toLowerCase().compareTo(getName().toLowerCase());
}
}
Listing 7-14
chapter07/src/main/java/com/bsg6/chapter07/Artist.java
```

The `ArtistService` needs a little more explanation.

First, remember that we’re trying to implement the following services, as listed in Chapter [3](#471370_2_En_3_Chapter.xhtml) that relates to artists:

*   Retrieve a list of artist names (for use in autocompletion operations)

The implication is that the other four services we listed in Chapter [3](#471370_2_En_3_Chapter.xhtml) are related to songs instead, even though artists are involved, and this is indeed the case.

The thing is, the other services also imply something about artists as well. Remember that we would want to record that a song exists, as well as vote for one, in our full set of requirements – these are song-related services, to be sure, but the songs also need to be related to artists. Therefore, we should add some basic services to our list of requirements:

*   Get an artist by id.

*   Get artists by name.

*   Save an artist.

The structure of each one of these functions looks a little odd, too. We actually will want two versions of each function: one that accepts just the parameters we need for the function (an `id` if we’re getting an `Artist` by `id`, for example) and another that adds a `Connection` object to those parameters. We’ll do this because we want to be able to compose our methods, and we want the composition to not require a unique database connection for every call. For example, saving an artist might look up the artist by name first; we want to use a single `Connection` for both operations, because that gives us the ability to use one database transaction instead of two. (We’ll see this done later on in this chapter.)

Warning

As with so many other things in this chapter, we’re …​ not actually using database transactions explicitly. We’ll get more into transaction isolation features in Chapter [8](#471370_2_En_8_Chapter.xhtml). In this chapter, though, maintaining transaction state would be distracting.

Let’s take a look at the first two of our `ArtistService` methods, `findArtistById()`. These methods are simple enough: retrieve an `Artist` whose primary key matches the one supplied as an argument.

We’re actually going to have *two* methods – one that accepts a `Connection` and the `id` and one that only requires an `id`. This is done by design so we can propagate transactions through the `Connection` object; the “simple” version of the method can start a transaction and control rollback, and the method that does the actual work can *participate* in a transaction without controlling it. We’ll see more of this pattern in later chapters, until we start to let Spring manage it for us.

```
public Artist findArtistById(int id) throws SQLException {
try (Connection conn = dataSource.getConnection()) {
return findArtistById(conn, id);
}
}
private Artist findArtistById(Connection conn, int id) {
String sql = "SELECT * FROM artists WHERE id=?";
try (PreparedStatement ps = conn.prepareStatement(sql)) {
ps.setInt(1, id);
try (ResultSet rs = ps.executeQuery()) {
if (rs.next()) {
return new Artist(id, rs.getString("name"));
} else {
throw new ArtistNotFoundException(id +
" not found in artist database");
}
}
} catch (SQLException e) {
throw new ArtistNotFoundException(e);
}
}
Listing 7-15
chapter07/src/main/java/com/bsg6/chapter07/services/ArtistService.java excerpt
```

What we see is a simple enough mechanism: if we call the `findArtistById(int)`, we allocate a `Connection` with Java’s try-with-resources (which means that when exiting the `try` block, the `Connection` will get closed properly without us having to write explicit code for that purpose), and then, inside the `try` block, delegate to an overloaded method of the same name, with that `Connection`.

The overloaded method simply creates a `PreparedStatement` and uses it to query the database for a matching `Artist`. If it finds one, it constructs an `Artist` with the data from the query and returns it. If one isn’t found, it throws a custom exception – `ArtistNotFoundException`.

### Handling Exceptions in Spring Boot

We create custom exceptions because we want to handle the different paths in different ways. A generic exception would work, but a custom exception allows us to assign semantic meaning to the exception, and handle it precisely. In fact, now that we’ve mentioned the exception, we should take a quick look at it before looking at the rest of `ArtistService.java`.

```
package com.bsg6.chapter07;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ArtistNotFoundException extends RuntimeException {
public ArtistNotFoundException(String message) {
super(message);
}
public ArtistNotFoundException(Exception e) {
super(e);
}
}
Listing 7-16
chapter07/src/main/java/com/bsg6/chapter07/ArtistNotFoundException.java
```

This is a very vanilla exception class, with one line that we really want to notice: `@ResponseStatus(HttpStatus.NOT_FOUND)`. This tells a Spring `@Controller` that this exception should map to a specific HTTP status code – in this case, it’s 404, corresponding to “not found,” as the name clearly implies. If we don’t set an explicit response status, the exceptions will be considered service errors, which corresponds to HTTP status 500\. Using the precise HTTP error code means that if this exception gets returned from a `Controller`, it should be treated as a “resource not found” – which, from the name, is **exactly** what it represents. (This feels obvious, but we’d have been remiss if we didn’t point it out.)

It’s also worth pointing out that it inherits from `RuntimeException`. This is typical for exceptions in Spring, as it means we don’t have to specify thrown exceptions in method signatures, keeping our code cleaner. There are many ways of looking at this practice, but **most** languages don’t have an analog to Java’s explicit error mechanisms, even on the Java Virtual Machine, and most programmers seem to be okay with writing less verbose code.

Now we can look at the rest of the `ArtistRepository`. Once we understand the method overloading being used, it’s really pretty simple, despite the length of the class. Also worth noting: the use of `@Repository` on the class, and its construction with a `DataSource`. Marking it as a `@Repository` describes its specific architectural role (it encapsulates behavior that interacts with a data storage system) and enables its participation in translating some exception types (a feature we’re not going to describe in this chapter).

### The Actual Implementation of `ArtistService` and Its Little `Controller`, Too

```
package com.bsg6.chapter07;
import org.springframework.stereotype.Repository;
import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;
@Repository
public class ArtistRepository {
private DataSource dataSource;
public ArtistRepository(DataSource dataSource) {
this.dataSource = dataSource;
}
public Artist findArtistById(int id) throws SQLException {
try (Connection conn = dataSource.getConnection()) {
return findArtistById(conn, id);
}
}
private Artist findArtistById(Connection conn, int id) {
String sql = "SELECT * FROM artists WHERE id=?";
try (PreparedStatement ps = conn.prepareStatement(sql)) {
ps.setInt(1, id);
try (ResultSet rs = ps.executeQuery()) {
if (rs.next()) {
return new Artist(id, rs.getString("name"));
} else {
throw new ArtistNotFoundException(id +
" not found in artist database");
}
}
} catch (SQLException e) {
throw new ArtistNotFoundException(e);
}
}
public Artist saveArtist(String name) throws SQLException {
try (Connection conn = dataSource.getConnection()) {
try {
return saveArtist(conn, name);
} catch (SQLException e) {
e.printStackTrace();
return findArtistByName(conn, name);
}
}
}
private Artist saveArtist(Connection conn, String name)
throws SQLException {
String sql = "INSERT INTO ARTISTS (NAME) VALUES (?)";
try (PreparedStatement ps = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {
ps.setString(1, name);
ps.executeUpdate();
try (ResultSet rs = ps.getGeneratedKeys()) {
rs.next();
int id = rs.getInt(1);
return new Artist(id, name);
}
}
}
public Artist findArtistByName(String name) throws SQLException {
try (Connection conn = dataSource.getConnection()) {
return findArtistByName(conn, name);
}
}
private Artist findArtistByName(
Connection conn,
String name
) throws SQLException {
String sql = "SELECT * FROM artists WHERE LOWER(name)=LOWER(?)";
try (PreparedStatement ps = conn.prepareStatement(sql)) {
ps.setString(1, name);
try (ResultSet rs = ps.executeQuery()) {
if (rs.next()) {
return new Artist(
rs.getInt("id"),
rs.getString("name")
);
} else {
throw new ArtistNotFoundException(name +
" not found in artist database");
}
}
}
}
public List findAllArtistsByName(String name)
throws SQLException {
try (Connection conn = dataSource.getConnection()) {
return findAllArtistsByName(conn, name);
}
}
private List findAllArtistsByName(
Connection conn,
String name
) throws SQLException {
String sql = "SELECT * FROM artists WHERE LOWER(name) LIKE LOWER(?)"
+ " ORDER BY name";
List artists = new ArrayList();
try (PreparedStatement ps = conn.prepareStatement(sql)) {
ps.setString(1, name + "%");
try (ResultSet rs = ps.executeQuery()) {
while (rs.next()) {
artists.add(new Artist(
rs.getInt("id"),
rs.getString("name")
));
}
}
}
return artists;
}
}
Listing 7-17
chapter07/src/main/java/com/bsg6/chapter07/ArtistRepository.java
```

There’s nothing spectacular in play in this particular implementation of `ArtistRepository`, including the SQL itself.

However, `saveArtist` could use some explanation – it actually returns a valid `Artist`. It does this by first trying to save an `Artist` to the database – but what if the data is already there? We’d get an exception (a `SQLException`, indication a violation of a unique key constraint). This method, if it gets that `SQLException`, assumes the `Artist` is already in the database, and queries for that `Artist` data and returns that instead – so we always get a valid “saved” `Artist`, even if we didn’t actually create the `Artist` data in this specific call.

Note

We’re still cheating (slightly) for the sake of expediency; we’re not checking to make sure that the `SQLException` in question was caused by an index violation, which is the only case in which we’d want to follow this logic.

This class uses wildcard matching to do searches in `findAllArtistsByName` – pass in `foo` and it will search for all artists whose names start with `foo`, by using a parameter value of `foo%` – and it uses the SQL `LOWER()` function to remove case significance from the queries. But apart from that (and, of course, try-with-resources [[`https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html`](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)]), it’s no different from JDBC code written from the earliest days of JDBC. Naturally, from Spring’s perspective, this is inefficient and rather awful – Spring has excellent mechanisms for data access that we’re **completely** ignoring here – but again, that’s more a subject for the next chapter.

We have one more class we need to see to fulfill our service functionality – the `Controller` itself, which mostly delegates to the `ArtistRepository`. It’s actually quite short, and thankfully so, simply having four exposed endpoints.

```
package com.bsg6.chapter07;
import org.springframework.http.HttpStatus;
import org.springframework.http.ProblemDetail;
import org.springframework.web.bind.annotation.*;
import java.net.URI;
import java.sql.SQLException;
import java.util.List;
@RestController
public class ArtistController {
private ArtistRepository service;
public ArtistController(ArtistRepository service) {
this.service = service;
}
@GetMapping("/artist/{id}")
Artist findArtistById(@PathVariable int id) throws SQLException {
return service.findArtistById(id);
}
@GetMapping({"/artist/search/{name}", "/artist/search/"})
Artist findArtistByName(
@PathVariable(required = false) String name
) throws SQLException {
if (name != null) {
return service.findArtistByName(name);
} else {
throw new IllegalArgumentException("No artist name submitted");
}
}
@PostMapping("/artist")
Artist saveArtist(@RequestBody Artist artist) throws SQLException {
return service.saveArtist(artist.getName());
}
@GetMapping({"/artist/match/{name}", "/artist/match/"})
List findArtistByMatchingName(
@PathVariable(required = false)
String name
) throws SQLException {
return service.findAllArtistsByName(name != null ? name : "");
}
@ExceptionHandler(ArtistNotFoundException.class)
ProblemDetail handleArtistNotFound(ArtistNotFoundException e) {
ProblemDetail problemDetails = ProblemDetail
.forStatusAndDetail
(HttpStatus.NOT_FOUND, e.getLocalizedMessage());
problemDetails.setTitle("Artist Not Found");
return problemDetails;
}
}
Listing 7-18
chapter07/src/main/java/com/bsg6/chapter07/ArtistController.java
```

Note the use of specific annotations for HTTP `GET` and `POST` methods. For the methods annotated with `@GetMapping`, the parameters are embedded as part of the URL mapping itself, and these are all fairly simple methods.

The `saveArtist()` method uses `@PostMethod`, though, and has a parameter of `Artist`. This means that an `Artist` model is expected to be passed to the method in the HTTP content – which is fairly standard for REST services. We don’t have to worry about doing any conversions from JSON or XML to our `Artist` class, though, because Spring takes care of all of that for us, as discussed in Chapter [6](#471370_2_En_6_Chapter.xhtml).

We also see a method, called `handleArtistNotFound()`, that returns a `ProblemDetail` to the caller. Our example here is rather pitifully underspecified; it merely converts the exception to a `ProblemDetail` and adds a title to the report. If we had more fine-grained `ArtistNotFoundException` instances that differentiated between artists not being found by `id` or by `name`, we could have more fine-grained problem handlers and give far more information to callers about what the actual error is.

### Testing Our `ArtistController`: Does It Work?

We actually do have one more class we’d like to see, though, and it’s pretty important: it’s the `TestArtistController` class, which is actually the longest class in the entire chapter, spanning more than 150 lines.

```
package com.bsg6.chapter07;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.core.ParameterizedTypeReference;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.util.List;
import static org.testng.Assert.*;
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class TestArtistController extends AbstractTestNGSpringContextTests {
@Autowired
private TestRestTemplate restTemplate;
@DataProvider
Object[][] artistData() {
return new Object[][]{
new Object[]{1, "Threadbare Loaf"},
new Object[]{2, "Therapy Zeppelin"},
new Object[]{3, "Clancy in Silt"},
new Object[]{-1, null},
new Object[]{-1, "Not A Band"}
};
}
@Test(dataProvider = "artistData")
public void testGetArtist(int id, String name) {
String url = "/artist/" + id;
ResponseEntity response =
restTemplate.getForEntity(url, Artist.class);
if (id != -1) {
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist artist = response.getBody();
Artist data = new Artist(id, name);
assertEquals(artist, data);
} else {
assertEquals(response.getStatusCode(), HttpStatus.NOT_FOUND);
}
}
@Test(dataProvider = "artistData")
public void testSearchForArtist(int id, String name) {
String url = "/artist/search/" +
(name != null ? name : "");
ResponseEntity response =
restTemplate.getForEntity(url, Artist.class);
if (name != null) {
if (id == -1) {
assertEquals(response.getStatusCode(),
HttpStatus.NOT_FOUND);
} else {
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist artist = response.getBody();
Artist data = new Artist(id, name);
assertEquals(artist, data);
}
} else {
assertEquals(response.getStatusCode(),
HttpStatus.INTERNAL_SERVER_ERROR);
}
}
/*
* This method tries to save an artist that should already
* exist in the database; this will validate that the
* Repository's saveArtist() method returns a valid artist
* in all cases, as it should return the original artist reference.
*/
@Test
public void testSaveExistingArtist() {
String url = // "http://localhost:" + port +
"/artist";
Artist newArtist =
restTemplate.getForObject(url + "/1", Artist.class);
ResponseEntity response =
restTemplate.postForEntity(url, newArtist, Artist.class);
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist artist = response.getBody();
assertNotNull(artist);
int id = artist.getId();
assertEquals(id, newArtist.getId());
assertEquals(artist.getName(), newArtist.getName());
response =
restTemplate.getForEntity(url + "/" + id, Artist.class);
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist foundArtist = response.getBody();
assertNotNull(foundArtist);
assertEquals(artist, foundArtist);
}
@DataProvider
public Object[][] artistSearches() {
return new Object[][]{
new Object[]{"", 3},
new Object[]{"T", 2},
new Object[]{"Th", 2},
new Object[]{"Thr", 1},
new Object[]{"C", 1},
new Object[]{"Z", 0}
};
}
@Test(dataProvider = "artistSearches")
public void testSearches(String name, int count) {
// this is used to help Spring figure out what types
// are returned by restTemplate.exchange()
ParameterizedTypeReference> type =
new ParameterizedTypeReference() {
};
String url = "/artist/match/" + name;
ResponseEntity> response = restTemplate.exchange(
url,
HttpMethod.GET,
null,
type
);
assertEquals(response.getStatusCode(), HttpStatus.OK);
List artists = response.getBody();
assertNotNull(artists);
assertEquals(artists.size(), count);
}
/*
* We need this to run AFTER testSearches completes, because
* testSaveArtist() adds to the artist list and therefore we
* might get one more artists than we're expecting out of
* some searches.
*/
@Test(dependsOnMethods = "testSearches")
public void testSaveArtist() {
String url = //"http://localhost:" + port +
"/artist";
Artist newArtist = new Artist(0, "The Broken Keyboards");
ResponseEntity response = restTemplate.postForEntity(
url,
newArtist,
Artist.class
);
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist artist = response.getBody();
assertNotNull(artist);
int id = artist.getId();
assertNotEquals(id, 0);
assertEquals(artist.getName(), newArtist.getName());
response =
restTemplate.getForEntity(url + "/" + id, Artist.class);
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist foundArtist = response.getBody();
assertNotNull(foundArtist);
assertEquals(artist, foundArtist);
}
}
Listing 7-19
chapter07/src/test/java/com/bsg6/chapter07/TestArtistController.java
```

Naturally, it looks more difficult than it actually is; it has a series of tests that follow the same pattern that we saw in `TestGreetingController`, in that they build a request and issue it against the `ArtistController` endpoint, then check for the expected HTTP response and the expected HTTP entity content (i.e., it makes sure that we get the right HTTP status, and then we check the data the response contained). Most of the code is built around making assertions based on the data we know our database will contain; `artistData()` (one of our data provider methods) contains the `Artist` names and identifiers, and `artistSearches()` keeps track of the number of responses matching the content supplied (if the request contains `"Th,"` then artists `Threadbare Loaf` and `Therapy Zeppelin` should be returned, for a record count of `2`).

There’s one method that needs to take place **after** `testSearches()` – `testSaveArtist()` – because `testSaveArtist()` mutates the database and potentially changes the result of one of the searches. It really wouldn’t matter which one executes first, but the order should be deterministic. (Otherwise, the methods won’t be able to predict the database state, and that’s not a good thing.)

## Next Steps

In this chapter, we introduced Spring Boot – a project that integrates many of Spring’s features into a single umbrella project, making dependency management easier and offering many services as convention rather than requiring the developer to pick and choose modules to include. We also demonstrated how to create and configure a microcontainer such that our process from compilation to execution became much simpler.

In the next chapter, we’re going to cover one of this chapter’s weakest aspects: data access, with Spring Data.

Footnotes [1](#471370_2_En_7_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_7_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_7_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_7_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_7_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_7_Chapter.xhtml#Fn6_source)  

# 8. Spring Data Access with JdbcTemplate

It’s time we started looking at how we actually access data. Spring has multiple ways of accessing data; here, we’re going to look at `JdbcTemplate`, a facade that provides trivial access to common operations, and we’re going to address some of Chapter [7](#471370_2_En_7_Chapter.xhtml)’s other issues with data access.

## Introduction

Chapter [7](#471370_2_En_7_Chapter.xhtml) was an “umbrella” chapter, a chapter focused on introducing Spring Boot. We then walked through some of the common things Spring Boot does for us, like handling dependency resolution and providing an easy starting point for features developers use often like trivial web deployment, a conveniently executable archive, and nearly transparent database configuration.

We followed all of that up with database access that most experienced developers – and many inexperienced ones – would describe as, perhaps, “quaint.” We also made a slight reference to a crucial problem in our database access code – one that ended up limiting our services to working with one of our two database entities, because solving it in the context of Chapter [7](#471370_2_En_7_Chapter.xhtml) would be more work than it was worth. Instead, we chose to model simpler things in Chapter [7](#471370_2_En_7_Chapter.xhtml), to defer proper database access to **this** chapter and the next.

What do you think the “crucial problem” was in Chapter [7](#471370_2_En_7_Chapter.xhtml), besides the obvious answer of “There was no reference to a ‘Song’”? Spoiler in three…​ two…​ one…​.

Chapter [7](#471370_2_En_7_Chapter.xhtml) didn’t manage transactions at all. There was no way to address coordination of database requests without introducing database abstractions that were out of scope for Chapter [7](#471370_2_En_7_Chapter.xhtml). Instead, we chose to keep things very simple, and lay the groundwork for Chapters [8](#471370_2_En_8_Chapter.xhtml) and [9](#471370_2_En_9_Chapter.xhtml), both of which address database access in far more elegant (and more correct) ways.

In this chapter, we’re going to demonstrate the use of `JdbcTemplate`, which itself serves as a model for many of Spring’s simpler data access facilities. JdbcTemplate gives us a single, common workflow model, as well as providing easy mechanisms to map from relational data into classes as well as a simple model for exception handling and central logging. We’re also going to round out what should have been Chapter [7](#471370_2_En_7_Chapter.xhtml)’s feature set, by adding controllers to access all of our data, as well as tests for everything.

## Project Setup

This is a simple, fairly straightforward project. First, let’s create our project directory structure.

```
mkdir -p chapter08/src/main/java/com/bsg6/chapter08
mkdir -p chapter08/src/main/resources
mkdir -p chapter08/src/test/java/com/bsg6/chapter08
mkdir -p chapter08/src/test/resources
Listing 8-1
Creating the directory structure with POSIX
```

As always, we need a `pom.xml`. We’re going to use the Spring Boot dependency resolution we saw first in Chapter [7](#471370_2_En_7_Chapter.xhtml) and include four things of particular note. Let’s take a look at the `pom.xml,` and then we’ll look at the dependencies in more detail.

```

4.0.0

com.apress
bsg6
1.0

chapter08
1.0
war

com.h2database
h2
runtime

org.springframework.boot
spring-boot-starter-web

org.springframework.boot
spring-boot-starter-jdbc

org.springframework.boot
spring-boot-starter-test
test

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

Listing 8-2
chapter8/pom.xml
```

First, note that we’re including `spring-boot-starter-jdbc`. This will bring in everything Spring needs to make sure we have Spring’s JDBC support and ecosystem, which means we get all kinds of goodies like a connection pool (HikariCP^([^(93)](#fn93)) by default) test-specific datasources and data loading features (as described in Chapter [7](#471370_2_En_7_Chapter.xhtml), with `schema.sql` and `data.sql`).

The next dependency we want to make sure we’ve included is actually the least relevant, because it’s one we saw in Chapter [7](#471370_2_En_7_Chapter.xhtml): `spring-boot-starter-web`. We’re including it here because this chapter will in fact contain a fully working backend for our band gateway.

## Our Entity and Data Models

We’re using the same entity model we described in Chapter [3](#471370_2_En_3_Chapter.xhtml) and that we’ve used since then. However, we want to change the **data** model slightly, to make data management more efficient.

What’s the difference between an “entity model” and a “data model”? Well, in concrete terms, they’re largely interchangeable. There’s not a formal definition that one would use to say “this is an entity model, that is a data model.” However, colloquially speaking – and as used here – an entity model describes the overall relationships between things we’re working with (the entities), and a data model is a more specific description of the actual managed elements.

For example, in an **entity** model, an `Artist` exists (therefore, it’s an entity!), and it has a `name`. In a data model, we might include things that make working with an `Artist` more convenient – a generated primary key, or a count of the songs for that `Artist`, for example – things that might be relevant for an `Artist` from a programming standpoint, but that aren’t necessarily part of the definition of an `Artist` as a concept.

Our entity model (again, chapter [3](#471370_2_En_3_Chapter.xhtml)) looks like this.

![](images/471370_2_En_8_Chapter/471370_2_En_8_Fig1_HTML.jpg)

An entity-relationship model between artist and song. E artist with attribute I d integer is interconnected to E song with attributes title string and votes integer via one-to-many relationship, respectively.

Figure 8-1

Entity model

Our **data** model adds a few attributes to the entities, to create relationships and make managing the objects much more efficient.

![](images/471370_2_En_8_Chapter/471370_2_En_8_Fig2_HTML.jpg)

A data-relationship model between artist and song. E artist with attributes I d integer and name string is interconnected to E song with attributes I d integer, artist I d integer, title string, and votes integer via one-to-many relationship, respectively.

Figure 8-2

Data model

This isn’t entirely descriptive – we’re not including nullability or sizes into our models, but we’re also not **quite** trying to build a formal entity model, either. We’re mostly thinking about how we design our class structure. This is the “right way” to model data when you’re proceeding from a SQL-first access model (i.e., when you anticipate using JDBC to access your data, as we are in this chapter); in our next chapter, when we look at Spring Data, we can actually design our classes first and then make sure they generate a workable database schema, rather than starting with the schema and building a workable class structure.

Here’s a full implementation of the `Artist` class.

```
package com.bsg6.chapter08;
import org.springframework.lang.NonNull;
import java.util.Objects;
import java.util.StringJoiner;
public class Artist {
Integer id;
@NonNull
String name;
public Artist() {}
public Artist(@NonNull String name) {
this.name=name;
}
public Artist(Integer id, @NonNull String name) {
this.id = id;
this.name = name;
}
public Integer getId() {
return id;
}
public void setId(Integer id) {
this.id = id;
}
@NonNull
public String getName() {
return name;
}
public void setName(@NonNull String name) {
this.name = name;
}
@Override
public String toString() {
return new StringJoiner(", ", Artist.class.getSimpleName() + "[", "]")
.add("id=" + id)
.add("name='" + name + "'")
.toString();
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof Artist artist)) return false;
return Objects.equals(getId(), artist.getId()) && Objects.equals(getName(), artist.getName());
}
@Override
public int hashCode() {
return Objects.hash(getId(), getName());
}
}
Listing 8-3
chapter8/src/main/java/com/bsg6/chapter08/Artist.java
```

Careful readers will note the use of `@NonNull`. This brings up an interesting point that’s been referenced before in some of our earlier chapters: Spring impacts your design and object models, but only as much as *you want it to*. That `@NonNull` means that when Spring itself constructs the model, or applies validation to the model (as when used from a `@Controller` or in some other similar cases), it can examine the validation and suggest whether the model is constructed properly or not.

But when Spring is *not* involved – or when it’s only involved in certain ways – those annotations make no difference. Annotations are applied by code at runtime, after all, and unless something examines the annotated structure and takes action based on the annotations, *nothing happens*.

So those annotations have value, but it’s important to note that their presence doesn’t mean anything for *ordinary code* – it’s informative more than anything else, and if we write vanilla Java code, those annotations have no bearing on anything. A `@NonNull` annotation, as used here, means that a validation mechanism can ensure that no `null` values pass through the *validation layer*, but the values can still be set to `null` by the JVM itself or by a determined programmer.

Now let’s see what a `Song` looks like.

```
package com.bsg6.chapter08;
import org.springframework.lang.NonNull;
import java.util.Objects;
import java.util.StringJoiner;
public class Song {
Integer id;
@NonNull
Integer artistId;
@NonNull
String name;
int votes;
public Song() {
}
public Song(Integer id, @NonNull Integer artistId, @NonNull String name, int votes) {
this.id = id;
this.artistId = artistId;
this.name = name;
this.votes = votes;
}
public Integer getId() {
return id;
}
public void setId(Integer id) {
this.id = id;
}
@NonNull
public Integer getArtistId() {
return artistId;
}
public void setArtistId(@NonNull Integer artistId) {
this.artistId = artistId;
}
@NonNull
public String getName() {
return name;
}
public void setName(@NonNull String name) {
this.name = name;
}
public int getVotes() {
return votes;
}
public void setVotes(int votes) {
this.votes = votes;
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof Song song)) return false;
return getVotes() == song.getVotes()
&& Objects.equals(getId(), song.getId())
&& Objects.equals(getArtistId(), song.getArtistId())
&& Objects.equals(getName(), song.getName());
}
@Override
public int hashCode() {
return Objects.hash(getId(), getArtistId(), getName(), getVotes());
}
@Override
public String toString() {
return new StringJoiner(", ", Song.class.getSimpleName() + "[", "]")
.add("id=" + id)
.add("name='" + name + "'")
.add("votes=" + votes)
.toString();
}
}
Listing 8-4
chapter08/src/main/java/com/bsg6/chapter08/Song.java
```

There’s nothing magical here; we’re basically echoing our data model in Java source.

We will want to manually create our database schema as described in Chapter [7](#471370_2_En_7_Chapter.xhtml), by creating a `schema.sql` file. These files don’t go in the same parts of the source tree, because the `application.properties` is going to apply only to our tests – we might want to use a different database in production, after all – but our `schema.sql` would be appropriate no matter what the H2 instance happens to be, whether in-memory (as in our tests) or as an external database, or whatever it might be.

H2 can run as an embedded database or as a separate database server. In addition, it can create databases on disk or in-memory, so we could have one of four different configurations.

| Embedded and in-memory | Useful for testing |
| --- | --- |
| Embedded and on-disk | Useful for applications that run in single containers and therefore don’t need to share access |
| External and in-memory | Useful for creating side-caches, perhaps; this would be fairly rare in the real world |
| External and on-disk | Useful for application where multiple processes use the database, and mirrors how most other databases are used |

First, the simple (and blessedly short) `application.properties`, which we’re going to put in `chapter08/src/test/resources` – remember, this is for testing only, so we **want** it in the testing directory tree. (If we want a production version, we can create that in `chapter08/src/main/resources`, with specific properties for production use – like an explicit JDBC URL and so forth. The version in the `test` directory tree would have precedence for our tests.)

The main purpose of this file is to make sure that Spring Boot knows what database platform we’re using, so it will use `schema.sql` to create our database tables.

```
spring.sql.init.platform=h2
Listing 8-5
chapter08/src/test/resources/application.properties
```

Our `schema.sql` file – which goes into our `chapter8/src/main/resources` directory, as it’s a schema that we would want in a production application (not just for tests) – is fairly simple as well, and models our entities in about as straightforward a fashion as can be done while representing our data model’s intent.

```
CREATE TABLE IF NOT EXISTS artists
(
id   BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
name VARCHAR(64) NOT NULL
);
CREATE UNIQUE INDEX IF NOT EXISTS artist_name
ON artists(name);
CREATE TABLE IF NOT EXISTS songs
(
id        BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
artist_id INT,
name      VARCHAR(64) NOT NULL,
votes     INT DEFAULT 0,
FOREIGN KEY (artist_id) REFERENCES artists (id)
ON UPDATE CASCADE
);
CREATE UNIQUE INDEX IF NOT EXISTS song_artist
ON SONGS (artist_id, name);
Listing 8-6
chapter08/src/main/resources/schema.sql
```

The foreign key for `songs` deliberately cascades updates through the foreign key – so if we update an `artist` record’s `id` field, it should propagate to the `songs` table. We don’t cascade deletes quite on purpose; imagine if we had an artist with forty songs recorded. Deleting the artist would delete all of their song data as well. Not propagating deletions means that we’d have to **explicitly** delete the `songs` records associated with an artist before deleting the artist’s data as well.

Lastly, it’d be nice to have some test data by default. Foreshadowing: we’re going to have three different test classes in this chapter, and two of them will create their own data, but one of them won’t. (`ArtistControllerTest` is a read-only test. As such, we need to make sure it has the data it needs in order to run properly. Thus, default data!) In anticipation of that one test, let’s go ahead and take a look at our default database content, which is located in `chapter8/src/test/resources/data.sql`.

```
INSERT INTO ARTISTS (NAME)
VALUES ('Threadbare Loaf');
INSERT INTO ARTISTS (NAME)
VALUES ('Therapy Zeppelin');
INSERT INTO ARTISTS (NAME)
VALUES ('Clancy In Silt');
INSERT INTO SONGS (ARTIST_ID, NAME, VOTES)
VALUES ((select id from artists where name like 'Thre%'),
'Someone Stole the Flour', 4);
INSERT INTO SONGS (ARTIST_ID, NAME, VOTES)
VALUES ((select id from artists where name like 'Thre%'),
'What Happened to Our First CD?', 17);
INSERT INTO SONGS (ARTIST_ID, NAME, VOTES)
VALUES ((select id from artists where name like 'The%'),
'Medium', 4);
INSERT INTO SONGS (ARTIST_ID, NAME, VOTES)
VALUES ((select id from artists where name like 'C%'),
'Igneous', 5);
Listing 8-7
chapter08/src/test/resources/data.sql
```

Note

What’s up with that `INSERT`? Because of the way we’re relying on autogenerated keys for `ARTISTS`, we don’t want to rely on the database’s sequence retention. Those `INSERT` statements are looking up matching `ARTISTS` for the insertion of data. If the database manufactures a sequence for the primary keys for `ARTISTS`, the assumption of the primary key would likely be wrong – this futureproofs the SQL, a lesson your authors have had to learn the hard way. Thankfully, this is a problem usually limited to test data generation.

The last class we want to include here is a configuration. As we’re using Spring Boot, this configuration relies on sensible defaults (or defaults constructed from `application.properties`) – so it’s nearly completely empty, with the annotations driving everything.

```
package com.bsg6.chapter08;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class JdbcConfiguration {
}
Listing 8-8
chapter8/src/main/java/com/bsg6/chapter08/JdbcConfiguration.java
```

Here, we’re telling Spring that it’s a Spring Boot configuration, to scan this package (and packages whose names start with this one, so any class in the `com.bsg6,chapter08` package hierarchy) for Spring components, and to enable Spring Web configuration.

Now we finally get to start **doing things**. All this was necessary to varying degrees, and all of it’s relevant, but it’s mostly been *preparatory* for actually working with data.

## Accessing Data

Let’s revisit our `MusicService`, as we’ve seen in Chapter [3](#471370_2_En_3_Chapter.xhtml) and in subsequent chapters. Our service has five primary operations:

*   Retrieve songs for an artist, ordered by popularity (the most popular song is the better “hook”).

*   Retrieve song names for an artist (for use in autocompletion operations, which we’re anticipating a more full-featured application would use).

*   Retrieve a list of artist names (for use in autocompletion operations).

*   Record that a song exists.

*   Vote for a song as a hook for a given `Artist.`

The mechanism to implement these with `JdbcTemplate` is really rather easy. What we want to do is have a class, `MusicService`, that receives a `JdbcTemplate` as an autowired attribute. It can then use `JdbcTemplate.query()` and `JdbcTemplate.update()` to issue SQL against the database as needed. Let’s take a look at what an implementation of `MusicService` might look like with only one method implemented.

Probably the simplest of the methods in our requirements is the one to retrieve matching artist names, such that if we pass in a “T” we get a list of strings reflecting artist names that, well, start with the letter “T.” For simplicity’s sake, we’ll make articles like “the,” “a,” and “an”^([^(94)](#fn94)) significant – that is, “The Who” and “Who” are different band names for the purpose of matching artists. There are definitely ways of making the articles optional, and that would actually be a good idea for an enhanced version of the API, but that’s outside of the scope of this chapter.

If you’re interested, one way this could be accomplished is by adding another field to the `Artist` table, a field that contained the name with all of the articles stripped out. You might also “stem” the remaining words – where “stemming” means translating the word to its base, most simple form, so that a word like apply a translation phase called “stemming” to the words to reduce them to their most rudimentary form; “improbably” becomes “improb,” and “threadbare loaf” becomes `threadbar loaf`. Then you’d use this artificial and internal-use-only content to do matches, perhaps in combination with the actual band name. (After all, you would want a search for “The Who” to work when typing `Th` and not just `Wh`.) This isn’t even beginning to look at access patterns or machine learning for such purposes. As we said, this is better left as an exercise for future enhancement, because this exercise alone would fill a few chapters of a book.

If you’re interested, there are some research papers on the topic, including

*   “Algorithmic and user study of an autocompletion algorithm on a large medical vocabulary”; [`www.sciencedirect.com/science/article/pii/S153204641100164X`](http://www.sciencedirect.com/science/article/pii/S153204641100164X)`.`

*   Apache Solr’s `Suggester` implementation, which can hide a lot of details from you; [`https://lucene.apache.org/solr/guide/7_7/suggester.html`](https://lucene.apache.org/solr/guide/7_7/suggester.html)`.`

*   Naturally, with Java’s giant ecosystem, someone ported something from Python that can help quite a bit: Python’s `fuzzywuzzy` library. See [`https://github.com/xdrop/fuzzywuzzy`](https://github.com/xdrop/fuzzywuzzy) for more details.

*   Lastly, a good overview that provides insight into autocomplete at Bing can be found at `https://`[`blogs.bing.com/search-quality-insights/September-2016/more-intelligent-autocomplete`](http://blogs.bing.com/search-quality-insights/September-2016/more-intelligent-autocomplete).^([^(95)](#fn95))

Others can be found, although searching on Google (or Bing, or DuckDuckGo, or whatever search engine you prefer) will return a lot of user-interface-oriented autocompletion mechanisms and not so much server-side information. Good hunting!

We’ll get to the full implementation of `MusicRepository` soon, but let’s look at an incomplete version just to get our feet wet with the API.

```
@Repository
public class MusicRepository {
JdbcTemplate jdbcTemplate;
MusicRepository(JdbcTemplate template) {
jdbcTemplate=template;
}
@Transactional
public List getMatchingArtistNames(String prefix) {
String selectSQL = "SELECT name FROM artists WHERE " +
"lower(name) like lower(?) " +
"order by name asc";
/*
* Note use of Object[] for query arguments, and
* the use of a lambda to map from a row to a String
*/
return jdbcTemplate.query(
selectSQL,
new Object[]{prefix + "%"},
(rs, rowNum) -> rs.getString("name"));
}
}
Listing 8-9
Part of chapter08/src/main/java/com/bsg6/chapter08/MusicRepository.java
```

The core observations:

*   `MusicRepository` is marked with `@Repository` and is therefore a Spring bean.

*   We have a `JdbcTemplate` reference provided by the constructor; Spring will automatically provide this for us.

*   We have a single method, annotated with `@Transactional`, that calls `JdbcTemplate.query()`.

There are two things about `getMatchingArtistNames()` that we want to think about. The first is the `query()` call itself – what with this chapter centering on `JdbcTemplate` – and the other is about transaction support.

### `JdbcTemplate.query()`

There are **nineteen** variants of `query()` in `JdbcTemplate`, and that’s not even counting query methods like `queryForMap`, `queryForObject`, and `queryForList`, each of which has a series of overloaded signatures available. (There are more than forty methods whose names start with `query` in the `JdbcTemplate` class.) The one used in Listing [8-12](#471370_2_En_8_Chapter.xhtml#PC16) is one of the simplest.

```
public  List query(
String sql,
@Nullable Object[] args,
RowMapper rowMapper
) throws DataAccessException
```

The first argument here is a SQL query. As with JDBC, an interrogative symbol – `?`, the question mark – is used as a placeholder.

The second argument is an array of `Object`. Each element of this array is placed into the SQL query, corresponding with the ordinal position – which means that the first `?` is replaced by the first element in the `Object[]`, the second `?` is replaced by the second element in the `Object[]`, and so forth and so on.

This version accepts a `RowMapper<T>` as a final argument. A `RowMapper<T>` is a simple interface with one method (and returning a specific type, represented by `<T>`), making it a “Single Access Method” class in Java – which means it can be represented by a lambda. A `RowMapper<T>` is responsible for accepting a `ResultSet` and a row number – an `int` – and mapping that row into a type of some kind; our method for querying for artist names, then, is responsible for accepting a `ResultSet` and returning an artist’s name from that ResultSet’s current row. In lambda form, it looks like the following.

```
(rs, rowNum) -> rs.getString("name")
```

In “traditional Java” form, it would look a little longer.

```
new RowMapper() {
@Override
String mapRow(ResultSet rs, int rowNum) throws SQLException {
return rs.getString("name");
}
}
```

Either form is acceptable; they both actually compile to nearly the same code in practice, and they accomplish exactly the same thing.

This method, then, is horribly simple: it contains a SQL statement, with a parameter placeholder, that retrieves artist names from an `artists` table. The parameter is mangled to add a SQL wildcard character (“%”) for use by the SQL `LIKE` operator, and the resulting data is translated from a `ResultSet` into a single `String` for each row – and as `query()` returns a typed `List`, we’re able to return the resulting `List<String>` to the caller. (We could have used any Java type as the return type for a `RowMapper`; it just so happens that here we’re working with simple `String` instances. We also don’t need to worry about coercing the types, because the declarations give the compiler enough information that the compiler knows what types are being returned.)

As `query()` returns a `List`, we can then manipulate the `List;` however, we like to get specific results. Consider a method where we might look an `Artist` up by its `id`.

```
@Repository
public class MusicRepository {
JdbcTemplate jdbcTemplate;
MusicRepository(JdbcTemplate template) {
jdbcTemplate=template;
}
@Transactional
public Artist findArtistById(Integer id) {
return jdbcTemplate.query(
"SELECT id, name FROM artists WHERE id=?",
new Object[]{id},
(rs, rowNum) ->
new Artist(
rs.getInt("id"),
rs.getString("name"
)
)
)
.stream()
.findFirst()
.orElse(null);
}
}
Listing 8-10
Part of chapter08/src/main/java/com/bsg6/chapter08/MusicRepository.java
```

If you’re wondering about the lambda still, here’s the “traditional form.”

```
new RowMapper() {
@Override
String mapRow(ResultSet rs, int rowNum) throws SQLException {
return new Artist(
rs.getInt("id"),
rs.getString("name")
);
}
}
```

In Listing [8-10](#471370_2_En_8_Chapter.xhtml#PC13), we’re doing the same thing that we saw in `getMatchingArtistNames()`, in structure. We have a simple SQL query with a placeholder for the `id`, and we pass in an object array with the `id` method parameter. We have a simple lambda (a one-liner, really, split apart to respect page widths for print) just as we did in `getMatchingArtistNames()`, except here we’re mapping the `ResultSet` to an `Artist` object.

We then let Java’s Stream API grab the first result of the `List` – which will have at most one entry. `Stream.findFirst()` returns an `Optional<T>`, so if the `List<Artist>` is empty, we can convert that to a `null` with `Optional<T>.orElse()`. We’ll see **this** pattern in other places of the `MusicRepository`, too, where we search for data, and take action if it isn’t present.

Before we go too much farther into `MusicRepository` – and before we see the actual full implementation instead of snippets – let’s talk about `@Transactional` for a bit.

### `@Transactional`

If you recall Chapter [7](#471370_2_En_7_Chapter.xhtml)’s database interaction service, we only supported working with `Artist` entities, for a few reasons.

One reason was that we knew that the code was going to be thrown away (in this chapter, in the section you’re reading **right now**), and adding support for `Song` wasn’t going to add any value to the service; the `Artist` support was enough to communicate the points we were trying to understand.

The main reason, though, was that our code wasn’t structured in a way that made supporting transactions very easy. With Spring’s data access tools (like `JdbcTemplate`) in our toolbox, we can indicate that methods are expected to participate in a transaction very easily.

What `@Transactional` means is that the method annotated needs to run in a *transaction context* of some sort, which might even include “no transaction at all.” A transaction context is referred to as its *propagation*. The default propagation is `REQUIRED`, which means that if a transaction has not yet been started, one *should* be started; you can also have `REQUIRES_NEW`, which says that a new transaction will be started for the method call even if a transaction has been started (which …​ may or may not work, depending on your datastore and configuration), and `SUPPORTS`, which says that transaction semantics will be respected if a transaction has already been started, but the method will execute non-transactionally if there is no transaction.

| Propagation | Description |
| --- | --- |
| `REQUIRED` | This will participate in a transaction that’s already begun, or will create a transaction if none exists. |
| `SUPPORTS` | This will participate in a transaction that’s already begun, or will execute non-transactionally if none exists. |
| `REQUIRES_NEW` | This will suspend a transaction if one is already underway, and begin a new one; the old transaction will be resumed when the new transaction completes. This may not work in all contexts. |
| `MANDATORY` | This will participate in a transaction if one currently exists, and will throw an exception if a transaction does **not** already exist. |
| `NOT_SUPPORTED` | This will suspend a transaction if one is underway, and will execute outside of the context of a transaction. |
| `NEVER` | This is the inverse of `MANDATORY`; it will throw an exception if a transaction is underway. If there’s no current transaction, it will execute non-transactionally. |
| `NESTED` | This will create a new transaction **within** a currently running transaction (if one is underway). This may not work in all contexts, as the transaction manager being used needs to support nested transactions. |

A `@Transaction` annotation can also specify that a transaction context is read-only (with the `readOnly` attribute, which accepts a `boolean`: therefore, it would look like `@Transaction(readOnly=true)` in use).

Another attribute of a transaction is `isolation`, which affects the visibility of actions taken by the transaction before the transaction is committed. For example, imagine if a transaction that takes five seconds to run deletes a record in the first 50 milliseconds of the transaction; should *other* transactions be able to see the effect of the deletion before the transaction is complete? There are four specific isolation levels allowed, with an additional level of `DEFAULT` being provided, which means to use the underlying default isolation level for the datastore.

| Isolation level | Description |
| --- | --- |
| `READ_UNCOMMITTED` | This allows a row changed by one transaction to be read by another transaction before any changes have been committed. If the transaction fails, the second transaction might have invalid data. |
| `READ_COMMITTED` | This prevents transactions from reading data that has been changed by other transactions, until the other transactions' changes have been committed. |
| `REPEATABLE_READ` | This prevents rows from being read with changes in them (much like `READ_COMMITTED`) but also prevents situations where queries re-fetch data if it’s been changed. Databases are odd, but this still makes sense when you go far enough down the rabbit hole. |
| `SERIALIZABLE` | This prevents a transaction from seeing changed data (as in `REPEATABLE_READ`) but also enforces that the transaction cannot see data from *any* other live transactions until *this* transaction is done; it’s as if the transaction has a snapshot view of the data as it was when the transaction was begun. |
| `NEVER` | This means that if a transaction is in progress when the method is called, an exception will be thrown! (This one is fairly rare in the real world, as far as your authors can tell; they’ve never seen it used.) |

There are other fields for the `@Transactional` annotation; two of them are particularly worth pointing out, even though we don’t use them in this chapter.

The first is `timeout`. If this is provided (with something like `@Transactional(timeout=5)`), the transaction will end with an exception if the transaction lasts longer than the timeout value. (The default is `-1`, which means “no timeout.”) This is valuable if you have a long-running transaction – or if you encounter a deadlock situation.

A deadlock can occur when transactions require resources that aren’t available. For example, imagine if transaction “X” - `tX` - acquires a lock on resource `A`, and at the same moment, transaction “Y” - `tY` = acquires a lock on resource `B`. There’s no deadlock here yet – unless in the **next** moment `tX` tries to acquire a lock on `B`, while `tY` tries to acquire a lock on `A`. Neither `tX` nor `tY` can proceed, because the resources they need are locked by the other transaction.

A transaction timeout will end the transactions after the timeout period ends, giving the program a chance to recover and proceed – and hopefully informing the programmer of the problem.

There are lots of ways to address locks like this, but they’re outside of the scope of this book; you’d really need to consider the exact situation that causes the deadlock and the transactional capabilities of the specific database you’re using.

With that said, a quick and simple rule of thumb is to **always** acquire resources in the same order – so `tX` and `tY` would always try to acquire resources `A` and `B` *in that order* so that `tY` wouldn’t proceed until `tX` was done with both `A` and `B`. This is, as stated, a “quick and dirty” rule of thumb, and that’s said very seriously; your specific problem domain may not allow such simple solutions.

The `@Transactional` annotation also supports a number of properties related to **rollback**. A rollback will cancel all changes created by a transaction; all locks, updates, or deletes are cancelled by a rollback. (It’s a cancellation of the transaction, and restores the database to its status before the transaction was begun.)

With `@Transactional`, you can add `rollbackFor`, followed by an array of classes that extend `Throwable`, or `rollbackForClassName`, followed by an array of class **names** – as strings, for example. If these exceptions are thrown by the annotated method, the transaction is rolled back as part of the method exit. There’s also a `noRollback` and `noRollbackForClassName` that indicate that the transactions are **not** rolled back if the exceptions are thrown by the methods. The default is to rollback on exceptions thrown by a `@Transactional` method – but it’s fully tuneable.

The defaults are actually quite enough for our `MusicRepository`. Speaking of `MusicRepository`, let’s get back to it and actually see what the real, live implementation looks like, all 150+ lines of it.

### The Actual `MusicRepository`

Here’s the **actual** content of `MusicRepository`. There’s one method we want to examine in detail: `internalFindArtistByName()`. There are other methods that follow some of the same principles (`getSong()` follows nearly the same pattern that `findArtistByName()` uses, and `voteForSong()` is an essentially similar operation, for example), but `findArtistByName` introduces some architecturally significant ideas into our implementation.

For completeness’ sake, the content of `getMatchingArtistNames()` and `findArtistById()` are included in this listing as well.

```
package com.bsg6.chapter08;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.support.GeneratedKeyHolder;
import org.springframework.jdbc.support.KeyHolder;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;
import java.sql.PreparedStatement;
import java.sql.Statement;
import java.util.List;
// tag::declaration[]
@Repository
public class MusicRepository {
JdbcTemplate jdbcTemplate;
MusicRepository(JdbcTemplate template) {
jdbcTemplate=template;
}
// end::declaration[]
@Autowired
SongRowMapper songRowMapper;
// tag::findArtistById[]
@Transactional
public Artist findArtistById(Integer id) {
return jdbcTemplate.query(
"SELECT id, name FROM artists WHERE id=?",
new Object[]{id},
(rs, rowNum) ->
new Artist(
rs.getInt("id"),
rs.getString("name"
)
)
)
.stream()
.findFirst()
.orElse(null);
}
// end::findArtistById[]
@Transactional
public Artist findArtistByName(String name) {
return internalFindArtistByName(name, true);
}
@Transactional
public Artist findArtistByNameNoUpdate(String name) {
return internalFindArtistByName(name, false);
}
private Artist internalFindArtistByName(String name, boolean update) {
String insertSQL = "INSERT into artists (name) values (?)";
String selectSQL = "SELECT id, name FROM artists " +
"WHERE lower(name)=lower(?)";
return jdbcTemplate.query(
selectSQL,
new Object[]{name},
(rs, rowNum) -> new Artist(
rs.getInt("id"),
rs.getString("name")
)
)
.stream()
.findFirst()
.orElseGet(() -> {
if (update) {
KeyHolder keyHolder = new GeneratedKeyHolder();
jdbcTemplate.update(conn -> {
PreparedStatement ps = conn.prepareStatement(
insertSQL,
Statement.RETURN_GENERATED_KEYS
);
ps.setString(1, name);
return ps;
}, keyHolder);
return new Artist(keyHolder.getKey().intValue(), name);
} else {
return null;
}
});
}
@Transactional
public List getSongsForArtist(String artistName) {
String selectSQL = "SELECT id, artist_id, name, votes " +
"FROM songs WHERE artist_id=? " +
"order by votes desc, name asc";
Artist artist = internalFindArtistByName(artistName, true);
return jdbcTemplate.query(
selectSQL, new Object[]{artist.getId()},
songRowMapper);
}
@Transactional
public List getMatchingSongNamesForArtist(
String artistName,
String prefix
) {
String selectSQL = "SELECT name FROM songs WHERE artist_id=? " +
"and lower(name) like lower(?) " +
"order by name asc";
Artist artist = internalFindArtistByName(artistName, true);
return jdbcTemplate.query(
selectSQL, new Object[]{artist.getId(), prefix + "%"},
(rs, rowNum) -> rs.getString("name"));
}
// tag::getMatchingArtistNames[]
@Transactional
public List getMatchingArtistNames(String prefix) {
String selectSQL = "SELECT name FROM artists WHERE " +
"lower(name) like lower(?) " +
"order by name asc";
/*
* Note use of Object[] for query arguments, and
* the use of a lambda to map from a row to a String
*/
return jdbcTemplate.query(
selectSQL,
new Object[]{prefix + "%"},
(rs, rowNum) -> rs.getString("name"));
}
// end::getMatchingArtistNames[]
@Transactional
public Song getSong(String artistName, String name) {
return internalGetSong(artistName, name);
}
private Song internalGetSong(String artistName, String name) {
String selectSQL = "SELECT id, artist_id, name, votes FROM songs " +
"WHERE artist_id=? " +
"and lower(name) = lower(?)";
String insertSQL = "INSERT INTO SONGS (artist_id, name, votes) " +
"values(?,?,?)";
Artist artist = internalFindArtistByName(artistName, true);
Song song = jdbcTemplate.query(
selectSQL,
new Object[]{artist.getId(), name},
songRowMapper)
.stream()
.findFirst()
.orElseGet(() -> {
KeyHolder keyHolder = new GeneratedKeyHolder();
jdbcTemplate.update(conn -> {
PreparedStatement ps = conn.prepareStatement(insertSQL,
Statement.RETURN_GENERATED_KEYS);
ps.setInt(1, artist.getId());
ps.setString(2, name);
ps.setInt(3, 0);
return ps;
}, keyHolder);
return new Song(keyHolder.getKey().intValue(),
artist.getId(),
name,
0);
});
return song;
}
@Transactional
public Song voteForSong(String artistName, String name) {
String updateSQL = "UPDATE songs SET votes=? WHERE id=?";
Song song = internalGetSong(artistName, name);
song.setVotes(song.getVotes() + 1);
jdbcTemplate.update(conn -> {
PreparedStatement ps = conn.prepareStatement(updateSQL);
ps.setInt(1, song.getVotes());
ps.setInt(2, song.getId());
return ps;
});
return song;
}
}
Listing 8-11
chapter08/src/main/java/com/bsg6/chapter08/MusicRepository.java
```

Remember to pay no attention to the `// tag::` and `// end::` comments. This book was written with AsciiDoctor ([`https://asciidoctor.org`](https://asciidoctor.org)), and these are typesetting comments to help us extract specific bits of the code as needed. The result is that we’re not copying code from a source tree into the book and hoping the sample code and the book contents stay in sync; we’re actually using the real code to generate the book content. There are a few exceptions, including some in this chapter (the `RowMapper` code to replace the lambda expressions, for example), but for the most part, you’re seeing the real code when you read the book, the code that is used to actually compile and run everything, so when (and if) you type it in or copy it, you’re getting something that actually worked when the book was written.

The first of the things we want to examine now: `intFindArtistByName()`, an internal method with access points through two `public` methods. The first public method (`findArtistByName`) expects a `String` (the name of the artist). The second public method (`findArtistByNameNoUpdate()`) has the same argument – an artist’s name – but calls the internal method such that an update is not attempted.

The private method is *not* annotated with `@Transactional` – it’s a private internal method and expects to be called from a public method, one that **has** been marked as `@Transactional`. This avoids problems with exception propagation; in this class, we have enough control over exception conditions that it’s *probably* not an issue, but we might as well *try* to do it right.

Internally, the first part of `internalFindArtistByName()` mirrors the `findArtistById()` method from Listing [8-10](#471370_2_En_8_Chapter.xhtml#PC13); we have a query that returns a `List<Artist>`, and we convert it to a stream. The `.orElse()` code is a little more involved, though.

This method might be used in two ways, you see: the first way implies that an `Artist` should be created, and the second does not. That’s because when we do a search for a list of songs, we don’t necessarily want to create an `Artist`; a search is a read-only operation, so there’s no need or requirement to try to write anything – in fact, there’s a desire **not** to write anything. The default “mode” of the method is to write an `Artist`, but we want access to a version that does not. Thus, the public version (`findArtistByName()`) assumes an update, and the internal version – the one that does all the work – checks to see what operations are desired.

If it’s a read-only operation, we return `null` – our signal value for “nothing found.” We could have thrown an exception, or returned an `Optional`, but in context, `null` is simpler; plus, we’ve already used it in other places in our code (see `findArtistById()` – again, Listing [8-10](#471370_2_En_8_Chapter.xhtml#PC13) – as an example).

If an update is required, then we have a few things to factor in. We need to return an `Artist` object, of course, and that object should be fully populated. The `name` is easy; we’re searching for the `Artist` by name, so that’s a logical value for the `name` attribute. (Does this even need to be said? …​ Probably not, but we’re aiming for some level of completeness here.)

However, we need to grab the `id` that the database assigns to the `Artist`, too. The way we do that is fairly simple, but there are some moving parts to consider:

1.  First, we create a `KeyHolder` (an interface with a concrete realization of `GeneratedKeyHolder`). This interface has a few access methods to get keys – but the most useful (and relevant in this case) is `getKey()`, which returns a `java.lang.Number`.

2.  We then use a lambda as an instance of a `PreparedStatementCreator`. This is a single-access method class (with the method being `createPreparedStatement()` and accepting a `Connection`); you’re meant to, well, create a `PreparedStatement` with the proper placeholders populated. We only have one placeholder (as an `Artist` has only one attribute besides `id`) so that’s …​ rather simple.

3.  We pass the `KeyHolder` as the final argument to the `JdbcTemplate.update()` method.

Lastly, we pull out the key from the `KeyHolder` – with `keyHolder.getKey().intValue()`, which gets an integer from the `Number` abstraction – and use that to build an `Artist` reference.

Could we have written this to be simpler, or at least to be more straightforward? Well…​ sort of. In this case, “straightforward” means “without streams or `Optional`,” and truthfully…​ it can be done, but involves a lot of temporary variables to contain local state. The “simpler” version is actually going to have a higher cyclomatic complexity^([^(96)](#fn96)) than the streaming version; the way it’s written now has a design complexity of 2 and a cyclomatic complexity of 2, and the old approach using `if` and checking `List.size()` for presence actually has complexity scores of 3 – where lower numbers are better.

You see the same pattern in `getSong()`, with a `private internalGetSong()` method being used to avoid nesting transactional calls made from the same class. `voteForSong()` uses the `internalGetSong()` when it needs to, well, get a `Song` – the transaction semantics are applied at the public method layer only.

In practice, you can get away (sort of) with nested transactional calls if you’re careful, but they’re generally bad practice (due to how transactions are applied by Spring) and can create some odd errors.

#### Testing MusicRepository

Writing `MusicRepository` is all well and good, but as usual, it’s not worth much if it doesn’t actually work. We need a test. Luckily, we already wrote one – all the way back in Chapter [3](#471370_2_En_3_Chapter.xhtml). What’s presented here is a modification of `MusicServiceTests` from Chapter [3](#471370_2_En_3_Chapter.xhtml) (Listing [3-16](#471370_2_En_3_Chapter.xhtml#PC16)), without the superclass structure.

Chapter [3](#471370_2_En_3_Chapter.xhtml) created `MusicServiceTests` as an abstract superclass for a series of tests, so we could issue the same tests against multiple implementations of `MusicService` by altering configuration mechanisms. We don’t have that concern in this chapter, so this test is slightly simpler in organization. The actual code to do the tests is the same, even though every method is slightly different because we’ve added things like `@BeforeMethod` to our toolbox since Chapter [3](#471370_2_En_3_Chapter.xhtml).

```
package com.bsg6.chapter08;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;
import java.util.List;
import java.util.function.Consumer;
import static org.testng.Assert.assertEquals;
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class MusicRepositoryTest extends AbstractTestNGSpringContextTests {
@Autowired
MusicRepository musicRepository;
@Autowired
JdbcTemplate jdbcTemplate;
private Object[][] model = new Object[][]{
{"Threadbare Loaf", "Someone Stole the Flour", 4},
{"Threadbare Loaf", "What Happened To Our First CD?", 17},
{"Therapy Zeppelin", "Medium", 4},
{"Clancy in Silt", "Igneous", 5}
};
void iterateOverModel(Consumer consumer) {
for (Object[] data : model) {
consumer.accept(data);
}
}
void populateData() {
iterateOverModel(data -> {
for (int i = 0; i 
assertEquals(
musicRepository.getSong((String) data[0],
(String) data[1]).getVotes(),
((Integer) data[2]).intValue()
));
}
@Test
void testSongsForArtist() {
List songs = musicRepository.getSongsForArtist("Threadbare Loaf");
assertEquals(songs.size(), 2);
assertEquals(songs.get(0).getName(), "What Happened To Our First CD?");
assertEquals(songs.get(0).getVotes(), 17);
assertEquals(songs.get(1).getName(), "Someone Stole the Flour");
assertEquals(songs.get(1).getVotes(), 4);
}
@Test
void testMatchingArtistNames() {
List names = musicRepository.getMatchingArtistNames("Th");
assertEquals(names.size(), 2);
assertEquals(names.get(0), "Therapy Zeppelin");
assertEquals(names.get(1), "Threadbare Loaf");
}
@Test
void testMatchingSongNamesForArtist() {
List names = musicRepository.getMatchingSongNamesForArtist(
"Threadbare Loaf", "W"
);
assertEquals(names.size(), 1);
assertEquals(names.get(0), "What Happened To Our First CD?");
}
}
Listing 8-12
chapter08/src/test/java/com/bsg6/chapter08/MusicRepositoryTest.java
```

A few things of note:

First, we have a method annotated with `@BeforeMethod`. TestNG (and other testing frameworks) has mechanisms to run specific methods before and after different stages in a given test; in this case, we want to run `clearDatabase()` (which clears the database and then populates it with known data) before executing any and every method marked with `@Test`. We could also run methods **after** each test, with `@AfterMethod`, and we have other annotations available as well, like `@BeforeClass` and `@BeforeTest`, each of which have their own significant behaviors. See [`https://testng.org/doc/documentation-main.html#annotations`](https://testng.org/doc/documentation-main.html#annotations) for more details. JUnit certainly has its own analogs to this behavior with `@BeforeEach` and `@BeforeAll`.

Second, note the use of `@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)` for the class-level annotation. In the context of **this** test, the port makes little sense – but we’re going to use it later in this chapter, so bear with it for now. We’re using this annotation here because of the configuration itself (and it’s copied from the other tests from the rest of the chapter, actually, so it becomes part of standard practice for this section of the book); it’s simple enough that we simply marked this as a web-enabled configuration, and therefore in order to get the full initialization to run properly, we need to mark this as a test that is also web-enabled. We could have avoided this by using multiple configurations, all tied together in a sort of “top-level” configuration class, but that’d serve little purpose other than preventing the need for one annotation and consuming more trees when the book is printed.

It’d be nice to re-use the test from Chapter [3](#471370_2_En_3_Chapter.xhtml), but it’s fundamentally difficult to do as written – because the base class for the Chapter [3](#471370_2_En_3_Chapter.xhtml) tests is in the `src/tests` directory, and therefore, it isn’t exported for use by other projects. We could have created a `testsupport` project that **did** export classes for use in testing, but that would have introduced a lot of complexity for such an early chapter. We will see this technique applied in Chapter [9](#471370_2_En_9_Chapter.xhtml), when we want to reuse tests for two completely separate persistence layers.

## Adding the REST Endpoints

We now have a `MusicService` implementation that uses a database to store data with `JdbcTemplate` – so our interactions with the database are reasonably safe, easy to maintain, and clear to anyone moderately familiar with SQL. It’s time for us to tie all of it together into an actual web application to show all of the pieces working together.^([^(97)](#fn97))

### The `ArtistController`

Let’s start small, by recreating our `ArtistController` from Chapter [7](#471370_2_En_7_Chapter.xhtml). (We can’t re-use the code because the underlying services have changed.) This is effectively the same code as in Chapter [7](#471370_2_En_7_Chapter.xhtml), except it adds a `decode()` method as mentioned briefly in Chapter [6](#471370_2_En_6_Chapter.xhtml) for completeness (we want to be able to handle band names with spaces, question marks, and other odd characters) and autowires the `MusicService` via the constructor. We’ll also need an `ArtistNotFoundException` just as in Chapter [7](#471370_2_En_7_Chapter.xhtml); we’re not including `ProblemDetail` because problem details are very important but they’re not in scope for *this* chapter.

```
package com.bsg6.chapter08;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.util.UriUtils;
import java.nio.charset.Charset;
import java.util.List;
@RestController
public class ArtistController {
private MusicRepository service;
ArtistController(MusicRepository service) {
this.service = service;
}
String decode(Object data) {
return UriUtils.decode(data.toString(), Charset.defaultCharset());
}
@GetMapping(value = "/artists/{id}",
produces = MediaType.APPLICATION_JSON_VALUE)
Artist findArtistById(@PathVariable int id) {
Artist artist = service.findArtistById(id);
if (artist != null) {
return artist;
} else {
throw new ArtistNotFoundException();
}
}
/*
* if no artist name is provided, the exception path is
* always chosen and an IllegalArgumentException is thrown.
*/
@GetMapping(value = {"/artists/search/{name}", "/artists/search/"},
produces = MediaType.APPLICATION_JSON_VALUE)
Artist findArtistByName(
@PathVariable(required = false) String name
) {
if (name != null) {
Artist artist = service.findArtistByNameNoUpdate(decode(name));
if (artist != null) {
return artist;
} else {
throw new ArtistNotFoundException();
}
} else {
throw new NoArtistNameSubmittedException();
}
}
@PostMapping(value="/artists",
produces = MediaType.APPLICATION_JSON_VALUE)
Artist saveArtist(@RequestBody Artist artist) {
return service.findArtistByName(artist.getName());
}
@GetMapping(value={"/artists/match/{name}", "/artists/match/"},
produces = MediaType.APPLICATION_JSON_VALUE)
List findArtistByMatchingName(
@PathVariable(required = false)
String name
) {
return service.getMatchingArtistNames(name != null ? decode(name) : "");
}
}
Listing 8-13
chapter08/src/main/java/com/bsg6/chapter08/ArtistController.java
```

We can see in `findArtistByName()` the use of “traditional” (i.e., non-streamed) straightforward code to work with the result of `findArtistByName()`. We could have written this with streams; this next example includes a method to convert the `null` from `MusicService.findArtistByName()` into an `Optional<Artist>`. Let’s take a look and then work out what it all means:

We’re still doing REST badly. When we create an `Artist` – or a `Song`, as we’ll see – we should be returning a `Location` header in the response that includes an endpoint that will return a newly created object.

Why not?

Well…​ one reason is that the web interface here isn’t really meant to be all that complete – the web layer of Spring is very thorough, and we’re barely skimming the surface of what it can do; we’re mostly showing code that **can** work, as opposed to code that works fantastically well. This chapter’s long enough as it is.

```
/*
* this method serves to migrate the MusicService' findArtistByName()
* to something that accepts and returns an Optional
*/
Optional findArtistByName(Optional name, boolean update) {
return Optional.of(service.findArtistByName(
decode(
name.orElseThrow(
() -> new IllegalArgumentException("No artist name supplied")
)
)
));
}
@GetMapping({"/artist/search/{name}", "/artist/search/"})
Artist findArtistByName(
@PathVariable(required = false) Optional name
) {
Optional artistOptional = findArtistByName(name, false);
return artistOptional.orElseThrow(
ArtistNotFoundException::new
);
}
Listing 8-14
A streamed version of findArtistByName
```

The class-local `findArtistByName()` here exists for two reasons: one is to convert the result of `MusicService.findArtistByName()` into an `Optional<Artist>`. Obviously, if the service returned an `Optional` itself instead of returning `null` for “no artist found,” we wouldn’t need the conversion. The other reason is to make sure we call `decode()` if a valid-looking `name` is passed in, and to throw an exception otherwise.

Do we **need** this functionality? Actually, no. We could always simply not map `"/artists/search/"` in the controller. However, this would mean that calling `/artists/search/` – with no artist name – would return an HTTP “not found” error instead of an “invalid invocation” error (a `404` instead of a `400`, in HTTP error code parlance). You could solve **that** by adding a specific mapping that returned the correct error code.

Which way is best? Well…​ the one that works. There’s not really a “best” here, although arguments can be made for every aspect of the code. You should use the approach you find most sensible.

The cyclomatic complexity of the “simple” version – the one in the actual sample code – is 3 (it has multiple branches and execution paths). The cyclomatic complexity of the streamed code – all of it, both methods – is **one**. In terms of code metrics, the streamed versions are actually **simpler** than the more straightforward code. That doesn’t mean that the streamed versions are better – and we’re not using them, since the API of `MusicService` doesn’t return an `Optional<Artist>` anyway. This example is just to illustrate the (ahem) options available to programmers.

And now the exception class, which has an annotation to tell Spring to convert it to an HTTP error code just as in Chapter [7](#471370_2_En_7_Chapter.xhtml). Note that it’s part of our actual program code, so it’s in `src/main`:

```
package com.bsg6.chapter08;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;
@ResponseStatus(code = HttpStatus.NOT_FOUND, reason = "Artist not found")
public class ArtistNotFoundException extends RuntimeException{
}
Listing 8-15
chapter08/src/main/java/com/bsg6/chapter08/ArtistNotFoundException.java
```

Now, we have a workable interface for working with `Artist` entities over HTTP. We can’t do anything with `Song` entities yet, but let’s get the simple things running first. (We can’t do anything with a `Song` if we can’t do anything with an `Artist`, after all.)

Of course we can’t have an `ArtistController` without something to actually test it, so let’s take a look at `ArtistControllerTest` – again, a near analog to what we saw in Chapter [7](#471370_2_En_7_Chapter.xhtml), although it’s slightly different to account for the slight variances in configuration approaches. We’re not actually touching the `MusicService` directly here – everything is through the controller, using the form of actual HTTP requests; it’s possible (and likely) there are error conditions not being tested, but in general if these tests all pass, our ability to work with `Artist` records is guaranteed.

```
package com.bsg6.chapter08;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.core.ParameterizedTypeReference;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.springframework.web.util.UriUtils;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import java.nio.charset.Charset;
import java.util.List;
import static org.testng.Assert.*;
import static org.testng.Assert.assertEquals;
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class ArtistControllerTest extends AbstractTestNGSpringContextTests {
@Autowired
private TestRestTemplate restTemplate;
@Autowired
JdbcTemplate jdbcTemplate;
String encode(Object data) {
return UriUtils.encode(data.toString(),
Charset.defaultCharset());
}
List getArtists() {
return jdbcTemplate.query(
"SELECT id, name FROM artists",
(rs, rowNum) ->
new Object[]{
rs.getInt("id"),
rs.getString("name")
}
);
}
@DataProvider
Object[][] artistData() {
List artists = getArtists();
artists.add(new Object[]{-1, null});
artists.add(new Object[]{-1, "Not A Band"});
return artists.toArray(new Object[0][]);
}
@Test(dataProvider = "artistData")
public void testGetArtistById(int id, String name) {
String url = "/artists/" + id;
ResponseEntity response =
restTemplate.getForEntity(url, Artist.class);
if (id != -1) {
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist artist = response.getBody();
Artist data = new Artist(id, name);
assertEquals(artist.getId(), data.getId());
// note: the corrected service returns the *proper* name
assertEquals(
artist.getName().toLowerCase(),
data.getName().toLowerCase()
);
} else {
assertEquals(response.getStatusCode(), HttpStatus.NOT_FOUND);
}
}
@Test(dataProvider = "artistData")
public void testSearchForArtist(int id, String name) {
String url = "/artists/search/" +
(name != null ? encode(name) : "");
ResponseEntity response =
restTemplate.getForEntity(url, Artist.class);
if (name != null) {
if (id == -1) {
assertEquals(response.getStatusCode(),
HttpStatus.NOT_FOUND);
} else {
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist artist = response.getBody();
Artist data = new Artist(id, name);
// note: the corrected service returns the *proper* name
assertEquals(
artist.getName().toLowerCase(),
data.getName().toLowerCase()
);
}
} else {
assertEquals(
response.getStatusCode(),
HttpStatus.BAD_REQUEST
);
}
}
@Test
public void testSaveExistingArtist() {
String url = "/artists";
Artist newArtist = new Artist("Threadbare Loaf");
ResponseEntity response = restTemplate.postForEntity(
url,
newArtist,
Artist.class
);
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist artist = response.getBody();
assertNotNull(artist);
int id = artist.getId();
assertEquals(artist.getName(), newArtist.getName());
response = restTemplate.postForEntity(
url,
newArtist,
Artist.class
);
assertEquals(response.getStatusCode(), HttpStatus.OK);
assertEquals(response.getBody(), artist);
}
@DataProvider
public Object[][] artistSearches() {
return new Object[][]{
new Object[]{"", 3},
new Object[]{"T", 2},
new Object[]{"Th", 2},
new Object[]{"Thr", 1},
new Object[]{"C", 1},
new Object[]{"Z", 0}
};
}
@Test(dataProvider = "artistSearches")
public void testSearches(String name, int count) {
ParameterizedTypeReference> type =
new ParameterizedTypeReference() {
};
String url = "/artists/match/" + encode(name);
ResponseEntity> response = restTemplate.exchange(
url,
HttpMethod.GET,
null,
type
);
assertEquals(response.getStatusCode(), HttpStatus.OK);
List artists = response.getBody();
assertNotNull(artists);
assertEquals(artists.size(), count);
}
// We need this to run AFTER testSearches completes, because
// testSearches adds to the artist list and therefore we
// might get one more artist than we're expecting out of
// some searches.
@Test(dependsOnMethods = "testSearches")
public void testSaveArtist() {
String url = "/artists";
Artist newArtist = new Artist(0, "The Broken Keyboards");
ResponseEntity response = restTemplate.postForEntity(
url,
newArtist,
Artist.class
);
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist artist = response.getBody();
assertNotNull(artist);
int id = artist.getId();
assertNotEquals(id, 0);
assertEquals(artist.getName(), newArtist.getName());
response = restTemplate.getForEntity(url + "/search/" + newArtist.getName(), Artist.class);
assertEquals(response.getStatusCode(), HttpStatus.OK);
Artist foundArtist = response.getBody();
assertNotNull(foundArtist);
assertEquals(artist, foundArtist);
}
}
Listing 8-16
chapter08/src/test/java/com/bsg6/chapter08/ArtistControllerTest.java
```

There’s an interesting problem with `testGetArtistsById()`. The way we’re initializing data in Listing [8-7](#471370_2_En_8_Chapter.xhtml#PC7) doesn’t include any `id` fields – it’s letting the database generate the `id` via an internal sequence. That means our tests don’t know the `id` to use to query for a given `Artist`; the `@DataProvider` used by `testGetArtistsById()`, the `artistData()` method, needs to have a way to query the database and build a dataset for testing.

Thus, we have `getArtists()`, which uses the `JdbcTemplate` to return a list of “valid artists” according to the database, and `artistData()` takes that list and appends to *invalid* `Artist` records to it, so we can test for misses as well as hits.

### The `SongController`

Now for new code – let’s add a `SongController`. This is a fairly simple controller, as you can see – nearly everything is delegated to the `MusicService`. The same approaches that we discussed for `ArtistController` apply here as well.

```
package com.bsg6.chapter08;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.util.UriUtils;
import java.nio.charset.Charset;
import java.util.List;
@RestController
public class SongController {
private MusicRepository service;
SongController(MusicRepository service) {
this.service = service;
}
String decode(Object data) {
return UriUtils.decode(data.toString(), Charset.defaultCharset());
}
@GetMapping(value="/artists/{name}/vote/{title}",
produces = MediaType.APPLICATION_JSON_VALUE)
Song voteForSong(@PathVariable String name, @PathVariable String title) {
return service.voteForSong(decode(name), decode(title));
}
@GetMapping(value="/artists/{name}/song/{title}",
produces = MediaType.APPLICATION_JSON_VALUE)
Song getSong(@PathVariable String name, @PathVariable String title) {
return service.getSong(decode(name), decode(title));
}
@GetMapping(value="/artists/{name}/songs",
produces = MediaType.APPLICATION_JSON_VALUE)
List getSongsForArtist(@PathVariable String name) {
return service.getSongsForArtist(decode(name));
}
@GetMapping(value={"/artists/{name}/match/{title}",
"/artists/{name}/match/"},
produces = MediaType.APPLICATION_JSON_VALUE)
List findSongsForArtist(@PathVariable String name,
@PathVariable(required = false)
String title) {
return service.getMatchingSongNamesForArtist(decode(name),
title != null ? decode(title) : "");
}
}
Listing 8-17
chapter08/src/main/java/com/bsg6/chapter08/SongController.java
```

At last, we come to what is arguably the most important test in the project – the `SongControllerTest`. Our other tests are important for incremental development, and test specific things. This test, however, covers nearly everything our project does in one class. (It doesn’t **quite** cover everything, because we didn’t want to repeat some of the simpler tests in `ArtistControllerTest`.)

```
package com.bsg6.chapter08;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.test.web.server.LocalServerPort;
import org.springframework.core.ParameterizedTypeReference;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.springframework.web.util.UriUtils;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;
import java.nio.charset.Charset;
import java.util.List;
import java.util.function.Consumer;
import static org.testng.Assert.assertEquals;
import static org.testng.Assert.assertNotNull;
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class SongControllerTest extends AbstractTestNGSpringContextTests {
@LocalServerPort
private int port;
@Autowired
private TestRestTemplate restTemplate;
@Autowired
JdbcTemplate jdbcTemplate;
private Object[][] model = new Object[][]{
{"Threadbare Loaf", "Someone Stole the Flour", 4},
{"Threadbare Loaf", "What Happened To Our First CD?", 17},
{"Therapy Zeppelin", "Medium", 4},
{"Clancy in Silt", "Igneous", 5}
};
@BeforeMethod
void clearDatabase() {
jdbcTemplate.update("DELETE FROM songs");
jdbcTemplate.update("DELETE FROM artists");
populateData();
}
void iterateOverModel(Consumer consumer) {
for (Object[] data : model) {
consumer.accept(data);
}
}
String encode(Object data) {
return UriUtils.encode(data.toString(), Charset.defaultCharset());
}
void populateData() {
iterateOverModel(data -> {
for (int i = 0; i  response =
restTemplate.getForEntity(url, Song.class);
assertEquals(response.getStatusCode(), HttpStatus.OK);
}
});
}
@Test
void testSongVoting() {
iterateOverModel(data -> {
String url = "http://localhost:"
+ port
+ "/artists/"
+ encode(data[0])
+ "/song/"
+ encode(data[1]);
ResponseEntity response =
restTemplate.getForEntity(url, Song.class);
assertEquals(response.getStatusCode(), HttpStatus.OK);
Song song = response.getBody();
assertNotNull(song);
assertEquals(song.getName(), data[1]);
assertEquals(song.getVotes(), ((Integer) data[2]).intValue());
});
}
@Test
void testSongsForArtist() {
ParameterizedTypeReference> type =
new ParameterizedTypeReference() {
};
String url = "http://localhost:"
+ port
+ "/artists/"
+ encode("Threadbare Loaf")
+ "/songs";
ResponseEntity> response = restTemplate.exchange(
url,
HttpMethod.GET,
null,
type
);
assertEquals(response.getStatusCode(), HttpStatus.OK);
List songs =response.getBody();
assertEquals(songs.size(), 2);
assertEquals(songs.get(0).getName(), "What Happened To Our First CD?");
assertEquals(songs.get(0).getVotes(), 17);
assertEquals(songs.get(1).getName(), "Someone Stole the Flour");
assertEquals(songs.get(1).getVotes(), 4);
}
@Test
void testMatchingSongNamesForArtist() {
ParameterizedTypeReference> type =
new ParameterizedTypeReference() {
};
String url = "http://localhost:"
+ port
+ "/artists/"
+ encode("Threadbare Loaf")
+ "/match/" +
encode("W");
ResponseEntity> response = restTemplate.exchange(
url,
HttpMethod.GET,
null,
type
);
List names = response.getBody();
assertEquals(names.size(), 1);
assertEquals(names.get(0), "What Happened To Our First CD?");
}
}
Listing 8-18
chapter08/src/test/java/com/bsg6/chapter08/SongControllerTest.java
```

From a coding standpoint, it’s not particularly complex, but it is fairly comprehensive.

## Next Steps

This chapter has been a bit of a journey through transaction support, `JdbcTemplate` usage, and testing of integrated services. Thankfully, the core concepts are fairly easy to summarize: using Spring’s data access mechanism gives you trivial access to transaction support, and the Spring interfaces are really easy to use despite presenting a wide array of options. One limitation, though, is that we’re still manually writing SQL for use with a relational database.

In our next chapter, we are going to take a look at another data abstraction from Spring, called “Spring Data.” With Spring Data, we can write interfaces to access nearly **any** database, whether relational or not, and we will see how to make our queries programmatically verifiable.

Footnotes [1](#471370_2_En_8_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_8_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_8_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_8_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_8_Chapter.xhtml#Fn5_source)  

# 9. Persistence with Spring and Spring Data

In Chapter [8](#471370_2_En_8_Chapter.xhtml) we finally stopped looking at configuration and presentation mechanisms, and we looked at accessing a relational database with `JdbcTemplate`. In this chapter, we’re going to look at accessing data again – with a Spring project called “Spring Data,” which can provide a mostly data-agnostic view of data access.

Spring Data unifies data access for Spring, providing common access to not only different databases, but different APIs – like JPA (the Java Persistence API) and our old friend JDBC, but different **types** of databases like MongoDB, Neo4J, and others.

## Introduction

We’ve tended to progress from the simplest use of technology to more advanced, but this chapter will invert things slightly, starting with JPA; JDBC and SQL are base-level technologies (and are used under the hood of JPA), but the problem with JDBC is that it is, well, JDBC – and it relies heavily on a live data structure, something you’d see in a running database with a fully functional, realized schema. That means that there are aspects of rigid data access that may or may not work, depending on the database, because it is the “system of record,” the canonical description of what the data looks like. When we compile our application, we don’t know if the database even exists, or what the schema looks like, which makes automatic tooling for that database rather difficult.

JPA, however, allows us to describe what the application should see as a data model. We can use a JPA data model and change the underlying structure all we like (as long as we also tell JPA what the underlying structure is), but we can also know at compile-time what the program sees as the data model. That means that when we first compile our application, regardless of whether the database exists or not, we can know how to expect to access our data (because it’s described in code) – which means we can have a high degree of assurance that our application will access the data correctly, if perhaps not efficiently.^([^(98)](#fn98))

Why don’t we know if the data access is efficient or not? The answer is simple: we might design our object model in such a way that the database uses unindexed data, or more tables than it needs, for example, such that queries require table scans and so forth. JPA isn’t a magic bullet that gives you an excellent schema no matter how you describe the data; it’ll generally help you create a **functioning** schema, but you still have to understand how relational databases work to really fine-tune the data model. It sure would be nice to have magic performance sauce, though.

We’re going to cover a few different storage mechanisms behind the Spring Data abstraction layer, but this chapter will hearken back to Chapters [3](#471370_2_En_3_Chapter.xhtml) and [8](#471370_2_En_8_Chapter.xhtml) and their usage of tests. Once we have the services working, it’s a trivial matter to add REST endpoints in front of the services (as we saw in Chapters [7](#471370_2_En_7_Chapter.xhtml) and [8](#471370_2_En_8_Chapter.xhtml)), so we’re not going to distract ourselves here by adding a web-enabled front end in this chapter.

## General Architecture

Spring Data extends the concept of a `Repository<T,ID>`, a generalized access layer for a given datastore and entity. We’ve already seen the `@Repository` annotation, but here we’ll see an interface (also called `Repository<T,ID>`, where `T` is the type being stored and `ID` is a primary key type, extended with `CrudRepository<T,ID>` and `PagingAndSortingRepository<T,ID>`) that Spring will implement **for** you (for the most part) via proxies.

Proxies in Java aren’t really in scope for this book, unfortunately. With that said, though, here’s a very cursory overview of one method: you can define an object (called a `Proxy`) that intercepts method calls, and arbitrarily execute code in a handler object. Since the proxy has access to the method signatures of the calls and the instance being called, the handler can execute code before or after the actual method bodies – or it can avoid calling the actual method bodies in the first place.

With classes that implement `Repository<T,ID>`, the interface is used to manufacture method bodies out of (nearly) whole cloth, and the effect is that we define a simple interface and Spring magically implements the interface for us. There’s obviously no *real* magic – it’s just a `Proxy` – but it makes using Spring Data a lot easier than it might have otherwise been.

It’s worth noting that there are multiple mechanisms for proxies in Spring. The default is to use Java’s innate proxy mechanisms, but under certain circumstances, Spring will use a library called CGLIB and build a different kind of proxy with it instead.

You will define the entity with Java, and then create an interface (or set of interfaces) to describe how to manipulate that entity in the data storage system. The `Repository<T,ID>` interface is just a marker, in and of itself; it has no methods, but indicates to Spring that implementations work with databases somehow.

What’s the use of `Repository<T,ID>` if it has no methods?

Well, sometimes you want to limit access to methods through the use of fine-grained interfaces. For example, one might have a `Controller` that has no ability to save entities; if we wanted to, we could define a `Repository<T,ID>` that only defined methods to read data, while the actual instance had methods to save or update entities. The `Controller` might receive a copy of the read-only interface from Spring, and therefore the **compiler** can enforce the requirement that it never save entities.

The `CrudRepository<T,ID>` interface, which extends `Repository<T,ID>`, defines operations for reading, writing, updating, removing, and **even** querying the database – and Spring Data applies some magic such that you can even specify queries **in the interface** without you having to know how to actually query the database itself. In Chapter [7](#471370_2_En_7_Chapter.xhtml) we saw methods like `findAllArtistsByName()`, which selected all artists whose names matched a wildcard, without being case-sensitive; with Spring Data, we define a method **signature** in the repository and get nearly the same functionality. (We’d want to build the wildcard externally, although there **are** ways to do it with the interface itself.)

```
public interface ArtistRepository
extends Repository {
List findLikeNameIgnoreCase(String name);
}
Listing 9-1
Spring Data’s equivalent to findAllArtistsByName from Chapter 7
```

Building a `Repository<T,ID>` involves three source files. For a given repository, you will have

1.  A Spring configuration (which can, of course, serve for multiple repositories)

2.  An entity type (an object that represents data), with a primary key of some sort

3.  An interface that implements `Repository<T,ID>` (or `CrudRepository<T,ID>`, or `PagingAndSortingRepository<T,ID>`, in practice), with the entity type and its primary key type

There are also variations of interface creation, to allow for different levels of method access. We’ll show some of these as we go through the chapter.

### An Important Note About Requirements

This chapter will demonstrate accessing two database systems: an embedded H2 database (just as in Chapters [7](#471370_2_En_7_Chapter.xhtml) and [8](#471370_2_En_8_Chapter.xhtml)) and a MongoDB database. We’re going to use a project that allows us to embed MongoDB, so you don’t have to have MongoDB running (or installed).

## Creating Our Project Structure

Chapter [9](#471370_2_En_9_Chapter.xhtml) is actually made up of four subprojects. They are

*   `chapter09-api`, which contains base classes and interfaces used by the other Chapter [9](#471370_2_En_9_Chapter.xhtml) projects

*   `chapter09-test`, which contains tests that our Spring Data implementations should be able to pass

*   `chapter09-jpa`, which contains an implementation and configuration for JPA

*   `chapter09-mongo`, which contains an implementation and configuration for MongoDB

The `chapter09-test` module has its test classes in `src/main/java`, which means the classes are available to projects that import `chapter09-test` – and we’ll be using them as dependencies for the testing phase.

Now let’s start creating our project structure – as there are four separate projects, there’s a lot here, but it’s rather repetitive.

```
mkdir -p chapter09-api/src/main/java
mkdir -p chapter09-api/src/main/resources
mkdir -p chapter09-api/src/test/java
mkdir -p chapter09-api/src/test/resources
mkdir -p chapter09-test/src/main/java
mkdir -p chapter09-test/src/main/resources
mkdir -p chapter09-test/src/test/java
mkdir -p chapter09-test/src/test/resources
mkdir -p chapter09-jpa/src/main/java
mkdir -p chapter09-jpa/src/main/resources
mkdir -p chapter09-jpa/src/test/java
mkdir -p chapter09-jpa/src/test/resources
mkdir -p chapter09-mongo/src/main/java
mkdir -p chapter09-mongo/src/main/resources
mkdir -p chapter09-mongo/src/test/java
mkdir -p chapter09-mongo/src/test/resources
Listing 9-2
Creating the directory structure with POSIX
```

There’s an alternative to this, of course, and since we’re programmers, it’s actually nicer and easier, using the `bash` shell (or an equivalent) and a touch of scripting. (It’s also the way the project was actually created when writing this chapter. Just don’t tell the editor!)

```
for i in api test jpa mongo; do
for j in main test; do
for k in java resources; do
mkdir -p chapter09-$i/src/$j/$k;
done;
done;
done
Listing 9-3
Creating the directory structure with POSIX, version two
```

Of course, since we’ve gone down the capabilities of bash scripting, we can do even nicer than that. This isn’t a book on shell scripting, but still, it’s fun to learn as we go.

```
mkdir -p chapter09-{api,test,jpa,mongo}/src/{main,test}/{java,resources}
Listing 9-4
Creating the directory structure with POSIX, version three
```

We need to walk through our `chapter09-api` and `chapter09-test` projects before actually getting into code we can actually execute, because these projects serve as the basis for our `chapter09-jpa` and `chapter09-mongo` projects. (They also contain more code than the `jpa` and `mongodb` projects do. The `chapter09-jpa` and `chapter09-mongo` projects’ source code is mostly made up of classes that extend or implement classes from `chapter09-api` or `chapter09-test`, with very little actual *source code*.)

### The Common Code

The `chapter09-api` project contains five interfaces, one abstract class, and one class that will end up being a Spring component. They are

*   `BaseEntity`, a generic interface that defines accessors and mutators for a generic identifier

*   `BaseArtist`, an interface that defines access to a `name` field for an artist, and extends `BaseEntity`

*   `BaseSong`, an interface that defines access to an implementation of `BaseArtist`, a song’s name, and the number of votes for a song, as well as extending `BaseEntity`

*   `BaseArtistRepository`, an interface which defines how we will access entities that extend `BaseArtist`

*   `BaseSongRepository`, an interface which defines how we will access entities that extend `BaseSong`

*   `BaseMusicService`, an abstract class that orchestrates the various repository methods

*   `WildcardConverter`, a class that provides a mechanism for constructing wildcards for services

Here’s a class diagram, in UML, that purports to show the relationship between the various classes. When we implement concrete versions of this structure in `chapter09-jpa` and `chapter09-mongo,` we’ll basically be finalizing versions of the base classes.

![](images/471370_2_En_9_Chapter/471370_2_En_9_Fig1_HTML.jpg)

A flowchart runs as follows. I base song repository and A base music service lead to I base song and I base artist. I base song and I base artist repository lead to I base artist. I base song and I base artist lead to I base entity. C wild card convertor is separate from the main flow.

Figure 9-1

Basic data design

Most of these are really pretty simple. Because they’re generic, though, their class declarations tend to be rather long; the reason is that different databases might treat primary keys differently; therefore, we need to have a way to define primary keys on a case-by-case basis.

We’re using “database” in the general sense of “application that stores data,” not in the “relational database” sense.

For example, for relational databases, primary keys are often best generated as integers of some sort, but with document databases like MongoDB, the primary keys are generated as UUIDs represented as `String` instances. We could coerce the types to be common (therefore, using UUIDs everywhere, or integers) but that feels like we’re pouring optimizations down the drain for no good reason (unless, of course, you consider a simpler interface a “good reason,” which you very well might.)

First, let’s take a look at the build script for `chapter09-api`. It has some variations on some of our prior build scripts.

```

4.0.0

com.apress
bsg6
1.0

chapter09-api
1.0

org.springframework.data
spring-data-bom
${springDataBomVersion}
import
pom

org.springframework.data
spring-data-commons

org.springframework
spring-tx

org.springframework
spring-beans
test

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

Listing 9-5
chapter09-api/pom.xml
```

Everything here is fairly straightforward, although we should note the specific inclusion of `spring-data-bom` in our `dependencyManagement` block, which gives us access to a consistent set of dependencies for Spring Data.

So let’s take a look at our entity interfaces, to start with; they’re simple (except for the generic declarations, which are only going to get worse as we progress through the `chapter09-api` project).

First, we have the `BaseEntity` interface, which accepts an `ID` type.

```
package com.bsg6.chapter09.common;
public interface BaseEntity {
ID getId();
void setId(ID id);
}
Listing 9-6
chapter09-api/src/main/java/com/bsg6/chapter09/common/BaseEntity.java
```

This is simple enough; it’s an interface, so it contains no state itself (no properties), but it does indicate that any class that implements `BaseEntity` has access to an `Id` property of some kind, passed to the compiler as a generic type, `<ID>`.

Let’s take a look at `BaseArtist`, next, as it uses `BaseEntity` and adds access to a property of its own.

```
package com.bsg6.chapter09.common;
public interface BaseArtist
extends BaseEntity {
/**
* Get the artist name
*/
String getName();
void setName(String name);
}
Listing 9-7
chapter09-api/src/main/java/com/bsg6/chapter09/common/BaseArtist.java
```

It has a generic parameter, just as `BaseEntity` does, but only uses it to defer **to** `BaseEntity`.

Note

These are all interfaces, because the actual implementations will need more information than generic interfaces can provide. For JPA, for example, each attribute – like `name` – will have a description of how `name` should be represented in the database, but those representations will change based on the actual data storage in use; MongoDB would use different information than JPA would, for example. Thus, we’re describing only the way an `Artist` should *appear* and not how an artist is actually represented in a concrete, real sense.

`BaseSong` is a little more complicated, because it needs to provide access to something that implements `BaseArtist`. It has **two** generic parameters: one is a type that implements `BaseArtist` and the other is the type for the identifier for `BaseEntity`.

```
package com.bsg6.chapter09.common;
public interface BaseSong
, ID>
extends BaseEntity {
T getArtist();
void setArtist(T artist);
/**
* Get the song name
*/
String getName();
void setName(String name);
int getVotes();
void setVotes(int votes);
}
Listing 9-8
chapter09-api/src/main/java/com/bsg6/chapter09/common/BaseSong.java
```

Now, we start getting into the fun stuff; we’re going to look at `BaseArtistRepository`, which actually uses Spring Data to provide functionality, and after that we’ll look at `BaseSongRepository`, `BaseMusicService,` and `WildcardConverter`. Once we understand `BaseArtistRepository`, the other classes will be fairly simple to walk through.

```
package com.bsg6.chapter09.common;
import java.util.List;
import java.util.Optional;
import org.springframework.data.repository.CrudRepository;
public interface BaseArtistRepository
, ID>
extends CrudRepository {
List findAllByNameIsLikeIgnoreCaseOrderByName(String name);
Optional findByNameIgnoreCase(String name);
}
Listing 9-9
chapter09-api/src/main/java/com/bsg6/chapter09/common/BaseArtistRepository.java
```

On its surface, this interface seems fairly simple. It has two generic parameters; one is something that implements `BaseArtist`, and the other is the primary key type. It also implements an interface, `CrudRepository`, which is our bridge to a lot of functionality.

`CrudRepository` exposes a number of useful and standard methods for, well, CRUD – “create,” “read,” “update,” and “delete” – operations. The `ArtistRepository`’s declaration means that this is an implementation of `CrudRepository` that works with types that extend `BaseArtist`, with a primary key type represented by `ID`. The `CrudRepository` interface itself exposes the following methods.

| Method signature | Description |
| --- | --- |
| `<S extends T> S save(S entity)` | This saves an entity and returns a type assignable to the entity type. (If passed an `Artist`, what you will get back will be equivalent to an `Artist`.) It may also mutate the entity passed as an argument if the save operation assigns visible values (i.e., if saving the entity generates a primary key.) It might not be the same type because some persistence mechanisms (like Hibernate) return a proxy that is assignable to the type instead of a simple type itself. |
| `<S extends T> Iterable<S> saveAll(Iterable<S> entities)` | This will save a collection of entities; you can pass it any collection that the JVM can iterate through. This can emulate a batch operation in some limited circumstances, although transactional operations at a service level might be better. |
| `Optional<T> findById(ID id)` | This will do a primary key lookup for the entity type based on the primary key passed to it. If it is not found, it will return `Optional.empty()`. |
| `boolean existsByid(ID id)` | This will indicate if the database has an entity of the correct type with the passed-in `id`. |
| `Iterable<T> findAll()` | This will return an `Iterable` of the entity type. |
| `Iterable<T> findAllById(Iterable<ID> ids)` | This will return an `Iterable` for every existing entity that has a matching `id` in the collection of primary keys. |
| `long count()` | This will give you the count of all entities of the correct type in the repository. (This is a surprise, we’re sure.) |
| `void deleteById(ID id)` | This will delete an entity from the datastore if one exists with this `id`. |
| `void delete(T entity)` | This will delete an entity from the datastore if the entity matches one that exists. |
| `void deleteAll(Iterable<? extends T> entities)` | This will delete all matching entities from the data store. |
| `void deleteAll()` | This will delete all existing entities of this type from the datastore. |

`PagingAndSortingRepository` adds two methods to `CrudRepository` to aid with pagination (and sorting!), as well as serving as a marker for other methods to be available. The methods are `findAll(Sort)`, where `Sort` represents options for sorting, and `findAll(Pageable pageable)`, where `pageable` represents pagination and sorting options. They’re both fairly simple methods, but they’re not part of this chapter’s scope.

However, `BaseArtistRepository` also defines two other methods:

```
List findAllByNameIsLikeIgnoreCaseOrderByName(String name);
Optional findByNameIgnoreCase(String name);
```

These methods are implemented by Spring Data via a dynamic proxy, and their functionality is derived from their names, in a fairly specific grammar. The grammar can be thought of like this:

*   A query type, such as
    *   `find`, which can return a collection or a single entity; you can get a collection by having a return type of `List`, for example; you can also return a `java.util.Stream` of the entity type if the underlying data persistence mechanism supports it.

    *   `read`, which operates in the same way as `find.`

    *   `query`, which operates in the same way as `find.`

    *   `get`, which operates in the same way as `find`. `get`, `read`, `query`, and `find` are all equivalent in application and meaning; they’re just present to allow programmers to use whatever query semantics they choose.

    *   `count`, which returns a count of entities matching the query.

*   A modifier, such as
    *   `Top`, `First`, or `Bottom` followed by an optional number (`1` is the default, so `Top1` and `Top` are the same).

    *   `Distinct` to apply distinctiveness to the results; this would apply to queries based on attributes other than the `id` or other unique fields.

*   An optional entity type (which is not necessary but might add human readability to the method name.) You might say `findArtistByName` or `findByName` – which you prefer is up to you.

*   `By`, which indicates the beginning of the criteria for the query

*   A list of criteria, comprised of
    *   An attribute of the entity, such as `Name` (for `Artist.name`). Traversals are acceptable, so if you were to have, say, a `Person` entity with a reference to an `Address` which itself contained a `city`, it’s acceptable to say `findByAddressCity` to use the city name as part of the query criteria.

    *   `IgnoreCase` will cause the query to be case-insensitive, with the actual implementation depending on the data store – JPA will call `UPPER()` on the attributes, for example.

    *   Operators, the availability of which depends on the specific datastore implementation. Examples include
        *   Between

        *   LessThan

        *   GreaterThan

        *   Like

    *   Optional ordering, comprised of
        *   OrderBy

        *   An attribute name, like Name for an Artist or Song

        *   An optional direction of either Asc or Desc

    *   A separator of `And` between the first and all subsequent criteria

This is not the **actual** grammar, obviously – for that we’d end up with four or five pages of Backus-Naur diagrams, which are designed to illustrate formal grammars – but hopefully it makes the naming system more clear.

If we have an `ArtistRepository<Artist, Integer>` (and we will!), if we want to find an `Artist` – here denoted by `<T>`, since `T` extends `BaseArtist` – by `name` without worrying about exact case matches, our query name would be expressed as `findByNameIgnoreCase(String name)`. It might return an `Optional<Artist>` or an `Artist` reference; if it’s `Artist`, it will return `null` if the name doesn’t exist in the database (i.e., the criteria failed). If it’s `Optional<Artist>,` then a failure to match the criteria will return `Optional.empty()` – a form useful primarily for work with streams.

Worth noting: while Spring Data’s query generator is amazingly powerful, it tends to yield rather complicated method names, and validation of the query name is a little…​ lacking. This places the burden for getting it right on you, the programmer. However, there’s a library called “Querydsl” ([`http://querydsl.com/`](http://querydsl.com/)) that can fix this somewhat, by providing typesafe queries for Spring Data.

With Querydsl, you’d use an annotation processor to generate a “metamodel” – much like JPA’s criteria modeling, actually – and use that model to generate queries that can be validated by the compiler itself. You’d use the metamodel’s methods and attributes to build programmatic criteria for the query, instead of typing in names. This means you’d also (hopefully) have method names that were much easier to type.

However, Querydsl integration is still somewhat in flux. For the purposes of **this** book, Querydsl is something to watch, but it’s not quite in scope.

Let’s take a quick look at `BaseSongRepository`. This interface is substantively the same as `BaseArtistRepository`, although the declaration is longer because it has to have a reference to something that extends `BaseArtist` (because the `BaseSong` declaration needs it, too.) This is also why we use `A extends BaseArtist<ID>` in the declaration; the `Repository` itself doesn’t use `A`, but needs `A` declared to understand how a `BaseSong<A, ID>` is constructed. (There are other ways we could have constructed this, but this involved less nesting of types.)

```
package com.bsg6.chapter09.common;
import org.springframework.data.repository.CrudRepository;
import java.util.List;
import java.util.Optional;
public interface BaseSongRepository
, S extends BaseSong, ID>
extends CrudRepository {
Optional findByArtistIdAndNameIgnoreCase(
ID artistId, String name
);
List findByArtistIdOrderByVotesDesc(ID artistId);
List findByArtistIdAndNameLikeIgnoreCaseOrderByNameDesc(
ID artistId, String name
);
}
Listing 9-10
chapter09-api/src/main/java/com/bsg6/chapter09/common/BaseSongRepository.java
```

Next, let’s take a quick look at our `WildcardConverter`, which mainly provides a single method, called `convertToWildCard(String)`. It’s very simple, of course. The reason this class exists is because some databases use special characters to match wildcards; SQL uses `%`, for example, while Neo4J uses a regular expression (like `".*"`). Other databases might use different characters or, as we’ll see with MongoDB, nothing at all. With this class, we can get our Spring configuration to build our `WildcardConverter` appropriately for our database, and our `BaseMusicService` doesn’t have to change at all.

```
package com.bsg6.chapter09.common;
public class WildcardConverter {
private final String append;
public WildcardConverter(String append) {
this.append = append;
}
public String convertToWildCard(String data) {
return data + append;
}
}
Listing 9-11
chapter09-api/src/main/java/com/bsg6/chapter09/common/WildcardConverter.java
```

It’s time for us to look at `BaseMusicService`, which uses classes that implement `BaseSongRepository` and `BaseArtistRepository`. This is an abstract class. Classes that extend `BaseMusicService` will need to do four things:

*   Have the correct class signature.

*   Delegate to this class’ constructor.

*   Implement `createArtist(String)`, which creates an instance of something that implements `BaseArtist.`

*   Implement `createSong(Artist, String)`, which creates an instance of something that implements `BaseSong``.`

Apart from that – and we’ll see how this class is used shortly, we promise! – this class implements every functional requirement of our music service from Chapters [3](#471370_2_En_3_Chapter.xhtml) and [8](#471370_2_En_8_Chapter.xhtml), along with providing two extra methods to access a `Song` and an `Artist` directly, by their `id`. (This is a useful feature for REST services, as we’ll see in limited fashion in Chapter [10](#471370_2_En_10_Chapter.xhtml).) It actually looks more daunting than it actually is. We know the code’s verbose – it’s a factor of this class being one of the most important ones in the entire chapter.

Note that for the most part, this is a sort of central entry point that allows us to provide those four specific methods without affecting our entire object design. Ordinarily, when you cater to multiple data storage services, you design your objects and access around what those data repositories do well, and here, we’re trying to avoid that (to prevent repeating very similar object models for pages and pages of listings). This class allows us to specialize in very narrow ways, based on our requirements.

```
package com.bsg6.chapter09.common;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
import java.util.stream.Collectors;
public abstract class BaseMusicService
,
S extends BaseSong,
ID> {
private final BaseArtistRepository artistRepository;
private final BaseSongRepository songRepository;
private final WildcardConverter converter;
protected BaseMusicService(
BaseArtistRepository artistRepository,
BaseSongRepository songRepository,
WildcardConverter converter
) {
this.artistRepository = artistRepository;
this.songRepository = songRepository;
this.converter = converter;
}
protected abstract A createArtist(String name);
protected abstract S createSong(A artist, String name);
@Transactional
public void voteForSong(String artistName, String songTitle) {
S song = getSong(artistName, songTitle);
song.setVotes(song.getVotes() + 1);
songRepository.save(song);
}
@Transactional
public S getSong(String artistName, String songTitle) {
A artist = getArtist(artistName);
return songRepository
.findByArtistIdAndNameIgnoreCase(
artist.getId(),
songTitle
)
.orElseGet(() -> {
S entity = createSong(artist, songTitle);
songRepository.save(entity);
return entity;
});
}
@Transactional
public A getArtist(String artistName) {
return artistRepository
.findByNameIgnoreCase(artistName)
.orElseGet(() -> {
A entity = createArtist(artistName);
artistRepository.save(entity);
return entity;
});
}
@Transactional
public List getSongsForArtist(String artistName) {
A artist = getArtist(artistName);
return songRepository.findByArtistIdOrderByVotesDesc(
artist.getId()
);
}
@Transactional(readOnly = true)
public List getMatchingArtistNames(String artistName) {
return artistRepository
.findAllByNameIsLikeIgnoreCaseOrderByName(
converter.convertToWildCard(artistName))
.stream()
.map(A::getName)
.collect(Collectors.toList());
}
@Transactional
public A getArtistById(ID id) {
return artistRepository.findById(id).orElse(null);
}
@Transactional
public S getSongById(ID id) {
return songRepository.findById(id).orElse(null);
}
@Transactional(readOnly = true)
public List getMatchingSongNamesForArtist(
String artistName,
String songTitle
) {
A artist = getArtist(artistName);
return songRepository
.findByArtistIdAndNameLikeIgnoreCaseOrderByNameDesc(
artist.getId(),
converter.convertToWildCard(songTitle)
)
.stream()
.map(S::getName)
.collect(Collectors.toList());
}
}
Listing 9-12
chapter09-api/src/main/java/com/bsg6/chapter09/common/BaseMusicService.java
```

Now we’ve seen a set of classes that can be used to build an application. It’s time for us to create the second of this chapter’s four projects: `chapter09-test`, which will contain three abstract classes that represent test suites. As with `BaseMusicService`, they’re not extremely short (but they’re also not extremely long!) – and the result will be that the “test code” in our JPA and MongoDB projects will be impressively short (mostly consisting of class declarations, in fact).

### The `chapter09-test` Project

The `chapter09-test` project, like `chapter09-api`, is not a “real” Spring Boot project; it’s an artifact that a Spring Boot project would use. It’s a little unique in that it’s meant to be imported into other modules’ **test** scopes, so it exports everything via `src/main/java`.

The pom.xml for chapter09-test is very similar to the pom.xml for chapter09-api.

```

4.0.0

com.apress
bsg6
1.0

chapter09-test
1.0

org.springframework.data
spring-data-bom
${springDataBomVersion}
import
pom

com.apress
chapter09-api
1.0

org.springframework.boot
spring-boot-starter-test

org.springframework
spring-test

org.testng
testng

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

Listing 9-13
chapter09-test/pom.xml
```

Note that it has explicit dependencies on TestNG, `spring-boot-starter-test`, and `spring-test` – and they’re not set to `test` scope. When we import this project into `chapter09-jpa` and `chapter09-mongo`, we’ll do it at `test` scope, so the TestNG and `spring-boot-starter-test` dependencies will be at test scope for downstream dependencies, but we want them in the main compilation classpath for *this* project. Since we’re importing this project at `test` scope for *other* projects, any transitive dependencies will be imported at `test` scope as well. We’re not polluting our classpaths.

There are three sets of tests in `chapter09-test`: `BaseArtistRepositoryTests`, `BaseSongRepositoryTests`, and `BaseMusicServiceTests`. Every one of them is fairly simple; the goal is to exercise the target of the tests, but with Spring injecting implementations of the services.

Thus, when we **use** these tests, we’ll have a Spring configuration that references the necessary services, and these classes will use whatever is provided. As with `chapter09-api`, this makes for some odd declarations, but it’s nothing too complicated – just verbose.

Let’s take a look at our first test, `BaseArtistRepositoryTests`.

```
package com.bsg6.chapter09.test;
import com.bsg6.chapter09.common.BaseArtist;
import com.bsg6.chapter09.common.BaseArtistRepository;
import com.bsg6.chapter09.common.WildcardConverter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;
import static org.testng.Assert.*;
public abstract class BaseArtistRepositoryTests
, ID>
extends AbstractTestNGSpringContextTests {
@Autowired
BaseArtistRepository artistRepository;
// to allow access to createWildcard...
@Autowired
WildcardConverter converter;
protected abstract A createArtist(String name);
@BeforeMethod
public void clearDatabase() {
artistRepository.deleteAll();
}
@Test
public void testOperations() {
// see if the database is empty
assertEquals(artistRepository.count(), 0);
var firstEntity = createArtist("Threadbare Loaf");
var secondEntity = createArtist("Therapy Zeppelin");
firstEntity = artistRepository.save(firstEntity);
assertNotNull(firstEntity.getId());
var artist = artistRepository.findById(firstEntity.getId());
assertTrue(artist.isPresent());
assertEquals(artist.get(), firstEntity);
var query = artistRepository.findAllByNameIsLikeIgnoreCaseOrderByName(converter.convertToWildCard("th"));
assertEquals(query.size(), 1l);
assertEquals(query.get(0), firstEntity);
artistRepository.save(secondEntity);
query = artistRepository.findAllByNameIsLikeIgnoreCaseOrderByName(converter.convertToWildCard("th"));
assertEquals(query.size(), 2);
}
}
Listing 9-14
chapter09-test/src/main/java/com/bsg6/chapter09/test/BaseArtistRepositoryTests.java
```

Everything here is fairly straightforward for a base class; implementations will need to provide a way to create an artist instance somehow (via `createArtist()`) – but as we’ll see in a few pages, a concrete instance of `BaseArtistRepositoryTests` is mostly boilerplate. The class itself fits in three lines.

Note that in Chapter [3](#471370_2_En_3_Chapter.xhtml) we used a different approach; instead of putting tests in a superclass, we had child classes delegate to a superclass. Chapter [3](#471370_2_En_3_Chapter.xhtml)’s approach is probably “safer” in a lot of ways, especially as your tests grow in complexity and features. We opted for this approach in **this** chapter mostly because we didn’t want to repeat more code than we absolutely needed to. The chapter’s long enough and got enough code in it as it is without adding another set of identical lines in every concrete test class.

`BaseSongRepositoryTests` is longer, but that’s because `BaseSongRepository` does more work; it has to interact with artists, after all.^([^(99)](#fn99)) It uses `@BeforeMethod` to reset the database (whichever database it is!) before testing the `BaseSongRepository` instance.

```
package com.bsg6.chapter09.test;
import static org.testng.Assert.assertEquals;
import java.util.List;
import java.util.Optional;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;
import com.bsg6.chapter09.common.BaseArtist;
import com.bsg6.chapter09.common.BaseArtistRepository;
import com.bsg6.chapter09.common.BaseSong;
import com.bsg6.chapter09.common.BaseSongRepository;
import com.bsg6.chapter09.common.WildcardConverter;
public abstract class BaseSongRepositoryTests,
S extends BaseSong,
ID>
extends AbstractTestNGSpringContextTests {
@Autowired
BaseArtistRepository artistRepository;
@Autowired
BaseSongRepository songRepository;
@Autowired
WildcardConverter converter;
protected abstract A createArtist(String name);
protected abstract S createSong(A artist, String name);
@BeforeMethod
public void clearDatabase() {
songRepository.deleteAll();
artistRepository.deleteAll();
buildModel();
}
private Object[][] model = new Object[][]{
{"Threadbare Loaf", "Someone Stole the Flour", 4},
{"Threadbare Loaf", "What Happened To Our First CD?", 17},
{"Therapy Zeppelin", "Mfbrbl Is Not A Word", 0},
{"Therapy Zeppelin", "Medium", 4},
{"Clancy in Silt", "Igneous", 5}
};
private void buildModel() {
for (Object[] data : model) {
String artistName = (String) data[0];
String songTitle = (String) data[1];
Integer votes = (Integer) data[2];
Optional artistQuery = artistRepository
.findByNameIgnoreCase(artistName);
A artist = artistQuery.orElseGet(() -> {
A entity = createArtist(artistName);
artistRepository.save(entity);
return entity;
});
Optional songQuery = songRepository
.findByArtistIdAndNameIgnoreCase(artist.getId(),
songTitle);
if (songQuery.isEmpty()) {
S song = createSong(artist, songTitle);
song.setVotes(votes);
songRepository.save(song);
}
}
}
@Test
public void testOperations() {
A artist = artistRepository
.findByNameIgnoreCase("therapy zeppelin")
.orElseThrow();
List songs = songRepository
.findByArtistIdAndNameLikeIgnoreCaseOrderByNameDesc(
artist.getId(),
converter.convertToWildCard("m")
);
assertEquals(songs.size(), 2);
songs = songRepository
.findByArtistIdOrderByVotesDesc(artist.getId());
assertEquals(songs.size(), 2);
// we know the votes assigned by default,
// and they should be in descending order.
// "Medium" has four votes...
assertEquals(songs.get(0).getName(), "Medium");
assertEquals(songs.get(0).getVotes(), 4);
// "Mfbrbl" is liked by nobody. I mean, REALLY.
assertEquals(songs.get(1).getVotes(), 0);
}
}
Listing 9-15
chapter09-test/src/main/java/com/bsg6/chapter09/test/BaseSongRepositoryTests.java
```

Lastly, we have the longest of our test classes, `BaseMusicServiceTests`. This one is conceptually the same as `BaseSongRepositoryTests`, except it works with `BaseMusicService` instead; the mechanisms it uses are pretty much exactly the same as what we see in `BaseSongRepositoryTests`.

There’s one thing to note, though: the `abstract ID getNonexistentId()` method. The base class has no idea what a valid `ID` looks like, so classes that use this will need to implement this method, hopefully with an identifier that won’t naturally occur, such that the tests that use this method – `testFindArtistById()` and `testFindSongById()` – will successfully check for an object that doesn’t exist.

```
package com.bsg6.chapter09.test;
import com.bsg6.chapter09.common.*;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;
import java.util.List;
import java.util.function.Consumer;
import static org.testng.Assert.*;
public abstract class BaseMusicServiceTests,
S extends BaseSong,
ID
> extends AbstractTestNGSpringContextTests {
@Autowired
BaseMusicService musicService;
@Autowired
BaseArtistRepository artistRepository;
@Autowired
BaseSongRepository songRepository;
private Object[][] model = new Object[][]{
{"Threadbare Loaf", "Someone Stole the Flour", 4},
{"Threadbare Loaf", "What Happened To Our First CD?", 17},
{"Therapy Zeppelin", "Medium", 4},
{"Clancy in Silt", "Igneous", 5}
};
@BeforeMethod
public void clearDatabase() {
songRepository.deleteAll();
artistRepository.deleteAll();
populateService();
}
protected abstract ID getNonexistentId();
void iterateOverModel(Consumer consumer) {
for (Object[] data : model) {
consumer.accept(data);
}
}
void populateService() {
iterateOverModel(data -> {
for (int i = 0; i 
assertEquals(
musicService.getSong(
data[0].toString(),
data[1].toString()
).getVotes(),
((Integer) data[2]).intValue()
));
}
@Test
void testSongsForArtist() {
List songs =
musicService.getSongsForArtist("Threadbare Loaf");
assertEquals(songs.size(), 2);
assertEquals(songs.get(0).getName(),
"What Happened To Our First CD?");
assertEquals(songs.get(0).getVotes(), 17);
assertEquals(songs.get(1).getName(),
"Someone Stole the Flour");
assertEquals(songs.get(1).getVotes(), 4);
}
@Test
void testMatchingArtistNames() {
List names = musicService.getMatchingArtistNames("Th");
assertEquals(names.size(), 2);
assertEquals(names.get(0), "Therapy Zeppelin");
assertEquals(names.get(1), "Threadbare Loaf");
}
@Test
void testFindArtistById() {
A artist = musicService.getArtist("Threadbare Loaf");
assertNotNull(artist);
A searched = musicService.getArtistById(artist.getId());
assertNotNull(searched);
assertEquals(artist.getName(), searched.getName());
searched = musicService.getArtistById(getNonexistentId());
assertNull(searched);
}
@Test
void testFindSongById() {
S song = musicService.getSong("Therapy Zeppelin",
"Medium");
assertNotNull(song);
S searched = musicService.getSongById(song.getId());
assertNotNull(searched);
assertEquals(song.getName(), searched.getName());
searched = musicService.getSongById(getNonexistentId());
assertNull(searched);
}
@Test
void testMatchingSongNamesForArtist() {
List names =
musicService.getMatchingSongNamesForArtist(
"Threadbare Loaf", "W"
);
assertEquals(names.size(), 1);
assertEquals(names.get(0),
"What Happened To Our First CD?");
}
}
Listing 9-16
chapter09-test/src/main/java/com/bsg6/chapter09/test/BaseMusicServiceTests.java
```

It’s time we actually got to **using** Spring Data instead of preparing to use it.

### The `chapter09-jpa` Project

JPA – the Java Persistence API – is a specification that provides idiomatic object/relational mapping (or “ORM”) for Java. In other words, it’s designed to map data from a database table (or sets of tables), where data is stored in rows and columns, to a Java object model.

We saw some of that in Chapter [8](#471370_2_En_8_Chapter.xhtml), except we were doing the conversion manually (and only when querying the database).

There are a number of JPA implementations. Hibernate ([`https://hibernate.org`](https://hibernate.org)) is *probably* the most influential object-relational mapper for Java, just as Spring is *probably* the most influential dependency injection library for Java. Alternatives to Hibernate include OpenJPA ([`https://openjpa.apache.org/`](https://openjpa.apache.org/)), EclipseLink ([`https://eclipse.dev/eclipselink//`](https://eclipse.dev/eclipselink//)), and DataNucleus ([`www.datanucleus.org`](http://www.datanucleus.org)) – among others – but in this chapter we’re going to use what Spring Data provides for us – which is Hibernate.

Let’s take a quick look at our `pom.xml`. There are a few things to note:

*   It includes a test-time dependency on `chapter09-test`.

*   It has the same limited plugin configuration and dependency management that `chapter09-api` and `chapter09-test` have. This is because we’re going to use `chapter09-jpa` as a dependency in Chapter [10](#471370_2_En_10_Chapter.xhtml).

*   It has a dependency on `spring-boot-starter-data-jpa` and on `h2` as a sample database.

*   It imports `jackson-annotations` because we want to use an annotation in one of the entity classes – which we’ll see (and explain) when we look at the `Artist` implementation.

```

4.0.0

com.apress
bsg6
1.0

chapter09-jpa
1.0

org.springframework.data
spring-data-bom
${springDataBomVersion}
import
pom

com.apress
chapter09-api
1.0

com.apress
chapter09-test
1.0
test

org.springframework.boot
spring-boot-starter-data-jpa

com.h2database
h2
runtime

com.fasterxml.jackson.core
jackson-annotations

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

Listing 9-17
chapter09-jpa/pom.xml
```

The hard work for us is actually already done, in `chapter09-api`. What this project needs to do is verbose (because it’s in Java, and Java idiomatically puts classes in their own source files, which means a lot of repetitive boilerplate), but it’s still surprisingly simple:

*   Create concrete implementations of all the base classes.

*   Provide a Spring configuration.

Let’s get to it. We have to implement `BaseArtist`, `BaseSong`, `BaseArtistRepository`, `BaseSongRepository`, and `BaseMusicService`; thankfully, all of them are very short, but we still have to have them. Then we’ll have to build a configuration, and then we’ll need to extend our test classes, too.

First, the `Artist` class. This is a JPA entity; note that it extends `BaseArtist` and uses `Integer` as the identifier type. It also uses regular JPA annotations to define relationships between artists and songs.

You’ll note the use of `@JsonIgnore` as an annotation on the `songs` reference; this is required in this case by our rather simple object structure, in which an `Artist` has a reference to a set of `Song` objects, and each of those `Song` objects has a reference back to the `Artist`. This has implications when serializing object structures – and since this module is used in Chapter [10](#471370_2_En_10_Chapter.xhtml), we have to account for serialization issues or else we’ll see stack overflows.

```
package com.bsg6.chapter09.jpa;
import com.bsg6.chapter09.common.BaseArtist;
import com.fasterxml.jackson.annotation.JsonIgnore;
import jakarta.persistence.*;
import org.springframework.lang.NonNull;
import java.util.ArrayList;
import java.util.List;
import java.util.Objects;
import java.util.StringJoiner;
@Entity
public class Artist implements BaseArtist {
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
Integer id;
@NonNull
String name;
@OneToMany(
cascade = CascadeType.ALL,
mappedBy = "artist",
fetch = FetchType.EAGER
)
@OrderBy("votes DESC")
@JsonIgnore
List songs = new ArrayList();
public Artist() {
}
public Artist(@NonNull String name) {
this.name = name;
}
@Override
public String getName() {
return name;
}
@Override
public void setName(String name) {
this.name = name;
}
@Override
public Integer getId() {
return id;
}
@Override
public void setId(Integer id) {
this.id = id;
}
@Override
public String toString() {
return new StringJoiner(", ", Artist.class.getSimpleName() + "[", "]")
.add("id=" + id)
.add("name='" + name + "'")
.add("songs=" + songs)
.toString();
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof Artist artist)) return false;
return Objects.equals(
getId(),
artist.getId()
) && Objects.equals(
getName(),
artist.getName()
);
}
@Override
public int hashCode() {
return Objects.hash(getId(), getName());
}
}
Listing 9-18
chapter09-jpa/src/main/java/com/bsg6/chapter09/jpa/Artist.java
```

Note

The reference to `songs` uses `FetchType.EAGER`. This means that when the `Artist` is loaded, the artist’s `Song` references are loaded at the same time, which might mean that retrieving the `Artist` is slower than it could be. The reason for this is because of how entities are managed; if we used `FetchType.LAZY`, we have to manage when we access and populate this field more aggressively, and that’s a subject better suited to a book that is specifically addressing JPA topics.

We can’t use this without a reference to `Song`.

```
package com.bsg6.chapter09.jpa;
import com.bsg6.chapter09.common.BaseSong;
import org.springframework.lang.NonNull;
import jakarta.persistence.*;
import java.util.StringJoiner;
@Entity
@Table(indexes = {
@Index(
name = "artist_song",
columnList = "artist_id,name",
unique = true
)
})
public class Song implements BaseSong {
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
Integer id;
@ManyToOne
@NonNull
Artist artist;
@NonNull
String name;
int votes;
public Song() {
}
public Song(@NonNull Artist artist, @NonNull String name) {
this.artist = artist;
this.name = name;
}
@Override
public Integer getId() {
return id;
}
@Override
public void setId(Integer id) {
this.id = id;
}
@Override
public Artist getArtist() {
return artist;
}
@Override
public void setArtist(Artist artist) {
this.artist = artist;
}
@Override
public String getName() {
return name;
}
@Override
public void setName(String name) {
this.name = name;
}
@Override
public int getVotes() {
return votes;
}
@Override
public void setVotes(int votes) {
this.votes = votes;
}
@Override
public String toString() {
return new StringJoiner(", ", Song.class.getSimpleName() + "[", "]")
.add("id=" + id)
.add("artist=" + artist)
.add("votes=" + votes)
.toString();
}
}
Listing 9-19
chapter09-jpa/src/main/java/com/bsg6/chapter09/jpa/Song.java
```

The `@Table` declaration includes an index, using `artist_id` and `name` – which means that we have a natural, database-enforced restriction on song titles for a given artist: they can only have one song with a given title. (We can’t use Songs’ `name` as a unique index, because more than one artist might release a song with a given song name. They might even be the same song, since artists can cover other musicians’ content: consider Jimi Hendrix’ “All Along the Watchtower,” a cover of a Bob Dylan song.)

Again, this is a fairly standard (and probably naïve) implementation of a JPA entity.

The `ArtistRepository` is actually incredibly simple: it’s just an interface that uses concrete types for `BaseArtistRepository`. That’s it. The only information added here is in the interface definition.

```
package com.bsg6.chapter09.jpa;
import com.bsg6.chapter09.common.BaseArtistRepository;
public interface ArtistRepository
extends BaseArtistRepository {
}
Listing 9-20
chapter09-jpa/src/main/java/com/bsg6/chapter09/jpa/ArtistRepository.java
```

The `SongRepository` is done in the exact same manner: it exists solely to add type information to `BaseSongRepository`.

```
package com.bsg6.chapter09.jpa;
import com.bsg6.chapter09.common.BaseSongRepository;
public interface SongRepository
extends BaseSongRepository {
}
Listing 9-21
chapter09-jpa/src/main/java/com/bsg6/chapter09/jpa/SongRepository.java
```

Lastly, we have `MusicService` – and here we have something a little more complicated, but not very much so. We need to implement two methods to create instances of `Artist` and `Song`, and we also want to delegate to the constructor for `BaseMusicService`.

It’s not marked as a `@Component`, however, because we’re going to specifically construct it in the Spring configuration. (The type resolution, caused by all of the generics being thrown about, makes explicit configuration far easier to use.)

```
package com.bsg6.chapter09.jpa;
import com.bsg6.chapter09.common.BaseMusicService;
import com.bsg6.chapter09.common.WildcardConverter;
public class MusicService extends BaseMusicService {
MusicService(
ArtistRepository artistRepository,
SongRepository songRepository,
WildcardConverter converter
) {
super(artistRepository, songRepository, converter);
}
@Override
protected Artist createArtist(String name) {
return new Artist(name);
}
@Override
protected Song createSong(Artist artist, String name) {
return new Song(artist, name);
}
}
Listing 9-22
chapter09-jpa/src/main/java/com/bsg6/chapter09/jpa/MusicService.java
```

The only thing left, then, is a Spring configuration. We’ll do this with a class called `JpaConfiguration`.

```
package com.bsg6.chapter09.jpa;
import com.bsg6.chapter09.common.WildcardConverter;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.Bean;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
@SpringBootConfiguration
@EnableJpaRepositories
@EntityScan
public class JpaConfiguration {
@Bean
WildcardConverter converter() {
return new WildcardConverter("%");
}
@Bean
MusicService musicService(
ArtistRepository artistRepository,
SongRepository songRepository,
WildcardConverter converter
) {
return new MusicService(artistRepository, songRepository, converter);
}
}
Listing 9-23
chapter09-jpa/src/main/java/com/bsg6/chapter09/jpa/JpaConfiguration.java
```

Here, we leverage Spring Boot to do most of the work, with `@SpringBootConfiguration`. This will scan the current package (and any packages whose names start with `com.bsg6.chapter09.jpa`) for Spring components – which will catch our `MusicService` and `ArtistRepository` and `SongRepository` interfaces; with `@EnableJpaRepositories` we’re informing Spring of what kind of services to implement (and where to look for classes that have the `@Repository` annotation).

We also use the `@EntityScan` annotation to force scanning for entities (classes marked with `@Entity`) in the current package (and any “subpackages” – i.e., packages whose names start with this package’s name.) We won’t need this to run our tests – the test annotations will do this for us – but in “real code” we’ll want it.

We also create our JPA-compatible `WildcardConverter` as a component.

Note that we’re not doing anything to create any resources for JPA; no datasources, no `EntityManager` references, nothing. Spring Boot is doing that for us, through the `spring-boot-starter-data-jpa` dependency. By default we’ll have an in-memory version of whatever database is in our classpath (H2, in this project); if we wanted to (and we do), we can use `application.properties` and set a JDBC url with `spring.datasource.url=jdbc:h2:file:chapter09-jpa`, or whatever value makes sense. Here is an example.

```
spring.datasource.url=jdbc:h2:./chapter9jpa;DB_CLOSE_DELAY=-1;
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
Listing 9-24
chapter09-jpa/src/main/resources/application.properties
```

Note

We are using `update` as the value for `spring.jpa.hibernate.ddl-auto`. For development and testing, this is fine. For production, this is a nightmare. Use it with caution.

Our tests for `chapter09-jpa` look a lot like the rest of the code. We have three tests, each extending one of the classes from the `chapter09-test` project, adding type information specific to `chapter09-jpa` and, occasionally, implementing a method or two to help the tests instantiate classes of the right type.

Here’s the first test, `ArtistRepositoryTests`.

```
package com.bsg6.chapter09.jpa;
import com.bsg6.chapter09.test.BaseArtistRepositoryTests;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
@DataJpaTest
public class ArtistRepositoryTests
extends BaseArtistRepositoryTests {
protected Artist createArtist(String name) {
return new Artist(name);
}
}
Listing 9-25
chapter09-jpa/src/test/java/com/bsg6/chapter09/jpa/ArtistRepositoryTests.java
```

Note the use of `@DataJpaTest`.

`SongRepositoryTests` is effectively the same.

```
package com.bsg6.chapter09.jpa;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import com.bsg6.chapter09.test.BaseSongRepositoryTests;
@DataJpaTest
public class SongRepositoryTests
extends BaseSongRepositoryTests {
@Override
protected Artist createArtist(String name) {
return new Artist(name);
}
@Override
protected Song createSong(Artist artist, String name) {
return new Song(artist, name);
}
}
Listing 9-26
chapter09-jpa/src/test/java/com/bsg6/chapter09/jpa/SongRepositoryTests.java
```

And lastly, let’s see `MusicServiceTests`, which is similar to the prior two tests in that it mostly serves to provide concrete types to the generic superclass, along with implementing `getNonexistentId()`, with an absurdly high identifier.

```
package com.bsg6.chapter09.jpa;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import com.bsg6.chapter09.test.BaseMusicServiceTests;
@DataJpaTest
public class MusicServiceTests
extends BaseMusicServiceTests {
@Override
protected Integer getNonexistentId() {
return 1928491;
}
}
Listing 9-27
chapter09-jpa/src/test/java/com/bsg6/chapter09/jpa/MusicServiceTests.java
```

As you can see, the `chapter09-jpa` project really doesn’t add much. It provides concrete entities (`Artist` and `Song`), and the configuration provides a `WildcardConverter`; `MusicService`, too, provides an easy way to build our entity instances, but that’s about it. The rest of the project’s code is providing concrete types for various interfaces.

Could we have built our base classes differently, in particular to make it so we didn’t have to provide methods like `createArtist()` and `createSong()`? The answer, of course, is “yes,” and in multiple ways. We could have derived the concrete class references in `BaseMusicService`, for example (the references aren’t available for `BaseMusicService` but would have been for child classes, and we could have exploited that).

We could also have passed in concrete references (i.e., `Artist.class`) to `BaseMusicService`’s constructor and used those references to build new objects, as well.

In the end, which approach you choose really depends on which approach you *prefer*. Here, one of the simplest approaches possible was chosen – abstract methods that, given data, pass back valid instances. It’s simple and hard to screw up.

It violates DRY – “don’t repeat yourself” – especially when you consider that the signature of each of the copied methods looks exactly the same. In this case, the choice was made deliberately to write the methods over again in the interest of space and simplicity; if we abstracted everything out, we’d have another whole set of classes to go through.

If we wanted to, we could write a configuration that ran a Spring Boot application as we saw in Chapter [8](#471370_2_En_8_Chapter.xhtml); all that would be left would be the creation of a functional user interface, and we’d have a working music application, using a relational database for data storage.

Now let’s see how much work we have to do to use a **different** data provider – MongoDB.

### The `chapter09-mongo` Project

MongoDB ([`www.mongodb.com`](http://www.mongodb.com)) is an open source database that represents data as collections of documents in binary JSON. As such, it uses a dynamic, flexible schema. It’s loosely grouped among “NoSQL databases,” those data management systems that don’t SQL to manipulate data. It’s also a good example of what makes NoSQL both good and bad: incredible speed and scalability, occasionally obscure querying and modeling, and a typically provider-specific approach to transactions.^([^(100)](#fn100))

Thankfully, Spring Data makes using MongoDB fairly trivial, and it matches the pattern we saw in the JPA example, with the use of the Spring Data MongoDB module.

### Getting MongoDB

We’re going to use a library that allows us to **embed** MongoDB for our tests, so you don’t have to have MongoDB installed. Of course, you might actually *want* to have MongoDB installed locally, rather than relying on an embedded installation; if you build an application using MongoDB, an external dependency isn’t unexpected, after all. Here’s how you can install MongoDB should you so choose; note that the tests will still use an embedded version, but it’s easy to run MongoDB if that’s what you want to do.

On OSX, it’s as simple as running `brew install mongo`.

On Ubuntu, MongoDB can be installed with `apt-get install mongodb`.

On Windows, you can install MongoDB by going to its download page ([`www.mongodb.com/try/download/community`](http://www.mongodb.com/try/download/community)) and grabbing the proper MSI file.

You’d start MongoDB trivially by finding an empty directory and running it.

```
mkdir /var/tmp/musicdata
mongod --noauth -dbpath /var/tmp/musicdata
Listing 9-28
Starting MongoDB on Linux
```

Consult the instructions on [`www.mongodb.com`](http://www.mongodb.com) for your specific operating system if this doesn’t work properly for you. Note also that this starts MongoDB without authentication; this is **solely** useful for running tests and other such operations of little long-term value.

### The Code for the `chapter09-mongo` Project

Our `pom.xml` looks very similar to our `chapter09-jpa pom.xml`. Instead of `spring-boot-starter-data-jpa`, it uses `spring-boot-starter-data-``mongodb`, and it also includes one more test dependency, `de.flapdoodle.embed.mongo`. This dependency is going to allow our tests to actually download and start an embedded MongoDB instance so we don’t have to make sure MongoDB is running before executing any of our tests.

```

4.0.0

com.apress
bsg6
1.0

chapter09-mongo
1.0

org.springframework.data
spring-data-bom
${springDataBomVersion}
import
pom

com.apress
chapter09-api
1.0

com.apress
chapter09-test
1.0
test

org.springframework.boot
spring-boot-starter-data-mongodb

de.flapdoodle.embed
de.flapdoodle.embed.mongo.spring30x
4.7.0
test

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

Listing 9-29
chapter09-mongo/pom.xml
```

The entities for the `chapter09-mongo` project are actually the core substantive difference between this and the `chapter09-jpa` project. The other classes will look pretty much exactly the same, with the primary differences being in package declarations (since the `chapter09-mongo` project uses `com.bsg6.chapter09.mongodb` instead of `com.bsg6.chapter09.jpa`.)

However, even in our entities the differences are minor; in `Artist`, for example, instead of using `@Entity`, we use `@Document`, for example, and the primary key is a `String` instead of an `Integer`.

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.common.BaseArtist;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.lang.NonNull;
import java.util.Objects;
import java.util.StringJoiner;
@Document
public class Artist implements BaseArtist {
@Id
String id;
@NonNull
String name;
public Artist() {
}
public Artist(@NonNull String name) {
this.name = name;
}
@Override
public String getId() {
return id;
}
@Override
public void setId(String id) {
this.id = id;
}
@Override
@NonNull
public String getName() {
return name;
}
@Override
public void setName(@NonNull String name) {
this.name = name;
}
@Override
public String toString() {
return new StringJoiner(", ", Artist.class.getSimpleName() + "[", "]")
.add("id='" + id + "'")
.add("name='" + name + "'")
.toString();
}
@Override
public boolean equals(Object o) {
if (this == o) return true;
if (!(o instanceof Artist artist)) return false;
return Objects.equals(
getId(),
artist.getId()
) && Objects.equals(
getName(),
artist.getName()
);
}
@Override
public int hashCode() {
return Objects.hash(getId(), getName());
}
}
Listing 9-30
chapter09-mongo/src/main/java/com/bsg6/chapter09/mongodb/Artist.java
```

The `Song` is also nearly identical. There are differences, of course; we see the use of `@CompoundIndexes` to declare a unique index between the artist and the name of the song. (In this, it’s a close analog to how a relational database implements compound indexes. The `":1"` in the index structure refers to sort order, which isn’t relevant for our application but still needs to be specified.) We also refer to an `Artist` as a `@DBRef`, which is a way of telling Spring to store a reference to a valid `Artist` document in the field. The result is that we still use the object in the exact same way as we would have with JPA – after all, we’re using the exact same interfaces – but we’re helping Spring Data determine what the object model should look like.

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.common.BaseSong;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.index.CompoundIndex;
import org.springframework.data.mongodb.core.index.CompoundIndexes;
import org.springframework.data.mongodb.core.mapping.DBRef;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.lang.NonNull;
@Document
@CompoundIndexes(
@CompoundIndex(unique = true, def = "{'artist':1, 'name':1}")
)
public class Song implements BaseSong {
@Id
String id;
@NonNull
@DBRef
Artist artist;
@NonNull
String name;
int votes;
public Song() {
}
public Song(@NonNull Artist artist, @NonNull String name) {
this.artist = artist;
this.name = name;
}
@Override
public String getId() {
return id;
}
@Override
public void setId(String id) {
this.id = id;
}
@Override
@NonNull
public Artist getArtist() {
return artist;
}
@Override
public void setArtist(@NonNull Artist artist) {
this.artist = artist;
}
@Override
@NonNull
public String getName() {
return name;
}
@Override
public void setName(@NonNull String name) {
this.name = name;
}
@Override
public int getVotes() {
return votes;
}
@Override
public void setVotes(int votes) {
this.votes = votes;
}
}
Listing 9-31
chapter09-mongo/src/main/java/com/bsg6/chapter09/mongodb/Song.java
```

Now, we get to the truly repetitive bits of the chapter: we’re going to see `ArtistRepository`, `SongRepository`, and `MusicService` for the `chapter09-mongo` project, and except for the package, all three will look nearly exactly like the versions from `chapter09-jpa`.

First, the `ArtistRepository`.

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.common.BaseArtistRepository;
public interface ArtistRepository
extends BaseArtistRepository {
}
Listing 9-32
chapter09-mongo/src/main/java/com/bsg6/chapter09/mongodb/ArtistRepository.java
```

Next, the `SongRepository`.

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.common.BaseSongRepository;
public interface SongRepository
extends BaseSongRepository {
}
Listing 9-33
chapter09-mongo/src/main/java/com/bsg6/chapter09/mongodb/SongRepository.java
```

Next, the `MusicService`.

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.common.BaseMusicService;
import com.bsg6.chapter09.common.WildcardConverter;
import org.springframework.stereotype.Component;
@Component
public class MusicService extends BaseMusicService {
MusicService(
ArtistRepository artistRepository,
SongRepository songRepository,
WildcardConverter converter
) {
super(artistRepository, songRepository, converter);
}
@Override
protected Artist createArtist(String name) {
return new Artist(name);
}
@Override
protected Song createSong(Artist artist, String name) {
return new Song(artist, name);
}
}
Listing 9-34
chapter09-mongo/src/main/java/com/bsg6/chapter09/mongodb/MusicService.java
```

Before we get to the tests – which will, by the way, look very familiar, just like our other classes so far – we should look at the `MongoConfiguration`. It, too, will be very familiar, with a few differences: we use `@EnableMongoRepositories` instead of `@EnableJpaRepositories`.

Just as we saw in the `chapter09-jpa` project, we initialize a `WildcardConverter` – but here, the wildcard is applied by MongoDB, and not with a special character.

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.common.WildcardConverter;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.Bean;
import org.springframework.data.mongodb.repository.config.EnableMongoRepositories;
@SpringBootConfiguration
@EnableMongoRepositories
@EntityScan
public class MongoConfiguration {
@Bean
WildcardConverter converter() {
return new WildcardConverter("");
}
@Bean
MusicService musicService(
ArtistRepository artistRepository,
SongRepository songRepository,
WildcardConverter converter
) {
return new MusicService(artistRepository, songRepository, converter);
}
}
Listing 9-35
chapter09-mongo/src/main/java/com/bsg6/chapter09/mongodb/MongoConfiguration.java
```

We could have declared this as a `@Bean` in a configuration, rather than having it picked up as a `@Component`, of course. We’d have done so in a test-only configuration class (remember, this is a class located in `src/test/java` so it will only be in the classpath for the tests). This way, though, we have a single configuration that we could re-use for a running application without modification or duplication.

After that, it’s all downhill and mercifully short, to boot. First, the `ArtistRepositoryTests` class.

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.test.BaseArtistRepositoryTests;
import org.springframework.boot.test.autoconfigure.data.mongo.DataMongoTest;
@DataMongoTest
public class ArtistRepositoryTests
extends BaseArtistRepositoryTests {
protected Artist createArtist(String name) {
return new Artist(name);
}
}
Listing 9-36
chapter09-mongo/src/test/java/com/bsg6/chapter09/mongodb/ArtistRepositoryTests.java
```

Here, we see the use of `@DataMongoTest` instead of `@DataJpaTest`.

Next, the `SongRepositoryTests` class.

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.test.BaseSongRepositoryTests;
import org.springframework.boot.test.autoconfigure.data.mongo.DataMongoTest;
@DataMongoTest
public class SongRepositoryTests
extends BaseSongRepositoryTests {
@Override
protected Artist createArtist(String name) {
return new Artist(name);
}
@Override
protected Song createSong(Artist artist, String name) {
return new Song(artist, name);
}
}
Listing 9-37
chapter09-mongo/src/test/java/com/bsg6/chapter09/mongodb/SongRepositoryTests.java
```

And now the `MusicServiceTests` class, which is as simple as we’ve come to expect, with the `getNonexistentId()` method returning a random `UUID` (which should fulfill the requirements of nonexistence, barring extraordinary luck, because random UUIDs clash very rarely).

```
package com.bsg6.chapter09.mongodb;
import com.bsg6.chapter09.test.BaseMusicServiceTests;
import org.springframework.boot.test.autoconfigure.data.mongo.DataMongoTest;
import java.util.UUID;
@DataMongoTest
public class MusicServiceTests
extends BaseMusicServiceTests {
@Override
protected String getNonexistentId() {
return UUID.randomUUID().toString();
}
}
Listing 9-38
chapter09-mongo/src/test/java/com/bsg6/chapter09/mongodb/MusicServiceTests.java
```

## Tying Up Loose Ends

So far, we’ve seen how Spring Data makes accessing data storage much easier than it might otherwise be, allowing us to access both relational database and a MongoDB database with two sets of classes that are far more similar than different – even though the underlying data models are **incredibly** different. You can see the same sort of benefit from accessing nearly any database that has support in Spring Data.

It’s worth noting that the interfaces we’ve seen in this chapter could actually slide fairly easily into the web-enabled front end shown in Chapter [8](#471370_2_En_8_Chapter.xhtml). We’ve maintained transactionality, and actually gained simplicity and flexibility; with JPA, we get support for most (if not all) relational databases, and changing the code for MongoDB is trivial. We could migrate to other databases like Cassandra or Neo4J with just as little effort.

## Next Steps

In Chapter [10](#471370_2_En_10_Chapter.xhtml), we shift away from data management and into Spring Security, which provides authentication and authorization support for Spring applications – with a particular focus on managing access via the Web.

Footnotes [1](#471370_2_En_9_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_9_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_9_Chapter.xhtml#Fn3_source)  

# 10. Spring Security

Security is critical in any application with access to live information – even public information.^([^(101)](#fn101)) Security means controlling access to features and information; unless Annie is specifically granted access to Frank’s information, Frank’s data should be **safe** and, from Annie’s perspective, invisible. Naturally, Spring has a powerful and capable security project – called, of all things, Spring Security – that allows you to control nearly every aspect of application security.

In this chapter, we’re going to develop two modules – `chapter10` and `chapter10-custom` – and we’re going to use one of the simple web applications we built in Chapter [9](#471370_2_En_9_Chapter.xhtml) – the `chapter09-jpa` project – and layer on a simple interface to authenticate and control authorization to our resources.

## Introduction

The Spring Security subproject was originally a separate project started in 2003 named Acegi Security System.^([^(102)](#fn102)) After developing separately from Spring itself for 3 years, it was officially adopted in 2007 by Spring and renamed Spring Security. Today, the subproject is the standard for authentication and access control for Spring. It provides enough boilerplate to allow you to use it without any customization, and is extensible enough to allow any specific implementation details your particular authentication and authorization requirements might need.

Like many other Spring projects (including Spring Data, as shown in Chapter [8](#471370_2_En_8_Chapter.xhtml)), the Spring Security subproject is further subdivided into many other subprojects based on features. At its most basic level, the three Spring Security libraries you’ll generally want to include by default are `spring-security-core`, `spring-security-config`, and `spring-security-web`.

Below is a non-exhaustive list of the most prominent modules and the requirement they can help you achieve:

| Library | Description |
| --- | --- |
| `spring-security-core` | The core library is required for all applications using Spring Security. It includes access control and authentication classes and interfaces and can be used for standalone or remote needs. |
| `spring-security-config` | This is a core library as well – providing, of all things, configuration via Java or XML. |
| `spring-security-web` | This library includes the necessary filters and web security infrastructure for using Spring Security in your web applications. |
| `spring-security-test` | Provides support for testing a Spring Security application. It maps easiest with JUnit, but we’ll be able to do the same trickery with TestNG. |
| `spring-security-acl` | Provides access control to specific operations on specific Java object instances in your application. With this, you can say that a specific user has the ability to execute specific methods on specific object instances, for example, although you can certainly rely on access control that isn’t quite **that** specific. (Usually, people rely on role access and not specific user access…​ but you have the ability to choose what features you require.) |
| `spring-security-ldap` | This library provides access to LDAP, the “Lightweight Directory Access Protocol,” for authentication and provisioning. |

There are plenty of other libraries to mention like OAuth (which has its own artifact group, `org.springframework.security.oauth`, with many artifacts to provide specific features) and JOSE.^([^(103)](#fn103)) Support also exists for CAS (a single-signon system; CAS stands for “Central Authentication Service”) and OpenID (a broad Internet authentication specification) and, well, `insert your security apparatus here,` whether in an official capacity or through one of the many open source integrations out there. There are libraries that focus on just the authentication aspect and those that integrate the different ideas surrounding authorization. As of this writing, there are 48 official packages available in Maven for the `org.springframework.security` namespace.

**Authentication and Authorization**

When we speak about authentication, we’re trying to correctly assert that the thing hitting our resources is what, or who, it claims to be. When we speak about this in regard to web-based authentication, we’re generally talking about a host of different methods, with the simplest being HTTP BASIC access, all the way to more involved mechanisms like OpenID or CAS.

Authorization is a set of one or many rules that determine who, once authenticated, is allowed to do what. For example, if Jennifer authenticates as an ADMIN, then she may have Create/Read/Update/Delete access to all entities in the system, whereas John may have USER access and only be able to view and update items associated with their account.

As you’ve seen in previous chapters, Spring Boot can massively reduce the amount of work needed to get started and get something complex running with less effort. We’ll take a look in the next section at how to start configuring a web application and a REST API using Spring Security.

## Quickstart

In this section we’ll get a very simple webapp secured with Spring Security with a little help from the starters available with Spring Boot. In later sections we will dive a bit deeper, but for now we’ll keep it simple to get running quickly.

First, we need to create our directory structure, starting in the overall project directory.

```
mkdir -p chapter10/src/main/java/com/bsg6/chapter10
mkdir -p chapter10/src/main/resources/templates
mkdir -p chapter10/src/test/java/com/bsg6/chapter10
Listing 10-1
Creating the directory structure with POSIX
```

We will need to set up our `pom.xml` – as in previous chapters.^([^(104)](#fn104)) We’ll be using Spring Boot so our setup time will be massively reduced. We’ll write the `pom.xml` we will need for the quickstart and notice the only explicitly called out dependency not part of Spring Boot is `spring-security-test` because it is not part of the starter packages.

```

4.0.0

com.apress
bsg6
1.0

chapter10
1.0
war

org.springframework.boot
spring-boot-starter-web

org.yaml
snakeyaml

org.springframework.boot
spring-boot-starter-test
test

org.springframework.boot
spring-boot-starter-security

org.springframework.boot
spring-boot-starter-mustache

org.springframework.security
spring-security-test
test

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

repackage

Listing 10-2
chapter10/pom.xml
```

The first webapp we will build will simply present a login challenge gating users before allowing them to see our super-secret dashboard pages. With Spring Boot at the helm, we’ll make use of the following dependencies:

*   `spring-boot-starter-web` for the Spring MVC starter

*   `spring-boot-starter-security` for the easiest way to get started with Spring Security

*   `spring-boot-starter-mustache` for rendering mustache templates

*   `spring-boot-starter-test` and `spring-security-test` to pull in useful code for testing out what we’re doing

Spring Security is flexible enough that even without the multiple official subprojects, the ways in which one can configure it are varied. For simplicity’s sake, we’re going to focus on Java-only configuration; XML configuration is certainly doable, but it’s rather verbose.

Let’s see what steps we’ll need to secure our first web application with Spring Boot and Spring Security. As we saw in Chapter [7](#471370_2_En_7_Chapter.xhtml) with our introduction to Spring Boot, getting started can be fairly simple. Our first webapp will be no different.

```
package com.bsg6.chapter10;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class GatewayApplication {
public static void main(String[] args) {
SpringApplication.run(GatewayApplication.class, args);
}
}
Listing 10-3
chapter10/src/main/java/com/bsg6/chapter10/GatewayApplication.java
```

The sensible defaults are there to get you started fast, and with the addition of the `spring-boot-starter-security` and lack of explicit instruction, any requests to the webapp will be assumed to need to be behind a security challenge.

You may be asking yourself, since we’re doing so much defaulting here, and we haven’t specified credentials, how do we log in? The default username if not specified with alternate configuration will be `user,` and the default password in UUID format will be shown when you run the war like so:

```
Using generated security password: 33ccafac-5d13-4af5-ab48-ced789749669
```

This generated password is for development use only. Your security configuration must be updated before running your application in production.

Without further ado, let’s run the app and see the above in action (even though we have no actual endpoint to hit yet).

```
mvn spring-boot:run
```

Open up http://localhost:8080 in any browser and you will see the following.

![](images/471370_2_En_10_Chapter/471370_2_En_10_Fig1_HTML.png)

A webpage interface requiring user authentication with two fields for inputting credentials and a button for submission.

Figure 10-1

Sign in page

After entering the default username: `user` and the generated password you can find in logs, you’ll get a 404 error message because we have no controllers yet defined.

The blatantly obvious warning message in the logs should of course be heeded. This is just a quickstart after all, but if we wanted to specify our own static credentials, we could add the following to an `application.properties` in `src/main/resources`:

```
spring.security.user.name=user
spring.security.user.password=password123
```

Let’s finish off our quickstart by adding a dashboard controller and a rendered template to our codebase.

```
package com.bsg6.chapter10;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
@Controller
public class DashboardController {
@GetMapping("/")
public String dashboard() {
return "dashboard";
}
}
Listing 10-4
chapter10/src/main/java/com/bsg6/chapter10/DashboardController.java
```

We’ll continue with our usage of mustache as template engine and override the default suffix of `.mustache` in favor of `.html` and define this suffix in our `application.properties` so it can be mapped accurately by Spring.

```
spring.mustache.suffix:.html
Listing 10-5
chapter10/src/main/resources/application.properties
```

And our super fancy template with a `logout` action.

```

Admin Dashboard Page

Hello Admin! Welcome to your dashboard!
Logout

Listing 10-6
chapter10/src/main/resources/templates/dashboard.html
```

Opening up a browser and logging in will no longer show you an error message, and instead you’ll get our rendered template.

![](images/471370_2_En_10_Chapter/471370_2_En_10_Fig2_HTML.png)

An interactive admin dashboard interface on a web browser presents a welcome message and a logout option, indicating a platform for administrative tasks and user management.

Figure 10-2

Admin dashboard

### Testing

The most code we’re going to write for this quickstart is in the tests. As with previous Spring Boot tests, we’ll have some annotations for the class and necessary items to work properly with TestNG.

The tests you’ll notice we’re using `MockMvc` instead of `TestRestTemplate`. The main reason aside from us not testing any REST API in these tests, is that the tests mock the request and response from `spring-test` and don’t require a running servlet container. It gives us what we need to ensure the functionality is working as expected. Let’s see how we can set up a mock `WebApplicationContext` and to also ensure we `.apply(springSecurity())` to the `MockMvcBuilders`.

```
@SpringBootTest(classes = GatewayApplication.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
public class GatewayApplicationTest extends AbstractTestNGSpringContextTests {
@Autowired
private WebApplicationContext webApplicationContext;
private MockMvc mockMvc;
@BeforeClass
public void setup() {
mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext)
.apply(springSecurity())
.build();
}
Listing 10-7
Part of chapter10/src/test/java/com/bsg6/chapter10/GatewayApplicationTest.java
```

Our first real test is to ensure that when we hit the root of our webapp we get an `HTTP 401` returned to us. Our second test is going to mock logging in to our tool, so after a request receives an `HTTP 401,` it redirects to login where we’re using a `RequestPostProcessor` which will handle anything needed like CSRF and the resulting cookie so we can check our expectations of an `HTTP 200` after logging in.

```
@Test
public void testDashboardRequiresLogin() throws Exception {
this.mockMvc.perform(get("/"))
.andExpect(status().isUnauthorized());
}
@Test
public void testDashboardAfterLogin() throws Exception {
this.mockMvc.perform(get("/")
.with(SecurityMockMvcRequestPostProcessors
.user("user")
.password("password")))
.andExpect(status().isOk());
}
Listing 10-8
Part of chapter10/src/test/java/com/bsg6/chapter10/GatewayApplicationTest.java
```

In order to run the tests in this module, we’ll use maven and specify the module and that we want to run `test`.

```
mvn -pl chapter10 -am test
```

## Customizing Your Security

Our quickstart is certainly fun for getting up and running fast, and will definitely not be sufficient for any real-world application. The convenient thing about using Spring Boot with Spring Security is that rather than disabling the auto-configuration it affords, we can just surpass it by adding the appropriate configuration classes to our application.

Spring Security works over the Web by using the servlet filter mechanism to intercept HTTP calls, matching the calls’ contents to the security mappings. It does the same thing for method calls, except with a dynamic proxy instead of a servlet filter. Therefore, we need to make sure the security filter is registered with the servlet container, or the classloaders, for Security to work its magic.

Spring Boot will take care of ensuring that the Servlet Filter `springSecurityFilterChain` is set up and registered with our application.

Spring Security’s `springSecurityFilterChain` is a filter chain responsible for intercepting incoming HTTP requests and applying various configured security mechanisms including authentication and authorization. It is the core mechanism for controlling access to specific resources and endpoints and is essential part of Spring Security.

Our custom security application will give us an application with defaults which we’ll customize at each step. Let’s create our custom directory structure for `chapter10-custom`.

```
mkdir -p chapter10-custom/src/main/java/com/bsg6/chapter10
mkdir -p chapter10-custom/src/main/resources/templates
mkdir -p chapter10-custom/src/test/java/com/bsg6/chapter10
mkdir -p chapter10-custom/src/test/java/resources/templates
Listing 10-9
Creating the chapter10-custom directory structure with POSIX
```

Let’s start with the `pom.xml` and `application.properties` which we can copy from `chapter10` wholesale without any changes (and just change the `artifactId` to say `chapter10-custom` in the `pom.xml`) for good measure.

```
cp ../chapter10/pom.xml .
cp ../chapter10/src/main/resources/application.properties src/main/resources
Listing 10-10
Copying pom.xml and application.properties from chapter10
```

They say there’s nothing new under the sun, and our starter applications are no exception here. We have just copied the main entry point and changed the class name for good measure.

```
package com.bsg6.chapter10;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class GatewayCustomApplication {
public static void main(String[] args) {
SpringApplication.run(GatewayCustomApplication.class, args);
}
}
Listing 10-11
chapter10-custom/src/main/java/com/bsg6/chapter10/GatewayCustomApplication.java
```

With what we’ve set up with our new module, `chapter10-custom` is mostly what we’ve achieved in the previous section. Nothing too exciting yet. What we’ll aim to do, however, is create our own custom login page available to all, and then behind the login will be a home page, dashboards for a regular user, admin user, and a contact page available to both roles.

We’ll start with our `@Configuration` class for this webapp. The only thing required during the declaration of this class is for us to specify the annotation `@Configuration` to let Spring know that it should look at this class. If we weren’t using Spring Boot, we’d also have to add the annotation `@EnableWebSecurity,` but thankfully Spring Boot saves you the typing.

```
@Configuration
public class GatewaySecurityConfig {
Listing 10-12
Part of chapter10-custom/src/main/java/com/bsg6/chapter10/GatewaySecurityConfig.java
```

One of the methods we need to define is a `PasswordEncoder` which can be used in our `userDetailsService` method. We’re going to use a factory that returns a `DelegatingPasswordEncoder` which under the hood is just a `Map` of named encoders with their implementations. If you’re after something a bit more defined, you can return any of the encoders found in the factory, or a custom encoder.

```
@Bean
public PasswordEncoder passwordEncoder() {
return PasswordEncoderFactories.createDelegatingPasswordEncoder();
}
Listing 10-13
Definition of PasswordEncoder in chapter10-custom/src/main/java/com/bsg6/chapter10/GatewaySecurityConfig.java
```

**DelegatingPasswordEncoder**

Migrating to a new encoding method for passwords because requirements changed or because you inherited a system that still used MD5 can be painful. From Spring Security 5 on brought the `DelegatingPasswordEncoder` which supports multiple password encoders using a prefix. If we use the factory method `PasswordEncoderFactories.createDelegatingPasswordEncoder(),` you will see if not specified it uses `{bcrypt}` as the default and provides a simple way to upgrade existing passwords not using your preferred method.

For our example application, we will set up two user accounts, each having access to one or more roles. We’ll create an account that has access to the `ADMIN` and `USER` roles, and a second account with just access to the `USER` role. Since we’re still working on keeping this simple, we’ll use the `InMemoryUserDetailsManager`^([^(105)](#fn105)) which will allow us to essentially hardcode the users and return this in-memory manager which would be replaced by an actual security provider. You’ll notice that we make use of the previously defined method `passwordEncoder` so we can have a common method of encoding passwords available to the `UserDetailsService` that we return.

**GrantedAuthority and Roles**

It may be confusing at first to see two different distinctions for a user, one being `GrantedAuthority` and the other being `Role`. A `GrantedAuthority` is a privilege which is finer grained than a role, something like `READ_AUTHORITY` or `WRITE_AUTHORITY` and is purely arbitrary and up to you how to name or handle. Spring Security offers a method for this to check the authority `hasAuthority`. On the other end we have `Role` which can be a more coarse-grained definition of what a User is within your system. A `Role` can be used with the `hasRole` method, and is usually represented like `ADMIN` or `USER` or `SUPERUSER` – again it is arbitrary and up to you.

```
@Bean
public UserDetailsService userDetailsService() {
InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
PasswordEncoder encoder = passwordEncoder();
UserDetails adminUser = User
.withUsername("admin")
.password(encoder.encode("admin123"))
.roles("ADMIN", "USER")
.build();
UserDetails regularUser = User
.withUsername("user")
.password(encoder.encode("user123"))
.roles("USER")
.build();
manager.createUser(adminUser);
manager.createUser(regularUser);
return manager;
}
Listing 10-14
Definition of userDetailsService in chapter10-custom/src/main/java/com/bsg6/chapter10/GatewaySecurityConfig.java
```

Our web application will have the following routes:

*   `/login` – A form showing a username and password challenge with a submit button

*   `/home` – Shown after a successful login and available to any authenticated user

*   `/dashboard` – Available to be shown to any user with the `USER` role

*   `/admin_dashboard` – Available to users with the `ADMIN` role

We’re using the Lambda DSL style in the following code which makes everything a lot easier to grok. Thinking through the above endpoints, our implementation below will ensure that the roles and permissions match up with intentions, and that any request not specified will automatically require authentication. The HttpSecurity object uses a builder pattern so we can ensure at each step that we’re getting that object back, and use the `build` method to return an instance of `SecurityFilterChain` so that Spring Security will act on our defined wishes.

```
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
http
.authorizeHttpRequests(authorize -> authorize
.requestMatchers("/login")
.permitAll()
.requestMatchers("/dashboard")
.hasRole("USER")
.requestMatchers("/admin_dashboard")
.hasRole("ADMIN")
.anyRequest()
.authenticated()
)
.formLogin(formLogin -> formLogin
.loginPage("/login")
.permitAll()
);
return http.build();
}
Listing 10-15
Definition of filterChain in chapter10-custom/src/main/java/com/bsg6/chapter10/GatewaySecurityConfig.java
```

The Spring Boot generated login page handles a lot of things for us; however, I really like my early 2000s no CSS style, so we’re going to build our own login form. One of the items the login page from Spring Boot handles custom is CSRF. We set up a Model attribute below using `@ControllerAdvice` which will let us inject this into our Mustache templates.

**Cross-Site Request Forgery (CSRF)**

Due to the inherently open nature of the Web, it is more vulnerable to attacks from bad actors. One of the numerous methods is cross-site request forgery CSRF or XSRF. The attacker is attempting to cause an innocent victim to submit a maliciously crafted web request that the victim has privileged access to. One way of combating this is to ensure that inherently dangerous actions we have a server-generated token which must be passed which we’ll use `@ControllerAdvice` to ensure it is a Model attribute available to all of our pages.

```
@ControllerAdvice
public class CsrfTokenControllerAdvice {
@ModelAttribute("csrf")
public CsrfToken csrfToken(CsrfToken token) {
return token;
}
}
Listing 10-16
Definition of filterChain in chapter10-custom/src/main/java/com/bsg6/chapter10/CsrfTokenControllerAdvice.java
```

The following template is our fancy login page. It submits to the endpoint `/login` and passes `username` and `password,` and thanks to our `CsrfTokenControllerAdvice,` we also can pass in the generated CSRF token and pass as a hidden field. An implementation-specific detail which we’ll see in our `NonSecureController` class file next is the conditional block for a boolean named `hasError`. If the items passed to the login handler don’t complete successfully, we will show the very generic message seen in our conditional block.

```

Login Page

{{ #hasError }}

Invalid username and password.

{{ /hasError }}

Username

Password

Login

Listing 10-17
Login template in chapter10-custom/src/main/resources/templates/login.html
```

Our controller will be fairly simple for the login route. The only unique bit will be our use of a non-required `@RequestParam` called `error` which will indicate if the login was for any reason unsuccessful. Mustache being a logic-less templating library, we pass back a boolean flag attribute to the template called `hasError` which is true if the param exists in the URL query param.

```
package com.bsg6.chapter10;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
@Controller
public class NonSecureController {
@GetMapping("/login")
public String login(Model model, @RequestParam(required = false) String error) {
model.addAttribute("hasError", error != null);
return "login";
}
}
Listing 10-18
Controller for unsecured routes in chapter10-custom/src/main/java/com/bsg6/chapter10/NonSecureController.java
```

There’s no system-level reason why the secure routes and unsecured routes need to be in separate class files other than it feels nice to separate them especially if there was more logic involved here. Our new routes should be familiar after perusing the `GatewaySecureConfig` class.

```
package com.bsg6.chapter10;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
@Controller
public class SecureController {
@GetMapping(value={"/", "/home"})
public String home() {
return "home";
}
@GetMapping("/dashboard")
public String dashboard() {
return "dashboard";
}
@GetMapping("/admin_dashboard")
public String admin_dashboard() {
return "admin_dashboard";
}
}
Listing 10-19
Controller for secured routes chapter10-custom/src/main/java/com/bsg6/chapter10/SecureController.java
```

Next, let’s check out the very simple templates for our custom secure application. The home page with links to the two other dashboards, a dashboard for regular users and one for admins (both of which include the ability to “logout”).

```

Admin Dashboard Page

Hello Admin! Welcome to your dashboard!

Logout

Listing 10-22
Admin dashboard template in chapter10-custom/src/main/resources/templates/admin_dashboard.html
```

```

Regular User Dashboard Page

Hello User! Welcome to your dashboard!

Logout

Listing 10-21
User dashboard template in chapter10-custom/src/main/resources/templates/dashboard.html
```

```

Home Page

Home Page
Dashboard | Admin Dashboard

Listing 10-20
Home template in chapter10-custom/src/main/resources/templates/home.html
```

Our tests will be instrumental to ensure that our code works as expected. Our first test `testHomepageRequiresLogin` will use `MockMvc` to hit the root of the webapp and ensure that we get an `HTTP 302` as response. Our other expectation is a pattern match using an Ant matcher to `**/login`. Our next two tests will use the annotation `@WithUserDetails` to mock that a user has logged in and that we’re able to view the page after. You’ll notice that the test for the user dashboard doesn’t specify a username, and this is because the default property is already `user` which we’re using for a regular user.

```
@Test
public void testHomepageRequiresLogin() throws Exception {
this.mockMvc.perform(get("/"))
.andExpect(status().is3xxRedirection())
.andExpect(redirectedUrlPattern("**/login"));
}
@Test
@WithUserDetails()
public void testDashboardAfterLoginAsUser() throws Exception {
this.mockMvc.perform(
get("/dashboard")
).andExpect(status().isOk());
}
@Test
@WithUserDetails("admin")
public void testDashboardAfterLoginAsAdmin() throws Exception {
this.mockMvc.perform(
get("/admin_dashboard")
).andExpect(status().isOk());
}
Listing 10-23
Controller for secured routes chapter10-custom/src/test/java/com/bsg6/chapter10/GatewayCustomApplicationTest.java
```

Let’s open up a terminal and navigate to the `chapter10-custom` directory and run our tests.

```
mvn test
```

If all goes well, and it should, everything will be green.

Let’s switch gears a bit here and talk about how we would go about securing a REST application using the repository code we looked at in Chapter [9](#471370_2_En_9_Chapter.xhtml).

## Securing a REST Application

In the last section we were pretty sure we were done creating new subprojects, but security always has other plans. We’ll create our last top-level project and name it `chapter10-jpa`. This section will pull in the subproject `chapter09-jpa` and see how to integrate Spring Security with our work from Chapter [9](#471370_2_En_9_Chapter.xhtml). Here’s how we’ll create the directory structure.

```
mkdir -p chapter10-jpa/src/main/java/com/bsg6/chapter10
mkdir -p chapter10-jpa/src/main/resources/templates
mkdir -p chapter10-jpa/src/test/java/com/bsg6/chapter10
Listing 10-24
Creating the chapter10-jpa directory structure with POSIX
```

One other great thing about pulling in the code from Chapter [9](#471370_2_En_9_Chapter.xhtml) is it uses Spring Boot which we haven’t dealt with in this chapter yet. As we’ve seen in other chapters that leveraged Spring Boot, Boot makes a lot of this stuff very easy.

First things first: let’s build our new Maven configuration file – `pom.xml` – and as always note that the top-level `pom.xml` is used for some inherited properties. We’ll pull in the necessary dependencies for Spring Boot including a new one `spring-boot-starter-security` which will do a lot of the leg work for us and the `chapter09-common` and `chapter09-jpa` subprojects which give us access to `MusicService` and the dependencies.

```

4.0.0

com.apress
bsg6
1.0

chapter10-jpa
1.0
war

org.springframework.boot
spring-boot-starter-web

org.yaml
snakeyaml

org.springframework.boot
spring-boot-starter-test
test

${project.parent.groupId}
chapter09-jpa
${project.parent.version}

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

repackage

Listing 10-25
chapter10-jpa/pom.xml
```

To use Spring Boot we will need to define a `@SpringBootApplication` class which we’ll do below in our `GatewayRestApplication`. We are making use of `@Bean` classes in the Chapter [9](#471370_2_En_9_Chapter.xhtml) codebase so our `scanBasePackages` will include `com.bsg6.chapter09.jpa` along with our package in Chapter [10](#471370_2_En_10_Chapter.xhtml) `com.bsg6.chapter10`.

```
package com.bsg6.chapter10;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication(scanBasePackages = {
"com.bsg6.chapter09.jpa",
"com.bsg6.chapter10"
})
public class GatewayRestApplication {
public static void main(String[] args) {
SpringApplication.run(GatewayRestApplication.class, args);
}
}
Listing 10-26
chapter10-jpa/src/main/java/com/bsg6/chapter10/MainApplication.java
```

With this one class, we now have a Spring Boot application and can run it using the following Gradle command: `mvn spring:bootRun`. Once it starts up, you’ll have a working Tomcat instance ready for action. We haven’t actually defined any endpoints yet though, so you’ll be met with a lot of `404` (not found) errors. Let’s fix that and create a `@RestController` for accessing and creating `Song` entries in our data source. We will define two endpoints just to keep things simple, one will retrieve (GET) a resource by identifier and the other will create (POST) a new resource.

```
package com.bsg6.chapter10;
import com.bsg6.chapter09.jpa.MusicService;
import com.bsg6.chapter09.jpa.Song;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class SongController {
private MusicService service;
SongController(MusicService service) {
this.service = service;
}
@GetMapping(value = "/songs/{id}",
produces = MediaType.APPLICATION_JSON_VALUE)
Song getSongById(@PathVariable int id) {
Song song = service.getSongById(id);
if (song != null) {
return song;
} else {
throw new SongNotFoundException();
}
}
@PostMapping(value="/songs",
consumes = MediaType.APPLICATION_JSON_VALUE,
produces = MediaType.APPLICATION_JSON_VALUE)
Song saveSong(@RequestBody Song song) {
Song songLookup  = service.getSong(song.getArtist().getName(), song.getName());
if(songLookup != null) {
return songLookup;
} else {
throw new SongNotFoundException();
}
}
}
Listing 10-27
chapter10-jpa/src/main/java/com/bsg6/chapter10/SongController.java
```

We are defining two methods here; the first is `getSongById` which takes a `@PathVariable` of `id` which we’ll use the `MusicService` to look up in the database. If it doesn’t exist, we’re going to throw a `SongNotFoundException` which is the same code we saw in Chapter [9](#471370_2_En_9_Chapter.xhtml) for `ArtistNotFoundException`.

The second method is for creating a new `Song`. It’s going to consume a JSON object which we’ll create a sample you can use to submit below and will produce / return the newly created object in the response. You can use the following `curl` command to see this in action for the save.

```
curl --header "Content-Type: application/json" \
-u user:user123 \
--request POST \
--data '{"name":"Someone Stole The Flour","artist":{"name": "Threadbare Loaf"}}' \
http://localhost:8080/songs
Listing 10-28
Curl request to save song
```

After creating this new song entry, we can either use a simple curl again to request our entry.

```
curl --header "Content-Type: application/json" http://localhost:8080/songs/2
Listing 10-29
Curl to get song
```

Now that we have a very simple REST controller in place, let’s see what it would look like with Spring Security integrated. The only thing we really have to do to secure the app and start requiring authentication is to add the following `pom.xml` dependency to the project.

```
org.springframework.boot
spring-boot-starter-security

org.springframework.security
spring-security-test
test

Listing 10-30
Adding spring-boot-starter-security to chapter10-jpa/pom.xml
```

This will set up some magic defaults adding security to all URLs starting from `/*`, using BASIC authentication as the default, and having a default username with `user`.

You may be asking yourself: “That’s great but what about the password?” When you run the app with `mvn spring:bootRun,` you will see something similar in the output.

```
Using generated security password: df1a58e9-7ac2-4d01-9e6e-41e36c06ddb9
Listing 10-31
Generated password from Spring Boot
```

After this issue a curl command to hit the GET endpoint without authentication details.

```
curl --header "Content-Type: application/json" http://localhost:8080/songs/2
Listing 10-32
Curl to get song without providing authentication
```

You will see something like this as a response from the handler.

```
{"timestamp":"2019-06-27T16:22:16.070+0000","status":401,"error":"Unauthorized","message":"Unauthorized","path":"/songs/2"}
Listing 10-33
Curl response without authentication
```

So instead, let’s authenticate and get our `Threadbare Loaf` entry with our credentials. If you remember from above, that means the username of `user` and whatever was listed in the `generated security password` section. Let’s just use the one I’ve already listed above for simplicity.

```
curl \
-u user:51f210f0-3f46-49be-97eb-13e7fef163a8 \
--header "Content-Type: application/json" \
http://localhost:8080/songs/2
{"id":2,"artist":{"id":1,"name":"Threadbare Loaf"},"name":"Someone Stole The Flour","votes":0}
Listing 10-34
Curl with authentication
```

So simple and easy to do, and you get it nearly for free here. This is obviously not going to work for a production application, and the single user is only for test purposes. Let’s take a look at a new config that we can create which allows us to create our own users and roles and override the Spring Boot defaults.

```
package com.bsg6.chapter10;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configurers.AbstractHttpConfigurer;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;
import static org.springframework.security.config.Customizer.withDefaults;
@Configuration
@EnableWebSecurity
public class GatewaySecurityConfig {
@Bean
public UserDetailsService userDetailsService() {
InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
PasswordEncoder encoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
UserDetails adminUser = User
.withUsername("admin")
.password(encoder.encode("admin123"))
.roles("ADMIN", "USER")
.build();
UserDetails regularUser = User
.withUsername("user")
.password(encoder.encode("user123"))
.roles("USER")
.build();
manager.createUser(adminUser);
manager.createUser(regularUser);
return manager;
}
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
http.authorizeHttpRequests(authorize -> authorize.anyRequest().authenticated())
.csrf(AbstractHttpConfigurer::disable)
.httpBasic(withDefaults());
return http.build();
}
}
Listing 10-35
chapter10-jpa/src/main/java/com/bsg6/chapter10/GatewaySecurityConfig.java
```

A good portion of the above should be very familiar to you already.

Our `userDetailsService()` method does a similar thing to the previous `userDetailsService` override which we used in previous examples. We’re setting up two users using the `DelegatingPasswordEncoder` using the credentials of `user:user123` for the `USER` role and `admin:admin123` for a role with both `USER` and `ADMIN` privileges.

Our second method is the `filterChain` method we’ve seen previously but are using a few new methods on `HttpSecurity`. Due to the usage of REST here, we don’t need to use `CSRF` – which is used to prevent cross-site request forgery with JavaScript. Other than those things, we’re securing all endpoints with role `USER` and can now use the simpler credentials already defined. When you’ve got this new class added, terminate the existing `bootRun` and re-run it with `mvn spring:bootRun`. We can copy the existing curl command above in Listing [10-33](#471370_2_En_10_Chapter.xhtml#PC38) and replace the credentials with `user:user123` and should see a similar result.

Let’s not miss the test. For these tests we’ll use the `TestRestTemplate` class as our way of testing out these restful calls, it’s in the name after all. Both tests will create an Artist called “Threadbare Loaf” and a song named “Someone Stole the Flour.” The first you will see is not passing any auth to our method, and our webapp correctly returns an `HTTP 401` in response. The second test will create the same objects, but instead will pass the basic auth challenge with `admin:admin123` and we’ll see an `HTTP 200` passed back and that the response body is not null.

```
package com.bsg6.chapter10;
import com.bsg6.chapter09.jpa.Artist;
import com.bsg6.chapter09.jpa.Song;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
import static org.testng.Assert.assertEquals;
import static org.testng.AssertJUnit.assertNotNull;
@SpringBootTest(classes = GatewayRestApplication.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class GatewayRestApplicationTest extends AbstractTestNGSpringContextTests {
@Autowired
private TestRestTemplate restTemplate;
@Test
public void testSaveSongsRequiresLogin() throws Exception {
Artist artist = new Artist("Threadbare Loaf");
Song song = new Song(artist, "Someone Stole the Flour");
ResponseEntity response = restTemplate.postForEntity("/songs", song, Song.class);
assertEquals(HttpStatus.UNAUTHORIZED, response.getStatusCode());
}
@Test
public void testSaveSongsWithAuth() throws Exception {
Artist artist = new Artist("Threadbare Loaf");
Song song = new Song(artist, "Someone Stole the Flour");
ResponseEntity response = restTemplate.withBasicAuth("admin", "admin123").postForEntity("/songs", song, Song.class);
assertEquals(HttpStatus.OK, response.getStatusCode());
assertNotNull(response.getBody());
}
}
Listing 10-36
chapter10-jpa/src/test/java/com/bsg6/chapter10/GatewayRestApplicationTest.java
```

To run this test with maven, navigate to the `chapter10-jpa` directory and run.

```
mvn test
Listing 10-37
Run the Spring Security JPA test
```

Now you have seen how simple it is to set up security with Spring Boot and REST endpoints and a simple test to ensure it works as expected.

### Integrating OAuth

The last official piece we’re going to work on is how to integrate OAuth 2.0^([^(106)](#fn106)) into your security flow if you choose. As we’ve already covered quite a bit in this chapter, the example we’ll build here is going to be fairly simple. We’ll create a webapp which will assume all endpoints must be protected by our OAuth provider (here we’re going to use GitHub). We’re going to create another subproject to keep things nicely separated.

```
mkdir -p chapter10-oauth/src/main/java/com/bsg6/chapter10
mkdir -p chapter10-oauth/src/main/resources/templates
Listing 10-38
Creating the chapter10-oauth directory structure with POSIX
```

Now that we have our directory structure in place, we’ll create a new `pom.xml` and make sure you add this to the parent pom as well.

```

4.0.0

com.apress
bsg6
1.0

chapter10-oauth
1.0
war

org.springframework.boot
spring-boot-starter-web

org.yaml
snakeyaml

org.springframework.boot
spring-boot-starter-security

org.springframework.boot
spring-boot-starter-mustache

org.springframework.boot
spring-boot-starter-oauth2-client

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

repackage

Listing 10-39
chapter10-oauth/pom.xml
```

The new dependency you’ll notice we’ve added is `spring-boot-starter-oauth2-client`. Given what we’ve seen with Spring Boot starters, this will be no different and will definitely be the simplest way you’ve ever integrated OAuth into an application before. Let’s create our `SpringApplication` next.

```
package com.bsg6.chapter10;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication()
public class GatewayOAuthApplication {
public static void main(String[] args) {
SpringApplication.run(GatewayOAuthApplication.class, args);
}
}
Listing 10-40
chapter10-oauth/src/main/java/com/bsg6/chapter10/GatewayOAuthApplication.java
```

Easy enough and if you ran the application here, Spring Boot’s Spring Security would default to using a regular form login with a generated security password as in the first example. The issue is that we haven’t specified any oauth providers to integrate with. We’ll fix that in a moment. First thing we need to do is to create a new GitHub application.

1.  Visit the following page to create a new GitHub application: [`https://github.com/settings/developers`](https://github.com/settings/developers).

2.  Click on the button “New OAuth App.”

3.  Give the application a name and description if you’d like.

4.  For the Homepage URL, you can put in `http://localhost:8080.`

5.  For the Authorization callback URL use: `http://localhost:8080/login/oauth2/code/github.`

6.  Next hit **Register application**, and you’ll be presented with a page which gives you a Client ID which you should copy and a button to “Generate a new client secret.”

7.  Take note of the client id and client secret as we’ll use these next.

We will avoid putting any secrets into our `application.properties` and use a `.env` file placed in the `chapter10-oauth` module directory to hold our secrets. The items you noted for #7 above should be put in the placeholders in the file.

```
GITHUB_OAUTH_CLIENT_ID=
GITHUB_OAUTH_CLIENT_SECRET=
Listing 10-41
Our secret .env file in action
```

Next, let’s use the above in our `application.properties` file. Line 2 contains a special config which allows us to utilize the `.env` file in our code and omit secrets from this file. The `optional` name tells Spring that if the resource doesn’t exist not to throw an error and the suffix of `[.properties]` indicates that the name of the file can either be `.env` or `.env.properties` and be valid. Lines 4 and 5 are the lines where we can tell Spring Boot’s OAuth implementation what the client id and client secret are.

```
spring.mustache.suffix:.html
spring.config.import=optional:file:.env[.properties]
spring.security.oauth2.client.registration.github.client-id=${GITHUB_OAUTH_CLIENT_ID}
spring.security.oauth2.client.registration.github.client-secret=${GITHUB_OAUTH_CLIENT_SECRET}
Listing 10-42
chapter10-oauth/src/main/resources/application.properties
```

The last two things we will do to flesh out our OAuth example is the `@Controller` which defines a single route at the root `/` and `/home` for good measure.

```
package com.bsg6.chapter10;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
@Controller
public class HomeController {
@GetMapping(value={"/", "/home"})
public String home() {
return "home";
}
}
Listing 10-43
chapter10-oauth/src/main/java/com/bsg6/chapter10/HomeController.java
```

Lastly, we create a template which will show after we’ve successfully logged in through our OAuth server GitHub.

```

Home Page

Home Page
You've successfully logged in!

Listing 10-44
chapter10-oauth/src/main/resources/templates/home.html
```

Open up a terminal and run the application `mvn spring-boot:run`, and open your browser to http://localhost:8080.

You will immediately be redirected to a GitHub login screen.

![](images/471370_2_En_10_Chapter/471370_2_En_10_Fig3_HTML.png)

A Git Hub login page on a web browser, with fields for username or email and password. It includes options to sign in, create an account, and links for support and password recovery.

Figure 10-3

Github login

After successfully logging in and authorizing the application you created, you will see the following.

![](images/471370_2_En_10_Chapter/471370_2_En_10_Fig4_HTML.png)

A webpage on a web browser indicating successful user login. It features a confirmation message of successful authentication.

Figure 10-4

Home page

As with the other examples we’ve used in this chapter, we can always supersede the default config in favor of our own custom implementation. There are a lot of methods available from the object returned from the `oauth2Login()` method are varied, but some that are pretty useful to configure and not accept the defaults are as follows:

*   `loginPage("/my_login")` – Allows you to configure an endpoint to serve the login screen rather than the Spring Boot default

*   `defaultSuccessUrl()` – The redirect to use upon success

*   `failureUrl()` – The redirect to use should the login fail (i.e., the user uses invalid credentials)

*   `successHandler()` – Custom logic handler for a successful user authentication which must implement `AuthenticationSuccessHandler` that generally would be a redirect or forward to a success page

*   `failureHandler()` – Custom logic handler for authentication failure which implements `AuthenticationFailureHandler` that usually redirects to the authentication page to try again

There are certainly more options which you can view more of within the documentation. See [`https://spring.io/projects/spring-security-oauth`](https://spring.io/projects/spring-security-oauth).

We’ve only scratched the very surface of the power that exists in Spring Security. The subproject has support for the simple like HTTP BASIC, Digest, and Form-based authentication to the more complex like OpenID, JOSSO, and SSO using Central Authentication Service (CAS). It has integrations which allow you to pull data from LDAP, Kerberos, and even Windows NTLM. Functionality that you would expect for a security framework to provide, it offers, and with configurability at its core to modify to your heart’s content.

The most important thing to remember is if you’re using anything standard or best-practice in the industry, Spring Security likely has you covered. If not, it is fully pluggable and customizable to work with your particular custom authentication implementations.

## The Road to Spring Security 7

Our goal is to always be prepared, and while we talk about Spring Security 6.x, a quick mention of some upcoming changes with the next version is appropriate. In the next version the non-lambda configuration style will be deprecated in favor of the Lambda DSL. In all of the code we’ve seen above, we’re intentionally using the Lambda-style DSL for forward compatibility. Let’s see an example from our codebase.

```
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
http
.authorizeHttpRequests(authorize -> authorize
.requestMatchers("/login")
.permitAll()
.requestMatchers("/dashboard")
.hasRole("USER")
.requestMatchers("/admin_dashboard")
.hasRole("ADMIN")
.anyRequest()
.authenticated()
)
.formLogin(formLogin -> formLogin
.loginPage("/login")
.permitAll()
);
return http.build();
}
Listing 10-45
Lambda DSL style example chapter10-custom/src/main/java/com/bsg6/chapter10/GatewaySecurityConfig.java
```

According to the developers, this was done in an effort to reduce confusion as the nested `.and()` and multiple return types were increasingly impossible to debug. The new method always returns an instance of the `HttpSecurity` object for further configuration and keeps the configuration style more in line with other packages like Spring Integration and Spring Cloud Gateway. The Lambda DSL style also works similarly and can be applied for WebFlux.

## Next Steps

In Chapter [11](#471370_2_En_11_Chapter.xhtml), we’ll explore Spring Batch for data processing and Spring Modulith for a better way of organizing your codebase. Together, we’ll learn how to set up, configure, and run batch jobs at enterprise scale and how to structure your applications in an even more modular way.

Footnotes [1](#471370_2_En_10_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_10_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_10_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_10_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_10_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_10_Chapter.xhtml#Fn6_source)  

# 11. Spring Batch and Modulith

In this chapter, we’re going to talk about two recent but lesser known libraries in the Spring Framework ecosystem: Spring Batch and Spring Modulith. Both can aid in providing useful tooling around functions we all have to deal with in software development, and provide some guidance and needed structure as part of the framework we all love.

## Introduction to Batch

Batch [`https://spring.io/projects/spring-batch`](https://spring.io/projects/spring-batch) is a set of lightweight functions built with the robustness to be integrated in an enterprise context. The features available in batch are

*   Transaction management

*   Chunk-based processing

*   Declarative I/O

*   Start/Stop/Restart

*   Retry/Skip

*   Web-based administration interface with Spring Cloud Data Flow [`https://dataflow.spring.io/`](https://dataflow.spring.io/)

### Configuration

In this section, we’ll create a small example which will import data from two separate CSV files to kickstart our data with the Band Gateway app.

First, we need to create our directory structure, starting in the overall project directory.

```
mkdir -p chapter11-batch/src/main/java/com/bsg6/chapter11
mkdir -p chapter11-batch/src/main/resources
Listing 11-1
Creating the directory structure with POSIX
```

We will need to set up our `pom.xml` – as in previous chapters.^([^(107)](#fn107)) We’ll be using Spring Boot so our setup time will be massively reduced.

```

4.0.0

com.apress
bsg6
1.0

chapter11-batch
1.0

org.springframework.boot
spring-boot-starter-batch

org.springframework.boot
spring-boot-starter-data-jpa

com.h2database
h2
runtime

org.springframework.boot
spring-boot-maven-plugin
${springBootVersion}

repackage

Listing 11-2
chapter11-batch/pom.xml
```

In an effort to reduce complexity, we won’t deal with importing dependencies from other projects and instead import `spring-boot-starter-data-jpa` and `h2` again, and the newcomer and reason for this chapter is `spring-boot-starter-batch` which will help us get started here super fast.

### Our First Job

The application will use CSV files that we define in our `application.properties` and import a list of artists first, and then go through and import a list of songs for each artist.

Our entrypoint application is the same as in previous chapters with Spring Batch. In the end, that may even be the point.

```
package com.bsg6.chapter11;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class GatewayBatchApplication {
public static void main(String[] args) {
SpringApplication.run(GatewayBatchApplication.class, args);
}
}
Listing 11-3
chapter11-batch/src/main/java/com/bsg6/chapter11/GatewayBatchApplication.java
```

First, let’s copy over any properties from our `chapter09-jpa` example into our new one. We will add two new properties for our CSV files named `file.artists.input` and `file.songs.input,` and we’ll show the contents of each CSV file but at a glance the artists assumed headers will be `id,name` and for songs it will be `id,name,artistId`.

```
file.artists.input=artists.csv
file.songs.input=songs.csv
spring.datasource.url=jdbc:h2:./chapter-11-batch;DB_CLOSE_DELAY=-1;
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.batch.jdbc.initialize-schema=always
Listing 11-4
chapter11-batch/src/main/resources/application.properties
```

We’re including any needs from Spring and JPA so that Spring Batch has access to create the tables it needs which we’ll include the DDL from the reference documentation. They map fairly closely to the object hierarchy of Spring Batch, `BATCH_JOB_EXECUTION` → `JobExecution`, etc.

![](images/471370_2_En_11_Chapter/471370_2_En_11_Fig1_HTML.jpg)

A diagram of the spring batch database schema. It depicts 6 sections titled batch step execution context, batch step execution, batch job execution context, batch job execution, batch job instance, and batch job execution params. Each section has various fields, and the relationships between these sections are indicated by lines.

Figure 11-1

Spring Batch

We need data for importing to work with, so let’s look at our CSV file of artists.

```
1,Threadbare Loaf
2,Therapy Zeppelin
3,Clancy in Silt
4,Electric Octopus
5,Quantum Quokkas
6,Neon Pigeons
7,Galactic Tofu
8,Velvet Unicorns
9,Pixelated Socks
10,Moonlight Llamas
11,Holographic Jukebox
12,Disco Dinosaurs
13,Steam-Powered Funk
14,Astral Alpacas
15,Time-Traveling Turnips
16,Cosmic Wombats
17,Funky Fireflies
18,Space Bananas
19,Robot Raccoons
20,Synthwave Sloths
21,Subatomic Sandwiches
22,Polka-Dotted Platypuses
Listing 11-5
chapter11-batch/src/main/resources/artists.csv
```

For each artist we’ve decided that there will be five gateway songs available, with the exception of the artists and songs we’ve used in previous examples.

```
1,Someone Stole the Flour,1
2,What Happened To Our First CD?,1
3,Mfbrbl Is Not A Word,2
4,Medium,2
5,Igneous,3
6,Electric Rhapsody,4
7,Funky Fusion Groove,4
8,Retro Groove Revival,4
9,Disco Fever Dream,4
10,Summer Night Serenade,4
11,Midnight Jazz Blues,5
12,Soulful Swing Shuffle,5
13,Daydream Delight,5
14,Jazzed Up Memories,5
15,Smooth Groove Affair,5
16,Velvet Whispers in Time,6
17,Rainbow Romance Chronicles,6
18,Whimsical Wonderland Waltz,6
19,Enchanted Evening Echoes,6
20,Heartfelt Harmonies of Love,6
21,Static Funk Odyssey,7
22,Neon Nights Reflections,7
23,Electro Elegance Fantasy,7
24,Swingin' Socks Stomp,7
25,Pixelated Party Celebration,7
26,Moonlit Memories Lullaby,8
27,Lullaby Serenade Under the Stars,8
28,Summer Tango Serenity,8
29,Starry Nights Whispers,8
30,Luna Blues Serenade,8
31,Groovy Heartbeat Journey,9
32,Retrowave Rhythms in Motion,9
33,Disco Fever Fantasy,9
34,Funky Fusion Groove Groove,9
35,Groove Expedition Quest,9
36,Alpaca Adventure Chronicles,10
37,Timeless Tango Tales,10
38,Turnip Twist Delight,10
39,Interdimensional Groove Quest,10
40,Carrot Boogie Bliss,10
41,Funky Quest Odyssey,11
42,Party Platter Extravaganza,11
43,Banana Boogie Blast,11
44,Funky Groove Fiesta,11
45,Infinite Joy Journey,11
46,Firefly Frenzy Fantasia,12
47,Neon Dreams Reverie,12
48,Glowing Vibes Celebration,12
49,Starlight Serenade Serenity,12
50,Firefly Fantasia Fantasia,12
51,Electric Jam Journey,13
52,Interstellar Rhythms in Motion,13
53,Wombat Waltz Whimsy,13
54,Carousel Swing Delight,13
55,Whimsical Whimsy Whimsy,13
56,Disco Delight Fantasy,14
57,Safari Serenade Serenity,14
58,Banana Samba Sensation,14
59,Neon Nightscape Nocturne,14
60,Funky Fiesta Fiesta,14
61,Robo Rock Revolution,15
62,Digital Dance Delight,15
63,Electro Blues Bliss,15
64,Emotional Echoes Echoes,15
65,Raccoon Rave Rave,15
66,Synthwave Sunset Serenity,16
67,Glitch Groove Quest,16
68,Neon Vibes Reverie,16
69,Robo-Boogie Extravaganza,16
70,Synthwave Serenade Serenade,16
71,Sandwich Swing Stories,17
72,Quiche Quest Quest,17
73,Crouton Conundrum Delight,17
74,Pickle Polka Extravaganza,17
75,Sandwich Shuffle Shuffle,17
76,Platypus Anthem Adventure,18
77,Polka-Dotted Dance Delight,18
78,Quokka Quesadilla Quest,18
79,Picnic Serenade Serenade,18
80,Dance of the Platypi Stories,18
81,Smooth Operator,19
82,Summer Breeze,19
83,Sunset Serenade,19
84,Island Dreams,19
85,Beachside Blues,19
86,Moonlit Sonata,20
87,Starry Night Waltz,20
88,Nocturnal Serenade,20
89,Midnight Whispers,20
90,Dreams of Stardust,20
91,Electric Dreamscape,21
92,Pixelated Reverie,21
93,Neon Nocturne,21
94,Holographic Harmony,21
95,Technicolor Tango,21
96,Synthwave Dreams,22
97,Cyberpunk Groove,22
98,Virtual Reality Rhapsody,22
99,Pixelated Dreamscape,22
100,Future Funk Fusion,22
Listing 11-6
chapter11-batch/src/main/resources/songs.csv
```

Now that we have our CSV files and the references to them in place, let’s take a look at creating a `@Configuration` file which will drive the batch processes that we’re looking to enable.

```
@Configuration
public class GatewayConfiguration {
@Value("${file.artists.input}")
private String artistsFileInput;
@Value("${file.songs.input}")
private String songsFileInput;
Listing 11-7
Declarations for chapter11-batch/src/main/java/com/bsg6/chapter11/GatewayConfiguration.java
```

Our declarations are fairly simple; we should have two variables now with the values in the `application.properties`. This is good because we’ve got two beans we’re defining next which will read in the data we need from the input files. To read in data to Spring Batch, we use an implementation of `ItemReader`. The types of input are varied, but the simplest are a flat file, an XML file,^([^(108)](#fn108)) or a database. In most cases, a reader will return a valid and usable domain object if a record exists at all, but `null` is also valid to indicate no underlying results.

```
public interface ItemReader {
T read() throws Exception, UnexpectedInputException, ParseException, NonTransientResourceException;
}
```

The `FlatFileItemReaderBuilder` is an implementation of `ItemReader` from Spring Batch which helps us ingest data from a given resource location (here we’re using a classpath resource), helps us define the fields we see in the document, and maps them to our bean which is `Artist`. We do the same thing for `Song` with the only difference being that we include `artistId,` so our songs can map to our unique and diverse artists in our catalog.

```
@Bean
public FlatFileItemReader artistReader() {
return new FlatFileItemReaderBuilder().name("artistsReader")
.resource(new ClassPathResource(artistsFileInput))
.delimited()
.names(new String[] { "id", "name" })
.fieldSetMapper(new BeanWrapperFieldSetMapper() {{
setTargetType(Artist.class);
}})
.build();
}
@Bean
public FlatFileItemReader songReader() {
return new FlatFileItemReaderBuilder().name("songsReader")
.resource(new ClassPathResource(songsFileInput))
.delimited()
.names(new String[] { "id", "name", "artistId" })
.fieldSetMapper(new BeanWrapperFieldSetMapper() {{
setTargetType(Song.class);
}})
.build();
}
Listing 11-8
Readers in chapter11-batch/src/main/java/com/bsg6/chapter11/GatewayConfiguration.java
```

The other side of the read equation is obviously the writer. In the same way that Spring Boot helped us on the read side, it will do equally on the write side. All writers implement the `ItemWriter` interface. The writer can assume that it will get a list of items to write out to the preferred method and that each batch of data will be chunked according to rules in the job, and at the end of writing the list will be responsible for any flushing operation on the datasource in use.

```
public interface ItemWriter {
void write(Chunk chunk) throws Exception;
}
```

In our example we’ll use the `JdbcBatchItemWriterBuilder` for each of our types `Artist` and `Song` and craft a SQL prepared statement. We use the `dataSource` that is created from our `application.properties` setup here which is autowired into the method.

```
@Bean
public JdbcBatchItemWriter writer(DataSource dataSource) {
return new JdbcBatchItemWriterBuilder()
.itemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider())
.sql("INSERT INTO artist (id, name) VALUES (:id, :name)")
.dataSource(dataSource)
.build();
}
@Bean
public JdbcBatchItemWriter songsWriter(DataSource dataSource) {
return new JdbcBatchItemWriterBuilder()
.itemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider())
.sql("INSERT INTO song (id, artist_id, name, votes) VALUES (:id, :artistId, :name, 0)")
.dataSource(dataSource)
.build();
}
Listing 11-9
Writers in chapter11-batch/src/main/java/com/bsg6/chapter11/GatewayConfiguration.java
```

When we build our `Job,` we use a `JobBuilder` to set up all the necessary pieces to make it work. A `Job` is plainly just a container for `Step` instances. Each `Step` in a given flow can be processed using chunks or Tasklets. A Tasklet is meant to perform one task within a step with possibly multiple steps depending on how things get processed. A chunk on the other hand will take a fixed amount of records (a chunk) and process through the `.reader()`, `.processor(),` and `writer()` flow until no more records exist. The builder takes two important pieces that will be used by the `JobLauncher` to support the execution of a batch: the job name and a `JobRepository`. The `JobRepository` that handles the CRUD operations for a `Job` has defaults that are likely fine for most situations you will run into, and the reference documentation has a full write-up available for different cases where you’re likely to want to customize.

For our case, we’re going to configure the execution of two steps. One to import the artists, and the next to import the songs for previously imported artists. If we were building for a production application, we’d do more verifying and checking, and since there is only so much paper, we’ll keep this simple. One of the first things we’ll need to add is a `JobParametersIncrementer` for keeping the id’s rolling forward, and we’re not doing anything fancy so we’ll just use the implementation `RunIdIncrementer`.

We’ll talk more about the `listener` in the next section, and it basically allows you to trigger code before and after a job execution with the `JobExecution` object. There are `*Listener` objects we can attach to each `Step` as well. They are all step scoped, and with the exception of the `StepExecutionListener` and `SkipListener,` all have handlers in the event of an error.

*   `StepExecutionListener` – Notification before and after a step execution

*   `ChunkListener` – Notification before a chunk and after a chunk

*   `ItemReadListener` – Notification before and after each item read

*   `ItemProcessListener` – Notification before and after each `ItemProcessor` run

*   `ItemWriteListener` – Notification before and after each item write

*   `SkipListener` – Notification upon a skip happening in the read, process, or write for further handling

The different flows for a `Step` can be an entire chapter. For our needs, we are looking to do something sequential, first `step1`, then `step2,` and if any error occurs during the process, the job stops.

```
@Bean
public Job importJob(JobRepository jobRepository, Step step1, Step step2, JobNotificationListener listener) {
return new JobBuilder("importJob", jobRepository)
.incrementer(new RunIdIncrementer())
.listener(listener)
.start(step1)
.next(step2)
.build();
}
Listing 11-10
Job definition in chapter11-batch/src/main/java/com/bsg6/chapter11/GatewayConfiguration.java
```

Let’s say though, that upon a failure you wanted to instead of failing, move to a different step? We can use a combination of methods `on`, `from,` and `to` in the proper order to simulate our new flow chart. We don’t implement this flow in our own code but thought it would be great to include as an example of how you could configure to your need.

```
return new JobBuilder("myJob", jobRepository)
.start(step1)
.on("*").to(step2)
.from(step1).on("FAILED").to(stepError)
.end()
.build();
```

Our flow basically says run `step1` and for all cases move on to `step2`. In the event from `step1` the exit status is `FAILED,` we move to `stepError`. One can imagine this can get fairly complex for any specific use case, so please refer to the reference documentation for Spring Batch for more details.

For the two steps we’re working with, we’re going to create using the `StepBuilder` and name them very simply `step1` and `step2`. With a batch job, we will want to provide some reasonable split of data being processed, and the method `chunk` is how we get that done. For each step, we provide a reader and writer, and we’ll go into a bit more detail in the next section, but the song import step will have a `processor` running between so we can transform what is created by the reader before we write to the database.

```
@Bean
public Step step1(JobRepository jobRepository, PlatformTransactionManager transactionManager, JdbcBatchItemWriter writer) {
return new StepBuilder("step1", jobRepository)
. chunk(5, transactionManager)
.reader(artistReader())
.writer(writer)
.build();
}
@Bean
public Step step2(JobRepository jobRepository, PlatformTransactionManager transactionManager, JdbcBatchItemWriter songsWriter) {
return new StepBuilder("step2", jobRepository)
. chunk(10, transactionManager)
.reader(songReader())
.processor(processor())
.writer(songsWriter)
.build();
}
Listing 11-11
Steps definition in chapter11-batch/src/main/java/com/bsg6/chapter11/GatewayConfiguration.java
```

### Processing and Listening

The steps of processing are generally applied to a step in our batch process. The `ItemProcessor` is the transformation step of ETL processing,^([^(109)](#fn109)) common in batch transactions. With a processor one can transform the object given into a new object, the same object, or return `null`. If the processor returns `null,` it is assumed that the processing and batch job itself should halt.

For our example, we’ve got a `transient` variable defined in our `Song` entity for `artistId,` so given a non-null value for `artistId` we’re creating a fresh `Artist` object, setting its `id,` and attaching it to the `Song`. Given how we’re doing writing, this isn’t strictly necessary, but if we were using something like Hibernate or another ORM, the binding would help.

```
package com.bsg6.chapter11;
import org.springframework.batch.item.ItemProcessor;
public class SongProcessor implements ItemProcessor {
@Override
public Song process(final Song song) {
if (song.getArtistId() != null) {
Artist artist = new Artist();
artist.setId(song.getArtistId());
song.setArtist(artist);
}
return song;
}
}
Listing 11-12
Processor definition in chapter11-batch/src/main/java/com/bsg6/chapter11/SongProcessor.java
```

Our `SongProcessor` is injected into the `Step` we want to execute on it (here the step is `step2`) and this definition in the configuration.

```
@Bean
public SongProcessor processor() {
return new SongProcessor();
}
Listing 11-13
Processor definition in chapter11-batch/src/main/java/com/bsg6/chapter11/GatewayConfiguration.java
```

A `@Component` with an implementation of `JobExecutionListener` which defines methods for `beforeJob` and `afterJob` with an instance of the `JobExecution` which will run or just ran. We will use the listener here to do a verification spot check of the database to see that things got added that we expected. We’ll inject a `JdbcTemplate` and do some `SELECT` statements against the `artist` and `song` tables and print them out to the logger.

```
package com.bsg6.chapter11;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.batch.core.BatchStatus;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.JobExecutionListener;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Component;
@Component
public class JobNotificationListener implements JobExecutionListener {
private static final Logger log =
LoggerFactory.getLogger(JobNotificationListener.class);
private final JdbcTemplate jdbcTemplate;
public JobNotificationListener(JdbcTemplate jdbcTemplate) {
this.jdbcTemplate = jdbcTemplate;
}
@Override
public void afterJob(JobExecution jobExecution) {
if(jobExecution.getStatus() == BatchStatus.COMPLETED) {
log.info("DONE: Time to verify the results");
log.info("Let's look at our list of artists!");
jdbcTemplate.query("SELECT name FROM artist",
(rs, row) -> new Artist(
rs.getString(1)
)
).forEach(artist ->
log.info("Found  in the database.",
artist.getName()));
log.info("Let's look at our list of songs!");
jdbcTemplate.query("SELECT name FROM song",
(rs, row) -> new Song(
rs.getString(1))
).forEach(song ->
log.info("Found  in the database.",
song.getName()));
}
}
}
Listing 11-14
chapter11-batch/src/main/java/com/bsg6/chapter11/JobNotificationListener.java
```

Now that we have everything in place, we can run our batch importing tool and seed the database with the contents of the CSV files.

```
mvn -pl chapter11-batch -am clean package
java -jar chapter11-batch/target/chapter11-batch-1.0.jar
```

In the Spring docs, there is a lot more to discover, and we’ve only just scratched the surface. The reference documentation for the latest version is available at [`https://docs.spring.io/spring-batch/docs/current/reference/html/`](https://docs.spring.io/spring-batch/docs/current/reference/html/).

## Introduction to Modulith

Spring Modulith [`https://spring.io/projects/spring-modulith`](https://spring.io/projects/spring-modulith) enables us to build well-structured applications based on a module-driven domain design. It’s most useful features are verification of modular arrangements, event publication and consumption, integration testing individual modules, and creating documentation snippets based on the arrangements.

We’ll start where we always have, at the Spring Boot application which is the same as we’ve seen prior. There are certainly different things you can specify on top of the defaults, but for our needs we only need the base.

```
package com.bsg6.chapter11;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@SpringBootApplication
public class GatewayApplication {
public static void main(String[] args) {
SpringApplication.run(GatewayApplication.class, args);
}
}
Listing 11-15
chapter11-modulith/src/main/java/com/bsg6/chapter11/GatewayApplication.java
```

From this base package, every direct subpackage underneath the Spring Boot application is considered an application module, and those application modules are all considered the API package and the only package allowed to have incoming dependencies from other modules.

We’ll take some of the concepts from our example application and build modules that can function independently with boundaries around certain items within the module. For example, we could say that we have two application modules which can work “somewhat” independently of each other `artist` and `song`.

Our application modules might look like this.

![](images/471370_2_En_11_Chapter/471370_2_En_11_Fig2_HTML.jpg)

A diagram with 2 entities labeled artist and song at the top, point arrows towards a third entity labeled C gateway application at the bottom.

Figure 11-2

Application module example

If we were building in a non-modulith style, we would likely build out something simple like this for the `artist` package.

![](images/471370_2_En_11_Chapter/471370_2_En_11_Fig3_HTML.jpg)

A flow diagram of 4 classes, namely C artist, C artist repository, C artist service, and C example controller. The last 3 classes find and get artist by i d as C artist has i d and name attributes. C artist points arrows to all other classes, and C artist repository points an arrow to artist service.

Figure 11-3

Artist package

And something similar for `song`.

![](images/471370_2_En_11_Chapter/471370_2_En_11_Fig4_HTML.jpg)

A diagram depicts a box labeled song with 4 classes, namely C song, C song controller, C song service, and C song repository. The last 3 classes get and find song by i d as C song has i d, title, and artist i d attributes. C song leads to the other 3 classes. C song controller leads to C song service which further leads to song repository.

Figure 11-4

Song

To build some of these pieces in isolation and treat their respective base packages as an application module, we would probably hide a lot of this implementation from other modules to keep things cleaner.

With Spring Modulith, unless otherwise specified, any subpackages of an application module are internal to that application module. Let’s take a look at how this might look for the `artist` application module.

![](images/471370_2_En_11_Chapter/471370_2_En_11_Fig5_HTML.jpg)

A flow diagram of 4 classes, namely C artist, C artist repository, C artist service, and C example controller. The last 3 classes find and get artist by i d as C artist has i d and name attributes. C artist points arrows to all other classes, and C artist repository points an arrow to artist service.

Figure 11-5

Artist module

This looks okay for now, but what are we unnecessarily exposing externally to the module? The `Artist` object which is supposed to be internal to the module. It’s obviously time for a DTO.^([^(110)](#fn110))

![](images/471370_2_En_11_Chapter/471370_2_En_11_Fig6_HTML.jpg)

A flow diagram of 5 classes, namely C artist, C artist repository, C artist service, C artist D T O and C example controller. Other classes get and find C artist and C artist D T O as they have i d and name attributes. C artist and C artist service lead to artist repository. C artist service also leads to C artist D T O, and it leads to example.

Figure 11-6

Artist module revised

Now, you can see that the `ArtistService` accesses `ArtistDTO` and `Artist,` but the external controller `ExampleController` only has access to `ArtistService` and `ArtistDTO`.

First, let’s take a look at our application module code in the base package which includes `ArtistDTO` and `ArtistService`.

```
package com.bsg6.chapter11.artist;
import com.bsg6.chapter11.artist.internal.Artist;
import com.bsg6.chapter11.artist.internal.ArtistRepository;
import org.springframework.stereotype.Service;
import java.util.List;
@Service
public class ArtistService {
private final ArtistRepository repository;
public ArtistService(ArtistRepository repository) {
this.repository = repository;
}
public List findAll() {
return repository.findAll();
}
public ArtistDTO getArtist(Integer id) {
return repository.findById(id)
.map(artist -> new ArtistDTO(artist.getId(), artist.getName()))
.orElse(null);
}
}
Listing 11-17
chapter11-modulith/src/main/java/com/bsg6/chapter11/artist/ArtistService.java
```

```
package com.bsg6.chapter11.artist;
public record ArtistDTO(Integer id, String name) {
}
Listing 11-16
chapter11-modulith/src/main/java/com/bsg6/chapter11/artist/ArtistDTO.java
```

The `ArtistService` receives an `Artist` object back from the repository and transforms it to an `ArtistDTO` for external modules to consume. Next, we’ll take a look at the classes within the subpackage we’re aptly naming `internal`.

```
package com.bsg6.chapter11.artist.internal;
import org.sp     ringframework.data.jpa.repository.JpaRepository;
public interface ArtistRepository extends JpaRepository {
}
Listing 11-19
chapter11-modulith/src/main/java/com/bsg6/chapter11/artist/internal/ArtistRepository.java
```

```
package com.bsg6.chapter11.artist.internal;
import jakarta.persistence.Entity;
import jakarta.persistence.Id;
@Entity
public class Artist {
@Id
public Integer id;
private String name;
public Integer getId() {
return id;
}
public void setId(Integer id) {
this.id = id;
}
public String getName() {
return name;
}
public void setName(String name) {
this.name = name;
}
}
Listing 11-18
chapter11-modulith/src/main/java/com/bsg6/chapter11/artist/internal/Artist.java
```

Code-wise it should look like previous examples that you’ve seen throughout this book. Since these pieces of code are within a subpackage of the application module, and unless otherwise explicitly specified, it will be unavailable outside of the application module.

Let’s take a look at the application module for `song`.

```
package com.bsg6.chapter11.song;
import com.bsg6.chapter11.artist.ArtistDTO;
import com.bsg6.chapter11.artist.ArtistService;
import com.bsg6.chapter11.song.internal.SongRepository;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Service;
@Service
public class SongService {
private final SongRepository repository;
private final ArtistService artistService;
public SongService(SongRepository repository,
ArtistService artistService) {
this.repository = repository;
this.artistService = artistService;
}
public SongDTO getSong(Integer id) {
return repository.findById(id)
.map(song -> {
ArtistDTO artistDTO =
artistService.getArtist(song.getArtistId());
return artistDTO != null ?
new SongDTO(id, song.getTitle(), artistDTO) :
null;
})
.orElse(null);
}
}
Listing 11-21
chapter11-modulith/src/main/java/com/bsg6/chapter11/song/SongService.java
```

```
package com.bsg6.chapter11.song;
import com.bsg6.chapter11.artist.ArtistDTO;
public record SongDTO(Integer id, String name, ArtistDTO artistDTO) {
}
Listing 11-20
chapter11-modulith/src/main/java/com/bsg6/chapter11/song/SongDTO.java
```

As with the `ArtistService` before it, we’re receiving a `Song` object which is part of the `internal` package and translating it to a `SongDTO`. One thing to note here is that we are injecting `ArtistService` into our `SongService` so that we can make a call to get a `ArtistDTO` given an `artistId`. In a production system you will likely not be doing this, and this is a book example so please suspend disbelief and what you’re seeing.

The `internal` package contains `Song` and `SongRepository` both of which cannot be accessed outside of the application module.

```
package com.bsg6.chapter11.song.internal;
import org.springframework.data.jpa.repository.JpaRepository;
public interface SongRepository extends JpaRepository {
}
Listing 11-23
chapter11-modulith/src/main/java/com/bsg6/chapter11/song/internal/SongRepository.java
```

```
package com.bsg6.chapter11.song.internal;
import jakarta.persistence.Entity;
import jakarta.persistence.Id;
@Entity
public class Song {
@Id
private Integer id;
private Integer artistId;
private String title;
public Integer getId() {
return id;
}
public void setId(Integer id) {
this.id = id;
}
public Integer getArtistId() {
return artistId;
}
public void setArtistId(Integer artistId) {
this.artistId = artistId;
}
public String getTitle() {
return title;
}
public void setTitle(String name) {
this.title = name;
}
}
Listing 11-22
chapter11-modulith/src/main/java/com/bsg6/chapter11/song/internal/Song.java
```

To ensure we’re as decoupled as we can possibly be, interacting between services is best served by being an event publication and consumption. The reference documentation goes into further detail, but let’s imagine for a second that we’re adding a `vote` service call to `SongService` and we want that to notify the `Artist` that they received a vote on one of their songs. It’s a book example, bear with me.

```
import org.springframework.context.ApplicationEvent;
...
private final ApplicationEventPublisher events;
public SongService(SongRepository repository, ArtistService artistService, ApplicationEventPublisher events) {
this.events = events;
...
}
@Transactional
public void vote(Integer songId) {
events.publishEvent(new VoteDTO(songId));
}
Listing 11-24
Events in SongService
```

We’re publishing an application event using Spring’s `ApplicationEventPublisher`. On the `ArtistService` side, we’d be listening for these events and can handle it in the most appropriate manner.

```
@Async
@TransactionalEventListener
void on(VoteDTO event) { /* do something */ }
Listing 11-25
Event listener in ArtistService
```

The preceding sample is by no means complete, and the reference documentation has a much more fleshed out example that you can peruse at your leisure. Suffice it to say that with just these fundamentals, you’ll be structuring your monoliths like microservices in no time and not having to deal with version hell either. Now that we’ve gotten a bit of a look at how things get set up with Spring Modulith, we’ll look at methods of verification and documentation that we can use to inform developers of the logical splits.

### Verification

The ability to enforce the module rules we’ve set in place with Modulith is a pretty big benefit which we can take advantage of. Let’s take a look at an item we can add to one of our tests, so if a code path has gone outside its bounds, the test will fail.

```
package com.bsg6.chapter11;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.modulith.core.ApplicationModules;
import org.springframework.test.context.testng.AbstractTestNGSpringContextTests;
import org.testng.annotations.Test;
@SpringBootTest(classes = GatewayApplication.class)
public class GatewayApplicationTest extends AbstractTestNGSpringContextTests {
@Test
public void testModulesValid() {
ApplicationModules.of(GatewayApplication.class).verify();
}
}
Listing 11-26
chapter11-modulith/src/test/java/com/bsg6/chapter11/GatewayApplicationTest.java
```

The checks will include

*   Dependencies between modules have to form directed, acyclic graph^([^(111)](#fn111))

*   All references to types that reside in application module internal packages are rejected

*   Explicitly allowed application module dependencies only if specified. If defined allowed dependencies are specifically configured any others will be rejected

If we were to add a reference to `Artist` in the `SongService` and ran the tests with maven, we would see something like the following:

```
[ERROR] Failures:
[ERROR]   GatewayApplicationTest>AbstractTestNGSpringContextTests.run:154->testModulesValid:13 » Violations - Module 'song' depends on non-exposed type com.bsg6.chapter11.artist.internal.Artist within module 'artist'!
Method  calls constructor ()> in (SongService.java:19)
```

### Documentation

One of the other nice features of Spring Modulith is the ability to generate documentation for our modules. We can do this by running tests for the module using `mvn test`.

```
package com.bsg6.chapter11;
import org.springframework.modulith.core.ApplicationModules;
import org.springframework.modulith.docs.Documenter;
import org.testng.annotations.Test;
public class DocumentationTests {
ApplicationModules modules = ApplicationModules.of(GatewayApplication.class);
@Test
void writeDocumentationSnippets() {
new Documenter(modules)
.writeModulesAsPlantUml()
.writeIndividualModulesAsPlantUml();
}
}
Listing 11-27
chapter11-modulith/src/test/java/com/bsg6/chapter11/DocumentationTests.java
```

The documentation this generates is available in `target/spring-modulith-docs,` and here’s an example of what it generates.

![](images/471370_2_En_11_Chapter/471370_2_En_11_Fig7_HTML.jpg)

A diagram of a gateway application. It depicts a dashed container labeled gateway application, container. There are 2 components inside the container, namely song module and artist module. The song module uses the artist module.

Figure 11-7

Gateway application

If you aren’t much for graphical representations of your component module relationships, you can opt for a `Documenter` that writes them out in a documented textual form.

```
new Documenter(modules)
.writeModuleCanvases();
Listing 11-28
Alternative documenter showing textual canvases
```

The example that follows is mapping the `artist` module and turning it into an `asciidoc` block for us.

| **Base package** | `com.bsg6.chapter11.artist` |
| **Spring components** | *Controllers*• `c.b.c.a.ArtistController`*Services*• `c.b.c.a.ArtistService` |

Building things like this into the modular architecture is definitely a nice touch and useful for exploring the power of the Spring Modulith system even though it’s just barely reached 1.0.

In this section we’ve gone over how to set up simple application modules and prevent internal code from being accessed from the other modules, keeping a nice separation of concerns. We’ve gone through verification checks to ensure everything looks good and seen what the documenting of application modules looks like in broad strokes. Within Spring Modulith, there are many other modules that we can utilize to help in the process, and we highly recommend you crack open the reference documentation: [`https://docs.spring.io/spring-modulith/docs/current/reference/html/`](https://docs.spring.io/spring-modulith/docs/current/reference/html/).

## Next Steps

In the next chapter, we’ll look at what’s next in your Spring journey. We’ll touch on other interesting modules of Spring that we haven’t covered previously.

Footnotes [1](#471370_2_En_11_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_11_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_11_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_11_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_11_Chapter.xhtml#Fn5_source)  

# 12. Next Steps

By now, we’ve read about Spring and dependency injection, along with topics like web services (particularly REST services), transaction, persistence, and security. These are likely to be the “most important” parts of the Spring ecosystem, generally speaking, but we’ve barely scratched the surface of the Spring ecosystem itself, much less projects that use Spring without being part of the Spring project.

In this chapter, we’d like to look at some of the things programmers will possibly run into in the real world. It’s very far from complete; a book covering everything would take up multiple volumes and, in the end, be incomplete: new projects and features are developed all the time.

Note that this chapter has no code to speak of. This chapter isn’t trying to demonstrate features, even with limited scope; here, we’re merely pointing out some interesting projects in the wider Spring ecosystem and address some development concerns about which you might have thinking.^([^(112)](#fn112)) As such, they tend to be things the authors noticed as being interesting or relevant in some way, and that we thought might spark readers’ interest as they develop their own projects.

## A Criticism of Books

In your humble authors’ opinion, books are excellent resources for developers,^([^(113)](#fn113)) but they’re far from perfect.

The biggest problem books on tech have is that they tend to aim too low. Most of them present snippets of code, for one thing, which can work depending on what they’re trying to demonstrate, but for any real application, a ten-line sample of code just isn’t enough when your deployed codebase is made of thousands of lines of code and dozens of components.

That’s why in this book, where possible, we’ve opted to show **complete** classes, including `package` and `import` statements, to remove the ambiguity about what code goes where. The intent was that you could use the book’s text as a workable reference; you don’t have to figure out if you’ve included everything (or the right thing) when you’re looking at a given snippet, if the code we show is meant to be executed.

We’ve also preferred tests over running code and manually checking output. Tests are how you **know** code works; it’s easy to look at `"Hello, wor1d"` and miss that `"world"` has a number `1` instead of the *letter* `l`. If you look closely enough – and your font is clear enough – you can see it, but the human brain is fantastic at correcting what it sees to make sense, so there’s absolutely no blame for anyone if you read “Hello, world” when “world” has a number embedded in it.

Tests don’t have a problem of autocorrecting results to be valid. The output has to be right. If your test uses a regex – `"Hello, wor[1l]d"` – which would pass, then you can see, in the test, that the matcher is using a regular expression that *probably* doesn’t make sense in the context of assuring that your output matches what you expect it to.

Tests can be aggravating to write, but they’re tools that should be in everyone’s reach. We could have done **more** testing in this book, but what we strove for was a good balance of testing for intent: if a service is supposed to be able to write, read, update, and delete data that looked a certain way, we’ve tried to represent some sensible aspect of those requirements in tests.

There are aspects of testing we’ve not touched upon, like performance (“Can the service handle one thousand updates from four hundred users in three seconds or less, with a memory impact of…​”), but that leads to another aspect of knowledge transfer that books suffer with: scope.

A book like this has to be limited in scope, or else we’re covering too much. We chose to focus on aspects of the Spring Framework and its accompanying ecosystem that you’re likely to encounter in real development, such that you were able to work with some confidence in what to expect the framework to do and what a real project would look like. But to go too much farther in some directions would cover deployment scenarios that are simply too broad for a book on **Spring** to cover…​ unless we had another few years, at least, to write, plus a few more volumes (leading to a version of Knuth’s *The Art of Computer Programming*^([^(114)](#fn114)) a multi-volume set that’s still not finished, despite being started in 1968), and not only would our gentle publishers rebel at the thought, but your authors don’t have the talent to be Donald Knuth.^([^(115)](#fn115))

We also purposefully stayed away from prescriptive decisions like “what IDE to use,” preferring instead to focus on build tools that are IDE-neutral. You could write this book’s code with Notepad++, vim, emacs, IDEA, Eclipse, NetBeans…​ anything that can save text in a format that the Java compiler can read. We used IDEA in writing (including writing the chapter contents, not just the code), but we could have done with *any* workable tool, because we felt that telling our readers to prefer one over the other was not helpful and covering *every* option wasn’t workable either.

Imagine as you’re reading having to look at screenshot after screenshot of Eclipse running the tests…​ and then IDEA running the tests (as the display would be similar in intent but not the same)…​ and then NetBeans. It would have been too much to ask of you, our readers, to endure all of that, and it would have added very little to the value of the book.

We’ve striven to deliver as much value as we were able to, outside of the footnotes.^([^(116)](#fn116))

## Spring Initializr

A project you might consider is Spring Initializr, a web application found at [`https://start.spring.io/`](https://start.spring.io/). This is an application that generates a Spring project with a set of dependencies that you can literally select from a list.

Here’s what Initializr’s first page might look like, after you’ve selected a group id and Maven as your build system (much as we might have done for this book, although we didn’t).

![](images/471370_2_En_12_Chapter/471370_2_En_12_Fig1_HTML.jpg)

A screenshot of the Spring Initializr interface at start dot spring dot io. It depicts various project settings, including project type, Spring Boot version, and project metadata. No dependencies are selected. Generate, explore, and share buttons are at the bottom.

Figure 12-1

Initializr’s first page

Selecting dependencies will add everything those dependencies need such that your project is set up **properly** for those dependencies. For example, a typical application (as one might have found in Chapters [5](#471370_2_En_5_Chapter.xhtml) and [6](#471370_2_En_6_Chapter.xhtml)) might have Spring Data JPA, as well as Spring Web and Mustache, and Initializr allows you to simply include all of these as straightforward dependencies in a simple dropdown.

![](images/471370_2_En_12_Chapter/471370_2_En_12_Fig2_HTML.jpg)

A screenshot of the Spring Initializr interface at start dot spring dot i o. It depicts various project settings including project type, Spring Boot version, and project metadata. Dependencies section lists 3 options as follows. Spring web, web. Spring data J P A, S Q L. Mustache, template engines.

Figure 12-2

Selecting dependencies

After your dependencies are selected, you can download a full compilable project using the “Generate” button; open that project with whatever tool you choose, and you now have a workable platform upon which to build the Next Big Thing the world’s been looking for.

We didn’t use Initializr for this book, as we’ve already said, but we certainly could have. If you’re wondering what some of the offerings the Spring ecosystem might have, this is an excellent place to find many of them.

There’s more to the Spring ecosystem than what Initializr has, of course: GitHub ([`https://github.com`](https://github.com)) has many projects that solve different problems than Spring has attempted, or in different ways. If you’re looking for something that Spring’s developers haven’t addressed, there’s a good chance someone else has made a workable attempt at it.

### Alternative JVM Languages

This book uses Java, for the most part, with XML used for project configuration (thanks to Maven), and a tiny bit of a templating language commonly referred to as “Moustache.” However, there are *many* languages available on the JVM, and there’s no reason whatsoever to limit yourself to Java if one of those other languages suits you for whatever reason.

Spring even supports most of those languages with tweaks that tune them for Spring, so you can write idiomatic code in, say, Groovy or Kotlin and get every feature we’ve demonstrated with Java.

It’s outside the scope of this book to recommend a given language (outside of Java, of course), but both Groovy and Kotlin are first-class Spring citizens, and you can certainly leverage languages like Scala or Clojure as well. Spring works on *classes* in the JVM; it doesn’t care how those classes are created.

Each of these languages has strengths and weaknesses. Groovy, for example, brings a very terse syntax such that you can add a lot of functionality with very few lines of code, and as a scripting language it tends to be very expressive. Kotlin *feels* a lot like Java, but also tends to be very expressive in its syntax (as it was created to represent Java’s syntax tree rather than Java’s *syntax*). Interoperability between both of these languages and Java tends to be *very* high.

Languages you might want to consider in addition to Java, in some rough order of popularity:

1.  Kotlin ([`https://kotlinlang.org/`](https://kotlinlang.org/))

2.  Groovy ([`https://groovy-lang.org/`](https://groovy-lang.org/))

3.  Ruby ([`www.ruby-lang.org/`](http://www.ruby-lang.org/) and [`www.jruby.org/`](http://www.jruby.org/) on the JVM)

4.  Scala ([`www.scala-lang.org/`](http://www.scala-lang.org/))

5.  Clojure ([`https://clojure.org/`](https://clojure.org/))

## Spring WebMVC.fn

In Chapters [6](#471370_2_En_6_Chapter.xhtml), [7](#471370_2_En_7_Chapter.xhtml), and [10](#471370_2_En_10_Chapter.xhtml), we looked at using WebMVC, Spring’s project for creating HTTP endpoints. Functional Endpoints ([`https://docs.spring.io/spring-framework/reference/web/webmvc-functional.html`](https://docs.spring.io/spring-framework/reference/web/webmvc-functional.html)) were introduced in Spring 5 as Spring WebMVC.fn and fully integrated with Spring Web MVC in version 6.

The Functional Endpoints model provides a method of using functions to route and handle requests and designed for immutability. It provides a “Domain-Specific Language” approach from within Spring as an alternative to the annotation-based approach but still runs on the same `DispatcherServlet`.

A Domain-Specific Language – or DSL – is a language or subset of grammar specialized for a specific application domain.

Languages like Java are very generalized, but code written in Java can target specific domains quite easily; you can, for example, create an abstraction of a `Door` that understands how to `open` or `close` itself.

However, you’re still expressing the actions in Java. Opening and closing doors is a fundamentally simple process to model, but consider simulating electronic signals as an example; it’s easy to write Java code to express concrete electronics concepts, but to an engineer, the Java code would have all kinds of extra information that the engineer wouldn’t need and might not understand.

A DSL, on the other hand, is usually designed to fit the problem space it describes very tightly, and an engineer would ideally be able to read an “electronics DSL” without having to deal with very much of the underlying programming language being used.

Popular examples of DSLs for the JVM include Gradle (which uses DSLs for building projects in either Groovy or Kotlin), JavaFX, and Processing (see [`https://processing.org/`](https://processing.org/)). There are, of course, many, many more – including `WebMVC.fn` itself. But you knew that, because we started off by describing WebMVC.fn as a DSL!

Instead of mapping the endpoints in the `Component`, though, you’d create **another** bean, of type `RouterFunction<ServerResponse>`, which has a convenient fluent API for creating endpoints for handling `GET` requests, `POST` requests, filtering, and so forth.

Here is a sample code snippet.

```
@Bean
RouterFunction routes(PersonHandler ph) {
return route()
.GET("/people", ph::handleGetAllPeople)
.GET("/people/{id}", ph::handleGetPersonById)
.POST("/people", ph::handlePostPerson)
.filter((serverRequest, handlerFunction) -> {
try {
log.info("entering HandlerFilterFunction");
return handlerFunction.handle(serverRequest);
}
finally {
log.info("exiting HandlerFilterFunction");
}
})
.build();
}
Listing 12-1
WebMVC.fn route configuration example
```

It takes a little bit of getting used to, in part because Java isn’t quite as flexible for creating domain-specific languages as programming languages like Scala, Kotlin, or Groovy, so the DSL isn’t quite as clean as it might be – it’s going to have Java-isms like lambdas, method references, and concrete type declarations, as you can see from Listing [12-1](#471370_2_En_12_Chapter.xhtml#PC1).

However, WebMVC.fn does allow you to centralize the endpoint configuration. If you have 17 different classes that are serving as `Controller` objects today, WebMVC.fn would allow you to change them back to ordinary `Component` classes – and have one location for configuration of all of the endpoints, instead of forcing a programmer to chase down which of those seventeen classes actually provides a specific endpoint.

Is this a worthy goal? Honestly, yes (or, well, “maybe”); a good design will allow you to easily map between an endpoint and a specific `Controller`, but having the `Controller` objects handle their own endpoint construction means that they have a wider visibility to the grand design and deployment of the application than perhaps they should.

It’s a minor design decision, in the end (after all, Web MVC works now, and you can manually register endpoints just as WebMVC.fn does, just with more code and in idiomatic Java), but it’s still a neat idea. You can still accomplish similar things without WebMVC.fn (by registering the endpoints in a central location, but without WebMVC.fn’s DSL), but the DSL makes it far more convenient than it otherwise might be.

As usual, Spring provides flexibility such that you can design your application in nearly any fashion you would like.

## Spring Reactive

Reactive programming is easily summarized as “programming with asynchronous data streams.”^([^(117)](#fn117))

We’ve seen example code where streams were preferred to traditional loops and other such iterative mechanisms (in particular, in Chapters [3](#471370_2_En_3_Chapter.xhtml), [8](#471370_2_En_8_Chapter.xhtml), and [9](#471370_2_En_9_Chapter.xhtml)) – the primary difference between traditional streams and reactive streams is that reactive code tends to have asynchronous datasources, so there’s typically not an explicit end to the stream.

In a traditional streaming model, there’s a data collection phase – where you’re getting a list of `Artist` or `Song` instances – and when you have all of those instances, **then** you can process them. Retrieving the instances is a **blocking operation** and represents a place in your code that cannot progress until the blocking operation completes.

On the other hand, reactive programming is asynchronous, as we stated: we might indicate that we want to get a list of instances, and **when they are available**, do something with them. We don’t block execution of the code while waiting for the instances to come back from a data source; we set up something to handle the instances when they arrive, and exit.

In practice, this tends to yield some incredible performance gains, because a database call (for example) might mean a ten millisecond pause for a given thread; with reactive programming, there is no pause at all. The thread can be used to do other things instead.

The cost, of course, is that you have to write your code to use reactive models. We’ve been leaning that direction throughout the book, with the emphasis on lambdas and streams, but for many Java programmers this is still a somewhat new approach – even though it was introduced with Java 8, in 2014.

With Spring 5, Spring provided a `spring-webflux` module that serves as nearly a drop-in replacement for WebMVC.^([^(118)](#fn118)) Spring Web Reactive adapts the concepts in Web MVC and migrates them to a reactive model, including the option to specify inputs and outputs that don’t necessarily **have** to block on input or output; you could use this to provide a series of data points as they become available (like stock ticker prices) – both as input **and** output – simply by changing the types from `Artist`, for example, to `Flux<Artist>`.

If this sounds complex on first read, don’t worry – it isn’t very simple. However, if you can write code to use WebMVC, it’s fairly easy to migrate to Spring Web Reactive and add features and performance gains as you understand how to leverage the reactive model.

There’s even a `spring-boot-starter-webflux` to allow easy usage with Spring Boot – and if you’re going to work with Spring Reactive, this is probably the way you should go.

## Message Queues and Spring

A message queue is a form of asynchronous service-to-service communication.^([^(119)](#fn119)) In a queue architecture, there are three basic components:

*   A host application like RabbitMQ ([`www.rabbitmq.com`](http://www.rabbitmq.com)), ActiveMQ ([`https://activemq.apache.org/`](https://activemq.apache.org/)), or Artemis ([`https://activemq.apache.org/components/artemis/`](https://activemq.apache.org/components/artemis/), itself an ActiveMQ project)

*   A producer (something that sends messages to the host application)

*   A consumer (something that retrieves messages from the host application)

Note that a given service can be both a producer and a consumer and that this is actually quite common; also, a producer isn’t limited to one type of production, nor is it limited to one type of consumption.

There are two primary message models in queueing systems: publish/subscribe (or “pub/sub”) and point-to-point (or “PtP”).

The two models are different in how messages are delivered:

*   In a pub/sub model, a message is delivered immediately to every available consumer. This message stream is often referred to as being a “topic” (and this is indeed how the Java Message Service refers to pub/sub queues).

*   In a PtP model, a message is distributed to one and only one consumer, no matter how many consumers might be listening on a given message stream; the message stream is also called a “queue” to differentiate it from a topic. How the messages are assigned to consumers is up to the queue itself – but usually messages are distributed in a “round robin” algorithm, meaning that messages are **usually** evenly distributed among available listeners.

A chat room is a good example of a topic; a message from one person goes to every other person who happens to be in the room when the message is sent.

An asynchronous logging service is a good example of a queue; a producer generates a log message and sends it to the message service, where one of multiple possible listeners retrieves the message and decides what to do with it.

As referred to earlier, Java has a standard specification for working with message queues, called the Java Message Service (or “JMS”). JMS is like JDBC: the specification covers how the API is used, but not how the API works. You’d use a JMS library for each given message queue host; ActiveMQ has one, and RabbitMQ has one of its own. (For RabbitMQ, see [`www.rabbitmq.com/jms-client.html`](http://www.rabbitmq.com/jms-client.html) for how to access RabbitMQ over JMS.)

Spring also has a module for working with JMS, `spring-amqp`; in particular, it provides the `JmsTemplate` class, as well as providing annotations like `@JmsListener` to allow components to retrieve messages from a queue or topic with fairly little effort (see [`https://spring.io/guides/gs/messaging-jms/`](https://spring.io/guides/gs/messaging-jms/) for more details). You still have to configure the host connection, but after that, using a queue or topic is a matter of using the `JmsTemplate.send()` method (although the `JmsTemplate` class is actually **extraordinarily** flexible, and it’s quite possible you’d never use `send()` by itself, as there are other methods that are likely to fit an exact scenario more completely).

RabbitMQ actually deserves some special treatment here.

Most messaging providers use version 1.0 of the Advanced Message Queueing Protocol, or AMQP ([`www.amqp.org`](http://www.amqp.org)). RabbitMQ, however, by default uses AMQP 0.9, and the JMS client for RabbitMQ is designed to work with RabbitMQ’s message service.

You have options, though: you can always install AMQP 1.0 as a plugin for RabbitMQ, or you can use AMQP 0.9 directly from Spring, using a `RabbitTemplate` as provided by the `spring-rabbit` dependency, which would be a rough equivalent to the `JmsTemplate` as provided by the `spring-amqp` module.

Why would you use a message queue? They’re ideal for massively scaled and transactional asynchronous operations; consider bank transactions as an example. Normally, when you create charges against your bank account, the bank stores a set of operations (withdrawals and deposits) and posts them at a specific time; since message queues can be transactional, failures can be reprocessed until they’re successfully handled. In addition to making a complex architecture fairly simple to work with (as processes have simple inputs and outputs), the transactional nature of consumption in PtP scenarios makes the code **safe**.^([^(120)](#fn120))

## GraphQL

In Chapter [6](#471370_2_En_6_Chapter.xhtml), we mentioned REpresentational State Transfer, or REST, which is a common and popular way to work with data models over HTTP. REST is usually pretty simple; the hardest thing about REST is creating endpoints that make sense from an external API’s perspective. (Another thorny problem is how to handle versioning of endpoints, for when things change and yet you still need to support the “old way.”)

For example, do we access `Song` instances through their `Artist` reference, or by title? It makes more sense to use song titles, because a given song title might be used by multiple artists…​ but how, then, do you refer to songs created by a specific artist? We have answers that satisfy us for the sake of this book, of course, but in more complex models, questions like these can be less easy to answer.

Also, REST endpoints tend to be fairly coarse; if you request a `Song`, you’re typically going to get every attribute of that `Song`, whether the request needs it or not. You can obviously tune the output of a REST request such that it does not contain every attribute (as we do when we create the autocompletion services), but usually creating a projection^([^(121)](#fn121)) involves creating extra object classes to represent the projection and that ends up being a lot of work for most projects (even though projections can be far more efficient than returning large objects). It’s simpler just to return giant objects, even though that can impact processing speed and network transfer time.

Is returning “giant objects” a bad thing? Well…​ as usual, it depends. If the objects can be represented in serialized form in relatively little space, it probably doesn’t matter, especially if it can be represented in a few thousand bytes.

A network packet – or segment, actually – is usually sized around 1500 bytes, roughly 1400 of which are usable by applications; the remainder makes up headers and other metadata for the packet. If the serialized form of an object fits into one or two network packets, it’s **probably** not a big deal to not worry about the extra data being transmitted.

Of course, if you’re handling millions of such requests and your data takes up **two** network packets and you only need to transfer **some** of that data…​ maybe it’d be cost-effective to trim the results down such that they’re slimmer.

Or, of course, one could look into libraries like GraphQL.

GraphQL ([`https://graphql.org/`](https://graphql.org/)) is a query language and library designed to work with APIs such that clients can actually define what attributes are requested, and can also specify that a **graph** of the data is to be returned.

In our music gateway example, we might have two endpoints to get an `Artist` and every `Song` for that artist, if we wanted all of the related data. With GraphQL, we could build a request that requested both sets of data (the `Artist` and related `Song` objects), possibly only including specific attributes from both object types instead of **everything**.

Since it’s done with a **query language**, the API designer doesn’t even have to anticipate ahead of time what fields should be included, or what the resulting graph should look like. (The consumer of the API, of course, has to be aware of what is being returned; the **consumer** has to be aware of the data, but that’s why the consumer specifies the nature of the data in the first place.)

The programmer simply defines what fields and objects **might** be available, and then uses the GraphQL library to handle the request’s inputs and outputs.

Since the query language is actually separated from the object model, it’s trivial to add features to the endpoints without having to change client code; the clients would request the data they need, and the presence of **additional** data in the API wouldn’t matter.

Does this mean, then, that programmers should toss out their musty and archaic REST endpoints in favor of GraphQL?

Probably not. GraphQL is, like many other things, a useful tool; it implies that clients have a certain responsibility in knowing what data is to be requested, and that’s not always the case. In fact, one developer known by the authors has run into multiple environments where GraphQL was used as “magic sauce” – only to rip it out and see benefits across the projects. That doesn’t mean that GraphQL is **bad** – it only means that GraphQL is a tool, and like most other tools, needs to be used in the appropriate environment and conditions. Under those conditions, GraphQL can be **very** useful; when those conditions aren’t present, it’s like the proverbial bicycle to a fish.

The Java library for GraphQL can be found at [`https://github.com/graphql-java/graphql-java;`](https://github.com/graphql-java/graphql-java%3B) there’s also a Spring Boot starter for GraphQL, at [`www.graphql-java-kickstart.com/spring-boot/`](http://www.graphql-java-kickstart.com/spring-boot/).

### Cloud Deployment

This book has focused on deploying applications in a Java virtual machine. However, one thing it hasn’t touched is where that virtual machine actually runs; in tests, you’re running on a local JVM, but the reality of modern development is that many companies run their applications on someone else’s hardware.

This creates enough complexity that *each remote provider* would justify its own book covering capabilities and concerns. It’s possible to abstract features from one to another (each one provides a way to run a virtual machine upon which you can install whatever services you’re willing to manage), but to really leverage Azure ([`https://azure.microsoft.com/`](https://azure.microsoft.com/)), AWS ([`https://aws.amazon.com/`](https://aws.amazon.com/)), or GCP ([`https://cloud.google.com/`](https://cloud.google.com/)), for example, you really would want to have those providers manage as much of your application as you can.

For example, you could set up your own message queuing system with Artemis or RabbitMQ…​ but AWS has the Simple Queue Service^([^(122)](#fn122)) (among others), GCP has PubSub,^([^(123)](#fn123)) and Azure has Azure Messaging.^([^(124)](#fn124))

Each one also provides different data storage, whether from Cloud-based relational databases like MySQL or Postgres, or other NoSQL- or SQL-related technologies like DynamoDB, MongoDB, or BigQuery.

The list of features is *very* broad: this is barely scratching the surface of what one might look for *first*.

Spring has a project, Spring Cloud ([`https://spring.io/projects/spring-cloud`](https://spring.io/projects/spring-cloud)), to help coordinate these services, whether in consumption or deployment. Most of the project is focused on application-level concerns (“How would I consume content, or coordinate services”), but it may also help address concerns like manageability or deployment.

### Performance Metrics

This book has been almost entirely focused on functional requirements, as we pointed out in the opening of this chapter: can a given service fulfill these tasks?

What we have not asked is: “can a given service fulfill these tasks under a given load?” – in other words, how fast, or how scalable, is a given service?

For us, there are two ways to measure scalability: “vertical” and “horizontal” scales. Different people will use different axes or terms, but the concepts travel well.

Vertical scalability is, for us, “how fast is a service?” If a given method – `methodA()` – takes 400 milliseconds to execute, and another method (`methodB()`) takes 750 to execute, the first method (`methodA()`) has better “vertical scalability” than the second.

Horizontal scalability is “how many of these calls can successfully execute at a given time?” If `methodA()` can only be invoked in *one thread* at a time, but `methodB()` can be invoked by four hundred threads at the same time, `methodB()` has much better horizontal scalability than `methodA()`.

These two axes work in concert with each other. Let’s say we have four threads that call each method.

The first thread calls `methodA()` – and takes 400ms to get a value back. The **second** thread’s call to `methodA()` *has to wait until the first thread’s call is finished* – so if they both start at the same time, the first thread will get a result in 400ms, but the *second* thread will get its result in *800ms*, the time it takes for the first thread to get its data, plus the time it takes to get the second thread’s result. The third thread will have an elapsed clock time of 1200ms, and the fourth thread will have an elapsed clock time of 1600ms, assuming nothing else interferes with the execution times.

Now, let’s run the same exercise with `methodB()`, which takes longer (750ms instead of 400ms), but now each thread can get its result concurrently – that is, the first, second, third, and fourth threads all get their response in 750 milliseconds.

The `methodB()` call is slower but has much better horizontal scalability; `methodA()` is faster but has terrible horizontal scalability.

We’ve tried to design code in this book such that horizontal scalability is preserved, but we’ve not tested it at all, largely because horizontal scalability is driven entirely by what kind of performance is required by an actual application. Does the application need to scale to dozens of users? Or millions? Code that’s designed for millions of simultaneous users would look *very* different from code that’s designed for ten users, and the testing harnesses would also be rather different.

Note

It’s fairly easy to find libraries that have tutorials that say, “Here’s how you’d build Twitter^([^(125)](#fn125)) with our technology,” and those tutorials can be very useful. But at the same time, Twitter’s performance requirements are not only *incredible*, but they’re incredibly difficult to *replicate*, and your authors know of very few tutorials that would hold up under Twitter’s actual deployment requirements, *in any way*. Very few tutorials address the horizontal scalability issues that big social media sites have to address.

Testing frameworks like TestNG ([`https://testng.org/`](https://testng.org/), used in this book) and JUnit5^([^(126)](#fn126)) can *help* test horizontal and vertical scalability, but you’d also at some point look at tools like Apache JMeter ([`https://jmeter.apache.org/`](https://jmeter.apache.org/)) to generate testing scenarios that stress horizontal capabilities.

### Other Developers

This book has been an attempt to have a conversation between your authors and you, largely one-sided because a book is a compilation of static information. That can be effective (books are how your authors learned first, too!), but it’s not the end of learning.

We would encourage you to find other developers, in whatever forums you find convenient, and *communicate with them*. Whether it’s a local Java users group ([`https://dev.java/community/jugs/`](https://dev.java/community/jugs/) is a good starting point), or StackExchange, Quora, IRC, Google Groups, Facebook, or…​ well, *anything*, really, developers are often interested in the art and skill of programming, and most are willing to teach (and learn). The community around Java is *by far* its most powerful tool. The amount of knowledge is incredible, and it’s easy to find motivated developers to contribute to whatever you’re doing.^([^(127)](#fn127))

Ask questions! Nobody is likely to blame you for not knowing something. (If they do, well, our apologies for their behavior in advance – not everyone behaves perfectly in every situation.)

Also, offer answers, even if you lack confidence – one can learn from an expert saying why your answer might not be the right one, too, and as we said early in this book, working code is better than non-working code, even if your answer might not be perfect.

Doing both of these things contributes to the sea of knowledge available for humanity – and that’s one of the highest callings that we can have.

## What’s Next?

We hope you’ve enjoyed learning more about Spring (and, occasionally, *more* than Spring). We’ve covered the basics of Spring, including why it was written and why it still exists, and how it still affects Java developers today. We’ve also covered using Spring to serve content over the Web, and how to interact with multiple data sources; we’ve also addressed how to identify users and limit what they can do based on their identities and roles. Lastly, we’ve started gently peeking beyond the Web, persistence, and security to consider the wider Spring ecosystem.

Now it’s your turn: develop fascinating applications with Spring! Change the world for the better, and tell the world what you’ve done and how you’ve done it. We’ll be watching and cheering you on.

Footnotes [1](#471370_2_En_12_Chapter.xhtml#Fn1_source)   [2](#471370_2_En_12_Chapter.xhtml#Fn2_source)   [3](#471370_2_En_12_Chapter.xhtml#Fn3_source)   [4](#471370_2_En_12_Chapter.xhtml#Fn4_source)   [5](#471370_2_En_12_Chapter.xhtml#Fn5_source)   [6](#471370_2_En_12_Chapter.xhtml#Fn6_source)   [7](#471370_2_En_12_Chapter.xhtml#Fn7_source)   [8](#471370_2_En_12_Chapter.xhtml#Fn8_source)   [9](#471370_2_En_12_Chapter.xhtml#Fn9_source)   [10](#471370_2_En_12_Chapter.xhtml#Fn10_source)   [11](#471370_2_En_12_Chapter.xhtml#Fn11_source)   [12](#471370_2_En_12_Chapter.xhtml#Fn12_source)   [13](#471370_2_En_12_Chapter.xhtml#Fn13_source)   [14](#471370_2_En_12_Chapter.xhtml#Fn14_source)   [15](#471370_2_En_12_Chapter.xhtml#Fn15_source)   [16](#471370_2_En_12_Chapter.xhtml#Fn16_source)  Index A Acegi Security System ActiveMQ Annotation-based web application Maven pom.xml Servlets, adding Spring context standalone working servlet ApplicationContext ApplicationContext getBean() method artistData() method @Autowired annotation @Autowired SongNameNormalizer B Band Gateway app Batch configuration CSV files features processing/listening Beans annotations, configuration @Autowired components constructor injection @Qualifier and Bean Names band gateway code annotations, configuration API API classes annotations, configuration build model Music Service API normalizer interface container Java config ApplicationContext @Autowired @Bean, declaring components Constructor Injection DataProvider @Qualifier sample application XML configuration declaring bean <property />, wiring components buildAnnotationContext() method build() method C Central Authentication Service (CAS) @Component annotation Context and Dependency Injection (CDI) @ContextConfiguration annotation D @DataProvider Dependency injection HelloWorldBean J2EE hello world JNDI dataset name services widget Dependency injection frameworks doGet() method E, F Enterprise Java Bean (EJB) @EntityScan annotation equals() method G getNonexistentId() method Gradle GraphQL cloud deployment developers performance metrics query language REST endpoints Groovy H handleArtistNotFound() HasData “Hello, World!” application building Maven Maven installation project programming languages Spring suffering-oriented programming testing I iterateOverModel() J, K, L Jakarta EE CDI container implementations Java EE modern web application design principles module structure annotation-based web application common module Gradle request/response interaction model Servlet API XML-based spring context application Java bean Java Enterprise Edition (“JavaEE”) Java Persistence API (JPA) Java Virtual Machine JdbcTemplate accessing data actual MusicRepository MusicRepository query() testing MusicRepository @Transactional annotations data access facilities data model entity model MusicService project setup REST endpoints ArtistController SongController SQL-first access model @JsonIgnore JSR-250 annotations annotated classes, constructors calling methods features scopes M Maven Message queue Microcontainers Model-View-Controller (MVC) architecture definition endpoint project directory Modulith application modules artist package documentation module-driven domain design Song and SongRepository Spring Boot application verification MongoDB musicServiceTests N NetBeans @NonNull Normalizer O oauth2Login() method Object/relational mapping (ORM) P @PathVariable annotation @PostConstruct @PostMethod @PreDestroy Q @Qualifier annotation Querydsl R RabbitMQ Reactive programming render() method REpresentational State Transfer (REST) @RequestMapping @RequestParam reset() method runMethod() method S saveArtist() method @Scope annotation Security authentication authorization custom directory structure Frank’s data Lambda-style DSL modules and the requirement project directory REST application @Bean classes create @RestController directory structure integrate OAuth JPA test TestRestTemplate class testing service() method Servlet API Single Access Method Spring EJB J2EE Java EE Rod/Juergen setting stage Spring 6 modern java Spring Boot ArtistService ArtistService/Little Controller, implementation checking foundation building application building transport object configuration “Hello” static content testing database connections definition handling exceptions initializing data microcontainers setting up project spring-webmvc TestArtistController class Spring Data architecture functioning schema JPA project structure, create BaseArtistRepository BaseEntity interface BaseMusicService BaseSong build script component creating directory, POSIX CrudRepository data design grammar JPA project MongoDB test project WildcardConverter Spring Initializr dependencies JVM language Spring lifecycle calling constructor calling methods dependency injection frameworks Java configuration listeners scope Spring Tool Suite Spring Web SQL-first access model SQL LOWER() function T TestNG testSongVoting() method Threadbare Loaf Traditional J2EE approach @Transaction annotation transform() method U, V userDetailsService() method W, X, Y, Z Web archives WebMVC