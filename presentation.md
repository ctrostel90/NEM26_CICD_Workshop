---
title: Software CICD Presentation
theme:
    name: tokyonight-night
---


# Goals

- Understanding what is CI/CD?
- What CI/CD tools does Beckhoff have?
- What is a Unit Test?
- What is TF1040 and basic usage
- How to write testable code

<!-- end_slide -->

# Agenda
<!-- alignment: 'right'-->
### Intro
- What is CICD
- Beckhoff Products and Advantages
<!--pause-->
### Testing Introduction
- What is Unit Testing
- Unit Testing vs Integration Testing
- Hands on exercises

<!--pause-->
### Unit Testable Code
- Techniques on how to create it
- Pitfalls
- Examples and Hands on Exercises

<!--pause-->
### Customer Strategies
- Approaching conversation with customers
- Adding unit testing to existing code bases

<!--pause-->
### Test Driven Development
- What is it?
- Pros/Cons
- Hands On exercises

<!-- end_slide -->

# Pre-Requisites

Everyone should've already installed

- TF1040 from the expirmental feed
- cloned the following repository from Beckhoff Community github
<TODO: Insert link>

<!-- end_slide -->

# CICD Presentation

To Powerpoint!
![](images/twincat_presentation.png)
<!-- end_slide -->

# Unit Tests

What is a unit test?

<!-- pause -->
It's an assertion

<!-- pause -->
```
Take a set of known inputs, if they go through a function, does the output equal what is expected?
```


```
ActualOutput = DoSomething(Inputs)
AssertEqual(ActualOutput,ExpectedOuput)
```

<!-- pause -->
With that, let's make one.

<!-- pause -->
To TwinCat!
<!-- end_slide -->
# Making a UnitTest

We'll start with the most common basic exmaple, some simple math functions.

<!-- pause -->
Create a new FB, name it adder. Add two inputs and an output

```pascal
FUNCTION_BLOCK Adder
VAR_INPUT
    Input1 : LREAL;
    Input2 : LREAL;
END_VAR
VAR_OUTPUT
    Output : LREAL;
END_VAR
VAR
END_VAR
```

```
Output := Input1 + Input2;
```

<!-- end_slide -->

In our Main let's instantiate that adder, and make a little test.

```
VAR
    TestAdder : Adder;
    runTest : BOOL;
    TestPass : BOOL;
END_VAR

```
```
IF runTest THEN
	runTest := FALSE;
	testAdder(Input1 := 1, Input2 := 2);
	TestPass := testAdder.Output = 3;
END_IF
```

Now we have a simple test! We can change the Adder's functionality and see the result of our test. 

<!-- end_slide -->

Let's expand and make another function block, a Divider.

```
FUNCTION_BLOCK Divider
VAR_INPUT
    Input1 : LREAL;
    Input2 : LREAL;
END_VAR
VAR_OUTPUT
    Output : LREAL;
END_VAR
```
```
Output := Input1 / Input2;
```

Add our first test to MAIN.

```
TestDivider : Divider;
runDividerTest : BOOL;
DividerTestPass : BOOL;

IF runDividerTest THEN
    runDividerTest := FALSE;
    TestDivider(Input1 := 4, Input2 := 2);
    DividerTestPass := TestDivider.Output = 2;
END_IF
```

<!-- end_slide -->

Now we have a divider and a test.

But we have a divide by 0 potential. So let's make a test that will catch that and then implement the fix to make it pass.

In Main we'll add our test variables and test code

```
runDividerTest2 : BOOL;
DividerTestPass2 : BOOL;

IF runDividerTest2 THEN
    runDividerTest2 := FALSE;
    TestDivider(Input1 := 1, Input2 := 0);
    DividerTestPass2 := TestDivider.Output = 0;
END_IF
```

<!-- pause -->

Inside our Divider we'll check for Input2 to be 0.

```
IF Input2 = 0 THEN
    Output := 0;
    RETURN;
END_IF

Ouput := Input1 / Input2;
```

<!-- end_slide -->
# Exercise End

