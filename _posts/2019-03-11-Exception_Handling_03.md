---
title: "C++에서의 예외처리 (3/3)"
date: 2019-03-11 23:00:00 -0800
categories: C++ Exception Exception_Handling
---

# 클래스와 예외처리

### 예외 상황을 표현하는 예외 클래스

지금까지 int, char형 등의 기본적인 자료형을 통해 예외처리를 했었다.
ex) try {... throw 3; ...} catch(int expn) {...}
하지만 이렇게 보면 어떠한 유형의 문제가 발생한지는 파악하기 힘들다. 이렇게 충분히 명시해두지 않는다면,
코드 분석뿐만 아니라 예외처리 다양화에도 한계가 온다. 

이러한 한계를 타파하기 위해 객체를 throw하고, class형으로 인자를 받는 식으로 해서 예외 상황을 해결할 수 있다.
객체는 하나의 오브젝트이기 때문에, 다수의 데이터나 행동(메소드)를 넣을 수 있는데, 이를 예외 처리에 활용하면 
어떠한 상황에서 예외가 throw되는가를 좀 더 자세하게 파악할 수 있다.

그러나 그 객체에 예외 상황을 넣기보단 예외의 모델이 될 예외 클래스를 만들고 예외 객체를 만들어서 throw하게 디자인 하는 편이 더 나은 편이다.
(물론 무조건적 정답은 아니다!!!)

이 쯤 정리
예외 객체 : 예외 상황을 알리는데 사용되는 객체
예외 클래스 : 예외 객체의 생성을 위해 정의된 클래스
객체를 이용해서 예외 상황을 알리면 예외가 발생한 원인에 대한 정보를 보다 자세히 담을 수 있다.

	// 입금 관련 예외 상황을 표현하기 위한 클래스
	class DepositException
	{
	private : 
		int reqDep; // 요청 입금액

	public : 
		DepositException(int iMoney) : reqDep(iMoney) {}
		void ShowExceptionReason() { std::cout << "예외 메세지! : "<< reqDep  << "는 입금 불가!" << std::endl; }
	}

	// 출금 관련 예외 상황을 표현하기 위한 클래스
	class WithdrawException
	{
	private : 
		int balance; // 잔고

	public : 
		DepositException(int iMoney) : balance(iMoney) {}
		void ShowExceptionReason() { std::cout << "예외 메세지! : 잔액 부족!" << std::endl; }
	}

위의 코드처럼 예외 클래스는 간결한 편이 좋다. 그 이유는 그만큼 예외 처리의 과정이 복잡하게 되기 때문이다.

그럼 이 예외 클래스 기반의 예외 처리는 어떻게 쓰는가를 보이겠다.

	// 계좌 정보가 담긴 Account라는 클래스 선언
	class Account
	{
	private : 
		int iBalance;

	public : 
		Account(int iMoney) : iBalance(iMoney) {}
		void Deposit(int iMoney) throw(DepositException)
		{
			//예외 상황 발생 시
			if(iMoney < 0)
			{
				DepositException expn(iMoney);
				throw expn;
 			}
			iBalance += iMoney;
		}

		void Withdraw(int iMoney) throw(WithdrawException)
		{
			//예외 상황 발생 시
			if(iMoney > iBalance)
			{
				WithdrawException expn(iBalance);
				throw expn;
			}
			iBalance -= iMoney;
		}
		void ShowMyMoney() const
		{
			std::cout << "잔고 : " << iBalance << std::endl << std::endl;
		}
	}

	int main()
	{
		Account myAccount(5000);

		try
		{
			myAccount.Deposit(2000);
			//예외 상황 발생
			myAccount.Deposit(-300);
		}
		// &가 들어가는 이유는 굳이 복사할 필요가 없기에 참조의 형태로 받는다.
		catch(DepositException &expn) 
		{
			expn.ShowExceptionReason();
		}
		myAccount.ShowMyMoney();
		try
		{
			myAccount.Withdraw(3500);
			//예외 상황 발생
			myAccount.Withdraw(4500);
		}
		catch(WithdrawException &expn)
		{
			expn.ShowExceptionReason();
		}
		myAccount.ShowMyMoney();

		return 0;
	}

또한 예외 클래스 또한 상속이 가능하다. 
즉 위에서 선보인 예외 클래스를 아래 코드처럼 선언하는 것 또한 가능하다는 것이다.

	class AccountException
	{
	public :
		// 순수 가상함수 선언
		virtual void ShowExceptionReason() = 0;
	}
	
	// 입금 관련 예외 상황을 표현하기 위한 클래스
	class DepositException : public AccountException
	{
	private : 
		int reqDep; // 요청 입금액

	public : 
		DepositException(int iMoney) : reqDep(iMoney) {}
		void ShowExceptionReason() { std::cout << "예외 메세지! : "<< reqDep  << "는 입금 불가!" << std::endl; }
	}

	// 출금 관련 예외 상황을 표현하기 위한 클래스
	class WithdrawException : public AccountException
	{
	private : 
		int balance; // 잔고

	public : 
		DepositException(int iMoney) : balance(iMoney) {}
		void ShowExceptionReason() { std::cout << "예외 메세지! : 잔액 부족!" << std::endl; }
	}


