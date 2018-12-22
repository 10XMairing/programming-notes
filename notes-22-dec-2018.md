# 22-dec-2018

### 7 - number to binary through division

```python
def divideBy2(decNumber):
    remstack = []

    while decNumber > 0:
        rem = decNumber % 2
        remstack.append(rem)
        decNumber = decNumber // 2

    binString = ""
    while not len(remstack)==0:
        binString = binString + str(remstack.pop())

    return binString

print(divideBy2(42))

```

### 6 - Checking paranthesis

```python
def parChecker(symbolString):
    s = []
    balanced = True
    index = 0
    while index < len(symbolString) and balanced:
        symbol = symbolString[index]
        if symbol == "(":
            s.append(symbol)
        else:
            if len(s) == 0:
                balanced = False
            else:
                s.pop()

        index = index + 1

    if balanced and len(s)==0:
        return True
    else:
        return False

print(parChecker('((()))'))
print(parChecker('(()'))
```

for multiple kinds of brackets:
```python

def parChecker(symbolString):
    s = []
    balanced = True
    index = 0
    while index < len(symbolString) and balanced:
        symbol = symbolString[index]
        if symbol in "([{":
            s.append(symbol)
        else:
            if len(s)==0:
                balanced = False
            else:
                top = s.pop()
                if not matches(top,symbol):
                       balanced = False
        index = index + 1
    if balanced and len(s)==0:
        return True
    else:
        return False

def matches(open,close):
    opens = "([{"
    closers = ")]}"
    return opens.index(open) == closers.index(close)


print(parChecker('{{([][])}()}'))
print(parChecker('[{()]'))
```

### 5 - Timing a python standard type operations

- http://interactivepython.org/courselib/static/pythonds/AlgorithmAnalysis/Lists.html

```python
def test1():
    l = []
    for i in range(1000):
        l = l + [i]

def test2():
    l = []
    for i in range(1000):
        l.append(i)

def test3():
    l = [i for i in range(1000)]

def test4():
    l = list(range(1000))

t1 = Timer("test1()", "from __main__ import test1")
print("concat ",t1.timeit(number=1000), "milliseconds")
t2 = Timer("test2()", "from __main__ import test2")
print("append ",t2.timeit(number=1000), "milliseconds")
t3 = Timer("test3()", "from __main__ import test3")
print("comprehension ",t3.timeit(number=1000), "milliseconds")
t4 = Timer("test4()", "from __main__ import test4")
print("list range ",t4.timeit(number=1000), "milliseconds")

```
output:
```
concat  6.54352807999 milliseconds
append  0.306292057037 milliseconds
comprehension  0.147661924362 milliseconds
list range  0.0655000209808 milliseconds
```


```python
popzero = timeit.Timer("x.pop(0)",
                       "from __main__ import x")
popend = timeit.Timer("x.pop()",
                      "from __main__ import x")

x = list(range(2000000))
popzero.timeit(number=1000)
#4.8213560581207275

x = list(range(2000000))
popend.timeit(number=1000)
#0.0003161430358886719
```

```
popzero = Timer("x.pop(0)",
                "from __main__ import x")
popend = Timer("x.pop()",
               "from __main__ import x")
print("pop(0)   pop()")
for i in range(1000000,100000001,1000000):
    x = list(range(i))
    pt = popend.timeit(number=1000)
    x = list(range(i))
    pz = popzero.timeit(number=1000)
    print("%15.5f, %15.5f" %(pz,pt))
```

dict vs list contains
```python
import timeit
import random

for i in range(10000,1000001,20000):
    t = timeit.Timer("random.randrange(%d) in x"%i,
                     "from __main__ import random,x")
    x = list(range(i))
    lst_time = t.timeit(number=1000)
    x = {j:None for j in range(i)}
    d_time = t.timeit(number=1000)
    print("%d,%10.3f,%10.3f" % (i, lst_time, d_time))
```

### 4 - logic gates and Fraction class

- http://interactivepython.org/courselib/static/pythonds/Introduction/Summary.html
- Computer science is the study of problem solving.
- Computer science uses abstraction as a tool for representing both processes and data.
- Abstract data types allow programmers to manage the complexity of a problem domain by hiding the details of the data.

```python
def gcd(m,n):
    while m%n != 0:
        oldm = m
        oldn = n

        m = oldn
        n = oldm%oldn
    return n

class Fraction:
     def __init__(self,top,bottom):
         self.num = top
         self.den = bottom

     def __str__(self):
         return str(self.num)+"/"+str(self.den)

     def show(self):
         print(self.num,"/",self.den)

     def __add__(self,otherfraction):
         newnum = self.num*otherfraction.den + \
                      self.den*otherfraction.num
         newden = self.den * otherfraction.den
         common = gcd(newnum,newden)
         return Fraction(newnum//common,newden//common)

     def __eq__(self, other):
         firstnum = self.num * other.den
         secondnum = other.num * self.den

         return firstnum == secondnum

x = Fraction(1,2)
y = Fraction(2,3)
print(x+y)
print(x == y)
```