Now we have 2 functions with a few test cases. 
<!-- pause-->
There are still plenty of more test cases we could want to ensure our function blocks could handle. Negative numbers, decimals etc etc.

<!-- pause-->

But this setup is cumbersome and really difficult for us to manage, we have to add all these extra boiler plate variables etc etc.

<!-- end_slide -->

# Test Frameworks

Enter the Unit Testing Framework. This is what the framework provides. A set of tools to aide in writting and running unit tests.

There are lots of options you've likely heard about in our space of automation and TwinCat. Some examples: TcUnit, TestSuite, CoUnit, Codesys Test Manager to name a few.

But, we now have a framework!

<!-- end_slide -->
# TF1040

We're not going to deep dive TF1040, but we'll use it moving forward in this workshop.

To Powerpoint!

<!-- end_slide -->
# Refactor to use TF1040

Let's refactor to use TF1040 instead.

<!-- pause -->

Add in the Tc3_PlcTestFramework to the project.

Create a new FB named AdderTests. Use the following code as the header/declaration.

```
{ attribute 'Name':='Adder-Test' }
{ attribute 'Timeout':='t#5s' }
{ attribute 'Owner':='TestOwner' }
FUNCTION_BLOCK AdderTests EXTENDS FB_TestCaseBase
VAR
    AdderTest : Adder;
END_VAR

AdderTest(Input1 := 2, Input2 := 2);
AssertEqual(expected := 4, actual := AdderTest.Output);
Succeeded();
```

<!-- pause -->
Create a new FB named DividerTests and add the following declaration and code snippet.

```
{ attribute 'Name':='Divider-Test' }
{ attribute 'Timeout':='t#5s' }
{ attribute 'Owner':='TestOwner' }
FUNCTION_BLOCK DividerTests EXTENDS FB_TestCaseBase
VAR
    DividerTest : Divider;
END_VAR

DividerTest(Input1 := 2, Input2 := 2);
AssertEqual(expected := 1, actual := AdderTest.Output);

DividerTest(Input1 := 2, Input2 := 0);
AssertEqual(expected := 1, actual := AdderTest.Output, msg := "Division by 0 not protected");
Succeeded();
```

Finally, in our MAIN we can remove all our pre-existing code and add the necessary pieces for the testing framework.

```
VAR
    TestAdder : AdderTests;
    TestDivider : DividerTests;
END_VAR

Tc3_PlcTestFramework.TestController.TestCtrl(); 
```
After activating you can use the Test UI client to run our tests and see the results!

<!-- end_slide -->

# Testable Code

To help give some strategies on how to write some testable code, we need to take a bit of a detour.

We're going to set the stage here by imaging we're all teachers. You all know how to teach. But you all teach different topics. You teach math, you teach history, you teach literature. Now, I'm the student that is wanting to learn about all these topics. I don't know anything about how to teach or the topics we're learning. But I know that you all, have the ability to teach. So I can go to every single one of you and ask "Teach me".

So we can imagine that we have a

```
FUNCTION_BLOCK HistoryTeacher
METHOD Teach
StudentKnowledge := StudentKnowledge + "History";
```

```
FUNCTION_BLOCK MathTeacher
METHOD Teach
StudentKnowledge := StudentKnowledge + "Math";
```
```

FUNCTION_BLOCK ScienceTeacher
METHOD Teach
StudentKnowledge := StudentKnowledge + "Science";
```

Because you all know how to teach, any student can interact with you in the same manner. They can come and be taught. They know nothing about how the teaching is going to be done, only that there will be teaching done. This abstraction concept is a very important topic that we'll utilize heavily when we want to create testable code.

# Continuing the Example
Let's continue our example in some more code.

We first create a Teacher FB that has a local variable assocaited to it:

```
FUNCTION_BLOCK Teacher
VAR
	_TeachTopic : E_TeachingTopic;
END_VAR
```

The Teaching topic enumeration we create to have our 3 example topics from above.

```
TYPE E_TeachingTopic :
(
	History,
	Math,
	Literature
);
END_TYPE
```