### 에외의 전달 방식에 따른 주의 사항

다중 catch블록이 있는 예외처리 코드의 경우 try블록에서 예외가 발생했다면 catch문을 타고 내려오면서 맞는 예외 상황을 찾다가
없다면 다른 영역으로 전달하는 방식으로 코드가 진행된다. 
하지만 예외 클래스인 AAA라는 클래스를 BBB라는 클래스가 BBB라는 예외 클래스를 CCC라는 예외 클래스가 상속을 받을 때,
처음 catch문을 AAA형 예외 클래스로 둔다면 (예외 클래스 안에 정의된)어떤 예외가 발생하던 AAA형 클래스가 처리를 하기에,
원하지 않는 예외 처리가 발생할 수 있다. 그렇기에  제일 아래 상속받은 클래스부터 위에 선언해서 예외를 처리해야 한다.

### 예외 처리의 또 다른 특성들

지금까지는 예외를 직접 정의를 해서 throw하는 코드를 작성했었다. 그 이유는 프로그램의 사용 과정에서 발생하는 것이기 때문인 점과
문법적인 오류가 아닌 논리적인 오류에서 발생하는 코드이기 때문에 직접 발생시키는 것이 많다. 

하지만 자연적으로 발생하는 예외도 있다. 다음과 같은 코드가 있다고 가정하자.

	int main()
	{
		int num = 0;

		try
		{
			while(1)
			{
				num++;
				std::cout << num << "번째 할당 시도" << std::endl;
				new int[10000][10000];
			}
		}
		catch(bad_alloc &bad)
		{
			std::cout << bad.what() << std::endl;
			std::cout << "더 이상 할당 불가!" << std::endl;
		}

		return 0;
	}

bad_alloc은 할당에 실패한 경우 발생되는 예외이고, 이러한 예외는 여러 개가 있다.
단 이러한 예외는 직접 처리하는 예외가 아닌데 이러한 이유는 만약 메모리 할당이 실패한 경우,
이는 메모리 정리 과정에서 메모리가 부족할 시 발생하는데 이런 경우를 프로그래머가 직접 어떻게 할 수 있는,
논리적인 오류가 아니며, catch로 어떻게 처리할 수 있는 상황도 아니기에 프로그래머가 직접 정의하는 편은 아니다.

단 이런 경우를 아예 안 쓰는 것은 아닌게 만약 코드 작성시 어떤 에러가 발생했을 경우 이 에러가 의심가는 부분에
자연적 예외처리를 실시해서 프로그래머가 코드를 좀 더 수월하게 고칠 수 있게 해준다.

즉 남발하는 것은 좋지 않지만 의심갈 때 사용해 확인하면 유용하게 쓸 수 있는 코드 스타일이다.

### 예외 비우기와 예외 던지기

만약 catch() 이런 식으로 파라미터를 비워놓으면 전달되는 모든 예외를 다 받겠다라는 선언이다.
하지만, 모든 예외를 받을 수 있을진 모르나 참조한 것은 아니기에 catch문 안에서 try에서 던져지는 어떠한
예외를 사용할 수 없다.

또 catch문 안에서 전달된 예외는 다시 던질 수 있다.
그리고 이로 인해서 하나의 예외가 둘 이상의 catch 블록에서 처리되기 할 수 있다.

예로 들어 

	void Divide(int iParamNum1, int iParamNum2)
	{
		try
		{
			if (iParamNum2 == 0)
				throw iParamNum2;

			std::cout << "나눗셈의 몫 : " << iParamNum2 / iParamNum2 << std::endl;
			std::cout << "나눗셈의 나머지 : " << iParamNum1 % iParamNum2 << std::endl;
		}

		catch (int expn)
		{
			std::cout << "first catch" << std::endl;
			throw; // 예외 다시 던지기
		}
	}

	int main()
	{
		try
		{
			Divide(9, 2);
			Divide(5, 0);
		}
		catch (int expn)
		{
			std::cout << "second catch" << std::endl;
		}
		std::cout << "end of main" << std::endl;

		return 0;
	}

같은 코드에서 Divide(9, 2)는 정상 실행된 다음에 Divide(5, 0)로 인해 첫번째 catch문인 Divide 함수 속
catch로 갔다가 다시 그 상태로 main문 안에 있는 catch로 가는 것을 확인할 수 있다.
