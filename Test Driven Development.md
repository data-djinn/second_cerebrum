[[Unit Testing in Python]]

**before we code, we should reason about our code; we should think about why we're going to write it and how**
- the goal is:
	- *specification*, not validation
		- think through your requirements or design before writing your functional code
	- **write clean code that works**
![[Pasted image 20220526090605.png]]

### High-level steps for test-first development (TFD)
1. quickly add just enough code to fail
2. run your tests (often the entire test suite, sometimes a subset), to ensure your test does in fact fail
3. update functional code to make it pass the new tests
4. run tests agan -> pass
5. repeat

##### TDD = Refactoring + TFD

- when implementing a new feature, the first question is whether the existing design is the best possible design that enables you to implement that functionality
	- if so, use TFD
	- if not, refactor it locally to change the portion of the design affected by the new feature, enabling you to add that feature as easily as possible
	- as a result, you will always be improving the quality of your design, thereby making it easier to work with in the future
- instead of writing functional code first, and testing code as an afterthought, you **write your test code before your functional code**
	- do so in **very small steps** - one test and a small bit of functional code at a time

##### 2 levels of TDD:
1. **Acceptance TDD**: you write a single "acceptance test", or behavioral specification, then use just enough production functionality/code to fulfill that test
	- goal of ATDD is to specify detailed, executable requirements for your solution on a "Just In Time" basis. 
	- also called Behavior Driven Development
2. **Developer TDD**: you write a single developer test (unit test), and then just enough production code to fulfill that test
	- goal is to specify a detailed, executable design for your solution on a JIT basis
![[Pasted image 20220526092439.png]]

## 2 rules for TDD:
1. write new business code only when an automated test has failed
2. eliminate any duplication that you find

#### individual & group behavioral benefits:
- you develop organically, with the running code providing feedback between decisions
- you write your own tests
- your development env must provide rapid response to small changes
- your designs must consist of highly cohesive, loosely coupled components (e.g. your design is highly normalized) to make testing easier (this also makes evolution and maintenance of your system easier too)

Good unit tests:
- run fast (short setups, run times, and break downs)
- run in isolation (you should be able to reorder them)
- use data that makes them easy to read and to understand
- use real data (e.g. copies of production data)
- represent one step towards your overall goal


- when a test fails, you have made progress because you now know that you need to resolve the problem
	- more importantly, you have a clear measure of success when the test passes
- **the greater the risk profile of the system, the more thorough your tests need to be**
	- you aren't striving for perfection - instead, you are testing to the importance of the system
	- "test with a purpose" - know why you are testing something and to what level it needs to be tested
- side effect of TDD is that you achieve 100% code coverage - every single line of code is tested

*if it's worth building, it's worth testing*

## TDD + Documentation
- most programmers don't read the written documentation for a system - they prefer to work with the code
- well-written unit tests provide a working specification of your functional code
	- as a result, unit tests effectively become a significant portion of your technical documentation
	- similarly, acceptance tests define exactly what your stakeholders expect of your system, therefo they specify your essential, critical requirements

# Why TDD?
- forces you to take small steps when writing software - far more productive than writing in large steps
- **the act of writing a unit test is more an act of design than of verification. It is also more an act of *documentation* than of verification. The act of writing a unit test closes a remarkable number of feedback loops in the agile process**

# Myths & Misconceptions
Myth: 
	you create a 100% regression test suite
Reality:
	
Myth: 
	
Reality:
	
Myth: 
	
Reality:
	
Myth: 
	
Reality:
	
Myth: 
	
Reality:

![[Pasted image 20220526095024.png]]