Then for the Teacher Function block, we'll add a "TeachTopic" property that will assign the local _TeachTopic with a Getter/Setter function. 

Additionally, we'll create a `Teach` method that takes a "Student's knowledge" (represented as just a string here) that the Teacher will add their knowledge to it.

We can solve it with a simple case statement. Something like this:

```
METHOD Teach : STRING
VAR_INPUT
	Student : STRING;
END_VAR
CASE _TeachTopic OF
	E_TeachingTopic.History:
		CONCAT(Student, 'Learned History');

	E_TeachingTopic.Literature:
		CONCAT(Student, 'Learned Literature');

	E_TeachingTopic.Math:
		CONCAT(Student, 'Learned Math');
END_CASE
Teach := Student;
```

But what if say, we start to add more specifics to the topic being taught. We want to have a Calculus teacher. But in order to teach calculus they need to make sure that they already have learned algebra. Etc etc. The complexity of this one function starts to increase quite a bit. And most importantly anytime you create a new Teacher instance, they all have this extra code in every single one. Even if they're just a Literature teacher.

Instead, we can utilize the programming implementation of the first example of abstraction I just gave - interfaces

We'll start by adding two new Interfaces to our project named `I_Teacher` and `I_SubjectMatter`.

For the `I_Teacher` we'll add one Method Teach:

```
Teach : STRING
VAR_INPUT
    StudentKnowledge : STRING;
END_VAR
```

We'll also add a property that is called SubjectMatter of type `I_SubjectMatter`

For the `I_SubjectMatter`, we'll add a method named `GetKnowledge` that returns type `STRING`.

We'll now make a new Teacher object named TeacherOOP that implements `I_Teacher`. This teacher we'll define as follows:

```
FUNCTION_BLOCK TeacherOOP IMPLEMENTS I_Teacher
VAR
	_SubjectMatter : I_SubjectMatter;
END_VAR
```

On our TeacherOOP object, in the getter/setter for the SubjectMatter property assign/return the `_SubjectMatter` variable

```
GET:
    SubjectMatter := _SubjectMatter;
SET:
    _SubjectMatter := SubjectMatter;
```

Next step is to add an implementation to the `Teach` method on the Teacher. For that we'll do the following:

```
Teach : STRING
VAR_INPUT
    StudentKnowledge : STRING;
END_VAR

CONCAT(StudentKnowledge, _SubjectMatter.GetKnowledge());
Teach := StudentKnowledge;
```

The final step is for us to create our Subject Matters. We'll make three quickly.

```
CalculusSubject IMPLEMENTS I_SubjectMatter

GetKnowledge() : STRING

GetKnowledge := 'Calculus';
```
```
EnglishLiteratureSubject IMPLEMENTS I_SubjectMatter

GetKnowledge() : STRING

GetKnowledge := 'English Literature';
```


```
ScientificMethod IMPLEMENTS I_SubjectMatter

GetKnowledge() : STRING

GetKnowledge := 'Scientific Method';
```

To use these in a program now we could do the following

```
MAIN
VAR
    TeacherOne : Teacher;

    //Subjects
    Calculus : CalculusSubject;
    EnglishLit : EnglishLiteratureSubject;

    StudentOne : STRING;
    ClassSubject : E_TeachingTopic;
END_VAR

CASE ClassSubject OF
    E_TeachingTopic.Calculus:
        TeacherOne.SubjectMatter := Calculus;
    E_TeachingTopic.EnglishLiterature:
        TeacherOne.SubjectMatter := EnglishLit;
END_CASE
IF ClassInSession THEN
    StudentOne := TeacherOne.Teach(StudentOne);
    CalcClassInSession := FALSE;
END_IF
```


The benefit here is we've seperated out the topic of how to teach from the person teaching. We taken the responsibility of the 'objects' and seperated them out into smaller code sections. 

Why this tangent in a class about unit testing?

