---
title: SmartPtr实现
date: 2020-03-06 22:16:41
tags:
---

## SmartPtr 简单实现


```

#include <iostream>
#include <memory>

using namespace std;

template <typename T> 
class SmartPtr {
private:
    T* _ptr;
    size_t* _count;
public:
    //  成员函数
	//  普通构造函数  初始化计数类
	SmartPtr(T *p = nullptr):_ptr(p){
        if(_ptr){
            _count = new size_t(1);
        } else {
            _count = new size_t(0);
        }
    }
    //  拷贝构造函数
    SmartPtr(const SmartPtr &other){
        if(this == &other)  return;
        this->_ptr = other._ptr;
        this->_count = other._count;
        (*this->_count)++;
    }
    //  赋值运算符重载函数
    SmartPtr &operator=(const SmartPtr &rhs){
        if(this == &rhs)    return *this;
        if(this->_ptr){
            (*this->_count)--;
            if(*this->_count == 0){
                delete this->_count;
                delete this->_ptr;
            }
        }
        this->_ptr = rhs._ptr;
        this->_count = rhs._count;
        (*this->_count)++;
        return *this;
    }
    //  解引用运算符重载函数
    T &operator*(){
        assert(this->_ptr != nullptr);
        return *(this->_ptr);
    }
    // ->运算符重载函数
    T* operator->(){
        assert(this->_ptr != nullptr);
        return this->_ptr;
    }
    //返回引用数量
    size_t use_count(){
        return *(this->_count);
    }
    //  析构函数
    ~SmartPtr(){
        (*this->_count)--;
        if (*this->_count == 0) {
            delete this->_ptr;
            delete this->_count;
        }
    }
};

class A{
public:
    A(int x,int y):a(x),b(y){};
    int a;
    int b;
};


int main(){
    SmartPtr<A> sp(new A(1,2));
    //sp2 = sp3;
    std::cout << sp.use_count() << std::endl;
    {
        SmartPtr<A> sp2(sp);
        std::cout << sp.use_count() << std::endl;
        {
            SmartPtr<A> sp3(sp);
            std::cout << sp.use_count() << std::endl;
            {
                SmartPtr<A> sp4;
                sp4 = sp;
                std::cout << sp.use_count() << std::endl;
            }
            std::cout << sp.use_count() << std::endl;
        }
        std::cout << sp.use_count() << std::endl;
    }
    std::cout << sp.use_count() << std::endl;
    (*sp).a = 100;
    sp->b = 102;
    cout<<"a:"<< (*sp).a <<" b:"<< (*sp).b<<endl;
    return 0;
}
```