# Constructors, Destructors, and Assignment Operatiors

## Know what functions C++ silently writes and calls

1. default constructor
2. copy constructor
3. destructor
4. copy assignment operator

## Explicitly disallow the use of compiler generated functions you do not want

The following example has three ways to avoid copy and assign for a class.

```
// chapter02.cpp : Defines the entry point for the console application.
//

#include "stdafx.h"

class Uncopyable {
protected:
	Uncopyable() {}
	~Uncopyable() {}
private:
	Uncopyable(const Uncopyable&);
	Uncopyable& operator=(const Uncopyable&);
};

class NoCopy : public Uncopyable {
public:
	explicit NoCopy(int a):val(a){
	}
	~NoCopy(){}
private:
	int val;
};

template <class T>
class UnCopyAbleTpl {
protected:
	UnCopyAbleTpl() {}
	~UnCopyAbleTpl() {}
private:
	UnCopyAbleTpl(const UnCopyAbleTpl&);
	UnCopyAbleTpl& operator=(const UnCopyAbleTpl&);
};

class NoCopyA : public UnCopyAbleTpl<NoCopyA> {
private:
	int val;
};

class NoCopyB : public UnCopyAbleTpl<NoCopyB> {
private:
	int val;
};

#define DISALLOW_COPY_AND_ASSIGN(TypeName) \
	TypeName(const TypeName&);		\
	void operator=(const TypeName&)


class NoCopyC{
public:
	int val;
	NoCopyC(int a=0) :val(a){

	}
private:
	DISALLOW_COPY_AND_ASSIGN(NoCopyC);
};

int main()
{
	NoCopy a(100);
	//error C2280: 'NoCopy::NoCopy(const NoCopy &)': attempting to reference a deleted function
	//NoCopy b(a);

	//error C2280: 'NoCopy::NoCopy(const NoCopy &)': attempting to reference a deleted function
	//NoCopy b = a;

	NoCopyA aA;
	//error C2280: 'NoCopyA::NoCopyA(const NoCopyA &)': attempting to reference a deleted function
	//NoCopyA bA(aA);

	NoCopyC aC;
	// error C2248: 'NoCopyC::NoCopyC': cannot access private member declared in class 'NoCopyC'
	//NoCopyC bC(aC);

	return 0;
}
```

## Declare destructors virtual in polymorphic base classes.

```
class TimeKeeper {
public:
	TimeKeeper() {
	};
	virtual ~TimeKeeper() {
		std::cout << "Call Destructor in TimeKeeper" << std::endl;
	};
};



class AtomicClock : public TimeKeeper {
public:
	AtomicClock() {};
	virtual ~AtomicClock() {
		std::cout << "Call Destructor in AtomicClock" << std::endl;
	};
};

TimeKeeper* getKeeper() {
	return static_cast<TimeKeeper*>(new AtomicClock());
}

TimeKeeper* keeper = getKeeper();
delete keeper;

// The output will be
//
// Call Destructor in AtomicClock
// Call Destructor in TimeKeeper

```

## Prevent exceptions from leaving destructors

Destructors shold never emit exceptions.
The destructor should catch any exceptions then
1. swallow them
2. terminate the program.

It is better to provide a regular function that be able to react to exceptions. 