Because this is a methodology on how to write testable code. The teacher in order to teach, is dependant upon the topic. If the code for teaching that topic lies in the teacher, then we have to setup/control the that code anytime we want to test anything about the teacher. Seperated out, now we can test the two things independantly. We can test "Does the teacher actually teach when we tell them to teach?" not caring about the topic/methodology of teaching so much as their ability to teach. And we can test the subject matter for "Does the subject matter actually provide the correct information on the topic when taught?"

The final piece we'll talk about here is that we have our teacher that by default, doesn't know how to teach anything. So if a new teacher is made and a topic not assigned, we'll see a page fault if someone asks them to teach. Here we can make a "default behavior" so that if a teacher doesn't know a topic, they still can teach something.

So what does every teacher that can't teach teach?

```
FUNCTION_BLOCK GymSubject IMPLEMENTS I_SubjectMatter

GetKnowledge() : STRING

GetKnowledge := 'Gym class';

```

We can then assign that as an initial value in the Teacher function block.

```
FUNCTION_BLOCK Teacher
VAR
    _DefaultClass : GymSubject;
    _SubjectMatter : I_SubjectMatter := _DefaultClass;
END_VAR

```

What we've just implemented is the concept of dependency inversion and utilized the 'strategy pattern'. This is the "D" portion of the commonly referenced "SOLID" programming practices. The more of the SOLID practices that are followed, the easier code will likely become to unit test. 

# Anytime when writting a unit test

Is this test, going to be worth the effort and help me ensure my code is safer? Very often you will run into when it's not worth the effort, and that's okay!

# Considerations When Testing

It's important and our job to understand what to actually test.

For example: Do we need to test that MC_Power.Status goes true when the Execute boolean is true?
No, we want to test instead that our code behaves in the correct way when the output of Mc_Power.Status is True. Our code is responsible for preforming the application process, so we want to validate that. We can rely on the testing done by the MC2 library itself to validate it is actually enabling the axis.

<!-- end_slide -->
# Code Coverage

Concept of code coverage

```
IF Axis.Position > SafeZone THEN
    //We're safe to move, issue movement commands
ELSIF Axis.Error THEN
    //Do some specific error behavior
ELSE
    //We're not safe to move, don't allow movement commands
END_IF

```

Code coverage looks and says you should have a test that covers all possible paths of the code execution. Every if condition/fork needs to have a test associated to it.

This is a dangerous concept to operate off of. You'll waste a lot of time if you approach this way.

Instead we want to think about a behavior that we want to test. 

"I need to ensure that if my axis is not in the right position, we don't accidentally try to move other equipment"

```
ExpectedResult := FALSE;
Axis.Position := SafeZone - 1;
Module.TryDangerousAction();
ActualResult := Module.DangerousActionResult;

Test.Assert(ExpectedResult,ActualResult)
```

This helps simplify your tests and keeps them focused. Making it so that you don't create endless tests.

<!-- end_slide -->
# Mockups
We took a detour down OOP lane for a reason. Despite that we will try to decouple software components from each other, our software components will have dependancies. Those dependancies can't be avoided. Have an application that's driving a conveyor belt? We're dependant on there being an axis.

This means at times we're going to need to replace those dependancies with abstractions. Instead of depending on an actual axis control, we can create an abstraction axis, and swap out the code to use that. This is called a mockup, mockups with interfaces can aide us in the ability to make testable code.

It is also, very often where you have to decide if something is worth the effort.

<!-- end_slide -->
# Mockup Exercise

For this exercise, we'll use a sample application prepared ahead of time. This application is an application to do some work on a product. It has a few pieces:
- A sensor to tell when a product is present in the system
- An Axis Controlling a product's position
- A cylinder that is used to do work on a product
- When a product is present, the product is moved to position, the cylinder is extended and held for a time before being retracted and the Axis moved to remove the product

In the sample, there is a UnitTest already written. We want to test and validate that when we call the `Process.ProduceProduct()` method, the PartCount is incremented and the cylinder's position is retracted.

Running the test however, you can see the test time's out! The axis never enables because there is no connection to the hardware. What can we do??

