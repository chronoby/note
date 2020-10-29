# C++ tips in《STL源码剖析》

## :: expression  

:: tells the compiler to call the operators defined in global namespace.  
It is the fully qualified name for the global new and delete operators.  

Note that one can replace the global new and delete operators as well as overload class-specific new and delete operators. So there can be two versions of new and delete operators in an program. The fully qualified name with the scope resolution operator tells the compiler you are referring to the global version of the operators and not the class-specific ones.

## Placement new

<https://stackoverflow.com/questions/222557/what-uses-are-there-for-placement-new>

## Memory fragmentation

<https://stackoverflow.com/questions/3770457/what-is-memory-fragmentation>

## C++ new-handler

See in *Effective C++*.

## auto_ptr

## partial specialization

## 虚继承