```python
class LogicGate:

    def __init__(self,n):
        self.name = n
        self.output = None

    def getName(self):
        return self.name

    def getOutput(self):
        self.output = self.performGateLogic()
        return self.output


class BinaryGate(LogicGate):

    def __init__(self,n):
        LogicGate.__init__(self,n)

        self.pinA = None
        self.pinB = None

    def getPinA(self):
        if self.pinA == None:
            return int(input("Enter Pin A input for gate "+self.getName()+"-->"))
        else:
            return self.pinA.getFrom().getOutput()

    def getPinB(self):
        if self.pinB == None:
            return int(input("Enter Pin B input for gate "+self.getName()+"-->"))
        else:
            return self.pinB.getFrom().getOutput()

    def setNextPin(self,source):
        if self.pinA == None:
            self.pinA = source
        else:
            if self.pinB == None:
                self.pinB = source
            else:
                print("Cannot Connect: NO EMPTY PINS on this gate")


class AndGate(BinaryGate):

    def __init__(self,n):
        BinaryGate.__init__(self,n)

    def performGateLogic(self):

        a = self.getPinA()
        b = self.getPinB()
        if a==1 and b==1:
            return 1
        else:
            return 0

class OrGate(BinaryGate):

    def __init__(self,n):
        BinaryGate.__init__(self,n)

    def performGateLogic(self):

        a = self.getPinA()
        b = self.getPinB()
        if a ==1 or b==1:
            return 1
        else:
            return 0

class UnaryGate(LogicGate):

    def __init__(self,n):
        LogicGate.__init__(self,n)

        self.pin = None

    def getPin(self):
        if self.pin == None:
            return int(input("Enter Pin input for gate "+self.getName()+"-->"))
        else:
            return self.pin.getFrom().getOutput()

    def setNextPin(self,source):
        if self.pin == None:
            self.pin = source
        else:
            print("Cannot Connect: NO EMPTY PINS on this gate")


class NotGate(UnaryGate):

    def __init__(self,n):
        UnaryGate.__init__(self,n)

    def performGateLogic(self):
        if self.getPin():
            return 0
        else:
            return 1


class Connector:

    def __init__(self, fgate, tgate):
        self.fromgate = fgate
        self.togate = tgate

        tgate.setNextPin(self)

    def getFrom(self):
        return self.fromgate

    def getTo(self):
        return self.togate


def main():
   g1 = AndGate("G1")
   g2 = AndGate("G2")
   g3 = OrGate("G3")
   g4 = NotGate("G4")
   c1 = Connector(g1,g3)
   c2 = Connector(g2,g3)
   c3 = Connector(g3,g4)
   print(g4.getOutput())

main()
```

### 3 - subset operations

```python
#Asks whether all elements of the first set are in the second

a = {1,2,3}
b = {1,2,3,4,5,6}
print( a<=b)
```

### 2 - center, ljust, rjust the string

```python

In [13]: "asdasdsad".center(20)
Out[13]: '     asdasdsad      '

In [15]: "asdasdsad".ljust(20)
Out[15]: 'asdasdsad           '

In [16]: "asdasdsad".rjust(20)
Out[16]: '           asdasdsad'

```

### 1 - list 

- http://effbot.org/zone/python-list.htm
- The list object consists of two internal parts; one object header, and one separately allocated array of object references. The latter is reallocated as necessary. 
- The list object stores pointers to objects, not the actual objects themselves. The size of a list in memory depends on the number of objects in the list, not the size of the objects.
- The time needed to get or set an individual item is constant, no matter what the size of the list is (also known as “O(1)” behaviour).
- The time needed to append an item to the list is “amortized constant”; whenever the list needs to allocate more memory, it allocates room for a few items more than it actually needs, to avoid having to reallocate on each call (this assumes that the memory allocator is fast; for huge lists, the allocation overhead may push the behaviour towards O(n*n)).
- The time needed to insert an item depends on the size of the list, or more exactly, how many items that are to the right of the inserted item (O(n)). In other words, inserting items at the end is fast, but inserting items at the beginning can be relatively slow, if the list is large.
- The time needed to remove an item is about the same as the time needed to insert an item at the same location; removing items at the end is fast, removing items at the beginning is slow.
- The time needed to reverse a list is proportional to the list size (O(n)).
- The time needed to sort a list varies; the worst case is O(n log n), but typical cases are often a lot better than that. 


```python
In [9]: def findall(L , value, start = 0 ):
   ...:     i = start - 1
   ...:     while 1:
   ...:         try:
   ...:             i = L.index (value , i + 1 )
   ...:             yield i
   ...:         except ValueError:
   ...:             raise StopIteration
   ...:         
   ...:         

In [10]: for index in findall([1,1,1,1,2,2,2,], 2):
    ...:     print "match at" , index
    ...:     
match at 4
match at 5
match at 6

In [11]: 
```

```python
#printing lists
print ", ".join(map(str, L))

sys.stdout.writelines(L) # if all items are strings
```

```python

#min and max can also take a mapping function
lo = min(L, key=int)
hi = max(L, key=int)

#sorting
def compare(a, b):
     return cmp(int(a), int(b)) # compare as integers

L.sort(compare)

def compare_columns(a, b):
     # sort on ascending index 0, descending index 2
     return cmp(a[0], b[0]) or cmp(b[2], a[2])

out = sorted(L, compare_columns)

#creates a key array where all elements are mapped to int, and uses that for sorting
L.sort(key=int)

out = sorted(L, key=int)
```

```python
# just to remind me, last item is not included, but first item is
list(range(10,1,-1))
[10, 9, 8, 7, 6, 5, 4, 3, 2]
```