Well, we can use a mockup axis and the 'Strategy Pattern' we learned about earlier!

In the folder from the repository, there is a `MockAxis` folder. Add it to the UnitTest project.

This is an example of how we can mock up the axis wrapper's behavior. We don't actually care when we're testing this process that the Axis is enabled and actually moves. If we're concerned that the AxisWrapper is working correctly, we can write a UnitTest specifically testing it's functionality. So in this case, the Mock mostly just fakes the control. When a MoveToPosition command is given, it sets an internal `_Position` variable to the commanded position. This is then returned with the `SetPosition` property that the `BasicProcess.ProduceProduct()` method utilizes.

Next step is to use the mock.

Inside the `SimpleProcess` declration, make a new MockAxis and assign it in the init section.

```
VAR
    _MockAxis : MockAxis;
END_VAR

IF NOT _Init THEN
    Process.ControlAxis := _MockAxis;
END_IF
```

Reactivate and run the test again. It should pass this time! This is an example of you can use Mockups and interfaces to decouple your code and make it testable.

Of course, as mentioned earlier, the effort here can balloon if you're not careful so it's important to think critically at the start of the application if you're considering unit testing and architect appropriately to enable unit testing!!

<!-- end_slide -->

# Customer Strategies
### Existing Code base, how can we add unit tests?
Find the low hanging fruit:
- Find the algorithims. 
- Find the math sections.
- Look for already decoupled code; a method that takes an input and sets some output only off those inputs

It can be really difficult to unit test sections of the application if the architecture isn't supporting it from the start as shown in the previous examples. In these cases where there wasn't a deliberate architecture implemented at the get go, the effort may exceed the benefit!

If there is still adamant interest, start looking for the smallest components. Look for sections that are part of a framework, start at the "bottom building blocks". An Axis wrapper, a recipe system etc.

### How do we get customer to take the first step into CI/CD practices
In general, I believe any customer will benefit from taking any step further into adopting more/another CI/CD practice. But it should be incremental. If a customer isn't using source control, it doesn't make sense to jump straight to deploying a build pipeline with automated deployment, static analysis and unit tests.

A good path is to understand what their desired outcomes are. 
- Are they interested in more unified code style? -> Static Analysis
- Are they interested in more stable code? -> Unit Testing, Source Control, 
- Managing versions in the field? -> Pipeline + Automation Interface

CI/CD is an a-la-carte system that the specific meal combination can be customized for the individual customer.
<!-- end_slide -->

## Test Driven Development
### What is it/What is it not?
TDD is a way of writing code that involves writing an automated unit-level test case that fails, then writing just enough code to make the test pass, then refactoring both the test code and the production code, then repeating with another new test case. It's defined as the idea of having a very small loop:
1. Write a test that will fail
2. Write the code to make that test pass
3. Go back to step 1

This approach if you follow it to the letter can seem incredibly slow. The idea is that you're constantly testing your code base and constantly building up regression tests. Personally I find this approach hard to be dilligent on and a little frustrating in our field with again, interacting with hardware. I think it's an admirable one to try and follow but again, practically speaking I know few teams that use this approach to a strict 'T' while developing software.

### How can we you use it in your workflow?
There are ways where this approach can be incredibly useful when applying it to development and one in particular i've found very useful. The most useful application i find applying the stricter TDD process is in the bug reporting/fixing workflow.

A bug is seen/reported on a software that you have unit testing on. There's a good chance when you see the bug, you have an idea on where to start looking, not always the case but often you'll have an idea. The first step however, is resist the temptation to go digging in the code and finding/fixing the bug. The first step is to write a unit test that shows the failure. Write a unit test that would pass if the behavior in question was working correctly. Then, once you have that, go start digging in code and trying to fix things. Think you've fixed it? run your unit test. Does it fail still? nope, you've not fixed it, back to the drawing board. Repeat this process until the test passes. Run the rest of your unit tests as a regression and close out the issue as done.

### Example
### Exercises
Time to do an exercise with a Logging Helper library. This is a library made mostly for this class however it is a generally useful tool. The 

