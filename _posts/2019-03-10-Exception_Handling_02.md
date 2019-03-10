---
title: "C++에서의 예외처리 (2/3)"
date: 2019-03-10 11:40:00 -0800
categories: C++ Exception Exception_Handling
---

#### 스택 풀기

기본적으로 예외는 처리되지 않으면 catch로 던져진다고 생각하면 된다. 그렇다면 만약 try문 안에 함수가 있고 그 함수 내에서 예외상황이 발생하면
어떻게 될까?

다음과 같은 코드를 예로 들겠다.

    void Divide(int iParamNum1, int iParamNum2)
    {
     if (iParamNum2 == 0)
       throw iParamNum2;
  
     std::cout << "나눗셈의 몫 : " << iParamNum2 / iParamNum2 << std::endl;
     std::cout << "나눗셈의 나머지 : " << iParamNum1 % iParamNum2 << std::endl;
    }
  
     int main()
     {
     int iNum1, iNum2;
     std::cout << "두 개의 숫자를 입력하시오 : ";
      std::cin >> iNum1 >> iNum2;
  
      try
      {
       Divide(iNum1, iNum2);
      std::cout << "나눗셈을 마쳤습니다." << std::endl;
        }
      catch (int expn)
      {
        std::cout << "제수는 " << expn << "이 될 수 없습니다." << std::endl;
       std::cout << "프로그램을 다시 실행하십시오." << std::endl;
     }
      std::cout << "end of main" << std::endl;
  
      return 0;
  	 }
 
예외가 Divide 함수 내에서 발생한다치자. 하지만 발생한 지점, 즉 함수 내에 throw 절을 감싸는 try 블록이 존재하지 않는다.
하지만 그렇다고 해서 함수 내에서 예외처리가 발생하지 않을까? 아니다 메모리 구조상 함수는 스택 메모리에 쌓이고 그 스택 내에 main 그 다음에 
호출한 함수 이렇게 쌓이기 때문에 만약 이런 예외처리가 발생했을 때 그 함수 내에 try~catch문이 없다면 있을 때 까지 메모리를 타고 내려가 
처리를 한다. 

하지만 여러 개의 함수 즉 main함수 위에 inputInt(), Divide() 이런 식으로 되어있다 치자.

    void Divide(int iParamNum1, int iParamNum2)
    {
      if (iParamNum2 == 0)
        throw iParamNum2;
  
      std::cout << "나눗셈의 몫 : " << iParamNum2 / iParamNum2 << std::endl;
     std::cout << "나눗셈의 나머지 : " << iParamNum1 % iParamNum2 << std::endl;
  	 }
  
    void InputInt()
     {
         int iNum1, iNum2;
         std::cout << "두 개의 숫자를 입력하시오 : ";
        std::cin >> iNum1 >> iNum2;
  
     Divide(iNum1, iNum2);
      std::cout<< "실행 완료" << std::endl;
	   }
  
     int main()
     {
        try
       {
          InputInt();
          std::cout << "나눗셈을 마쳤습니다." << std::endl;
       }
        catch (int expn)
       {
         std::cout << "제수는 " << expn << "이 될 수 없습니다." << std::endl;
         std::cout << "프로그램을 다시 실행하십시오." << std::endl;
       }
       std::cout << "end of main" << std::endl;
    
       return 0;
  	   }
  
이런 경우 만약 8 0 (예외가 발생될 상황) 을 입력했다고 가정하자. 그럼 InputInt의 나머지 부분을 거치지 않고 main으로 간다.

그럼 코드가 제대로 실행이 안 된 것 아닌가란 의문을 제기할 수 있지만 스택 메모리는 InputInt를 호출하기 전 상태로 잘 되돌아온다.
그 이유는 예외 데이터가 나온 함수는 그 시점에서 메모리에서 pop-up되고 그 전 함수가 예외를 처리하지 못한다면 그 함수도 종료되며 pop-up되고 
try-catch문이 있는 함수까지 종료되다가 main함수의 try-catch문에서 처리 되기 때문이다.
(만약 try-catch문이 main함수에도 없다면 terminate함수(프로그램을 종료시키는 함수)가 호출되면서 프로그램이 종료가 된다.)

#### 자료형이 일치하지 않는 예외 데이터 

만약 다음과 같은 코드가 있다 가정하자.

    int SimpleFunc(void)
    {
	    // 생략
  	  try
  	  {
	  	  if (...)
	  	  	throw - 1; // int형 예외 데이터
	    }
  	  catch (char expn) { ... } // char형 예외 데이터를 전달해달라.
   }

이런 경우에는 catch로 처리되지 않고 SimpleFunc함수를 호출한 영역으로 전달을 한다.
그래서 이런 경우 이런 것을 어떻게 처리되는가? try~catch문은 하나의 자료형의 예외처리만을 받지 않는다. 즉 다양한 자료형의 예외처리가 가능한데,
이는 아래의 코드처럼 쓸 수 있다.

    try
    {
      if(...)
        throw -1; // int형 예외 데이터
    }
    catch(char ch) {...} // char형 예외 데이터를 전달해달라.
    catch(int expn) {...} // int형 예외 데이터를 전달해달라.
    
#### 전달되는 예외의 명시

프로그래머는 함수 내에서 예외 상황이 어떻게 발생할까를 미리 선언을 해둘 수 있다.

    int ThrowFunc(int iNum) throw(int, char)
    {
      
    }
  
이런 식으로 선언하여 int형과 char형의 예외 데이터가 전달될 수 있음을 미리 명시할 수 있다는 것이다.
그리고 내부에는 위에서 언급했던 방식의 예외처리를 하면 된다.

    int ThrowFunc(int iNum) throw(int, char)
    {
        try
      {
        if(...)
          throw -1; // int형 예외 데이터
      }
      catch(char ch) {...} // char형 예외 데이터를 전달해달라.
      catch(int expn) {...} // int형 예외 데이터를 전달해달라.
    }
  
이 때 int, char형 이외의 데이터가 들어온다면, 대비하지 못한 예외 상황에 대한 처리로 terminate 함수가 호출이 되어 프로그램은 종료된다.
우리가 어떠한 예외도 원하지 않는다면 throw() 이런 식으로 선언하여 예외 상황 발생시 프로그램을 종료하게 된다.
